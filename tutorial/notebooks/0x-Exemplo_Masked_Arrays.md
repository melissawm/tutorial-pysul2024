---
jupytext:
  formats: md:myst
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.16.4
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

# Exemplo: Arrays com Máscara

Vamos usar arrays com máscara para analisar dados do COVID-19 e lidar com valores ausentes.


***


## O que são arrays com máscara?

Considere o seguinte problema. Você tem um conjunto de dados com entradas inválidas. Se você quiser *pular* ou marcar esses valores indesejados sem apagar dados, você pode usar o [numpy.ma](https://numpy.org/devdocs/reference/maskedarray.generic.html#module-numpy.ma).

Da documentação de [Referência](https://numpy.org/devdocs/reference/maskedarray.generic.html#module-numpy.ma):

> A masked array is the combination of a standard [numpy.ndarray](https://numpy.org/devdocs/reference/generated/numpy.ndarray.html#numpy.ndarray) and a **mask**. A mask is either `nomask`, indicating that no value of the associated array is invalid, or an array of booleans that determines for each element of the associated array whether the value is valid or not. When an element of the mask is `False`, the corresponding element of the associated array is valid and is said to be unmasked. When an element of the mask is `True`, the corresponding element of the associated array is said to be masked (invalid).

Podemos pensar numa [array com máscara](https://numpy.org/devdocs/reference/maskedarray.baseclass.html#numpy.ma.MaskedArray) como uma combinação de:

- Dados, numa `numpy.ndarray` com shape e dtype quaisquer;
- Uma máscara booleana com o mesmo shape dos dados;
- Um valor de preenchimento `fill_value`, que vai substituir as entradas inválidas. 

## Quando podem ser úteis?

Existem algumas situações em que arrays com máscara podem ser úteis:

- Quando você quer preservar os valores que foram mascarados sem copiar a array;
- Quando você tem que lidar com muitas arrays, cada uma com sua máscara. Se a máscara é parte da array, você pode evitar bugs e o código pode ficar mais compacto;
- Quando você tem flags diferentes para valores inválidos e ausentes, e quer preservar as flags sem substitui-las, excluindo-as dos cálculos;
- Se você não puder evitar ou eliminar valores ausentes, mas não quer lidar com [NaN (Not a Number)](https://numpy.org/devdocs/reference/constants.html#numpy.nan).

O módulo `numpy.ma` também vem com uma implementação específica da maior parte das [NumPy universal functions (ufuncs)](https://numpy.org/devdocs/glossary.html#term-ufunc), o que permite usar operações e funções vetorizadas (rápidas). 


## Usando arrays com máscara para ver dados do COVID-19

Do [Kaggle](https://www.kaggle.com/atilamadai/covid19) a gente pode baixar um conjunto de dados com informações sobre o início da pandemia. Vamos olhar para um subconjunto de dados, contidos no arquivo `who_covid_19_sit_rep_time_series.csv`.

```{code-cell} ipython3
import numpy as np
import os
# A função os.getcwd() retorna o diretório atual
filepath = os.getcwd()
filename = os.path.join(filepath, "dados", "who_covid_19_sit_rep_time_series.csv")
```

O arquivo tem dados de diferentes tipos organizado assim:

- A primeira coluna é um cabeçalho, que descreve os dados em cada coluna. A partir da quarta coluna, denota a data da coleta dos dados correspondentes.
- Da segunda até a sétima coluna, temos um resumo dos dados que será excluido da nossa análise.
- Os dados numéricos em que estamos interessados começam na coluna 4, linha 8.

Vamos explorar os dados deste arquivo para os primeiros 14 dias de registros. Para coletarmos os dados do arquivo `.csv`, vamos usar a função [numpy.genfromtxt](https://numpy.org/devdocs/reference/generated/numpy.genfromtxt.html#numpy.genfromtxt).

```{code-cell} ipython3
# Vamos usar skip_header e usecols para ler apenas um pedaço do arquivo.
dates = np.genfromtxt(filename, dtype=np.str_, delimiter=",",
                      max_rows=1, usecols=range(3, 17),
                      encoding="utf-8-sig")

locations = np.genfromtxt(filename, dtype=np.str_, delimiter=",",
                          skip_header=7, usecols=(0, 1),
                          encoding="utf-8-sig")

nbcases = np.genfromtxt(filename, dtype=np.int_, delimiter=",",
                        skip_header=7, usecols=range(3, 17),
                        encoding="utf-8-sig")
```

Na chamada de `numpy.genfromtxt`, também selecionamos o [numpy.dtype](https://numpy.org/devdocs/reference/generated/numpy.dtype.html#numpy.dtype) para cada subconjunto dos dados (um inteiro - `numpy.int_` - ou texto - `numpy.unicode_`). Também usamos o argumento `encoding` para selecionar `utf-8-sig` como a [codificação do arquivo](https://docs.python.org/3/library/codecs.html#encodings-and-unicode).


## Explorando os dados

Vamos selecionar apenas algumas datas para mostrar no nosso gráfico (os [x-axis ticks](https://matplotlib.org/api/_as_gen/matplotlib.pyplot.xticks.html#matplotlib.pyplot.xticks)). Usamos `nbcases.T` para plotar cada linha do arquivo como uma curva do gráfico.

```{code-cell} ipython3
%matplotlib widget
import matplotlib.pyplot as plt
selected_dates = [0, 3, 11, 13]
plt.plot(dates, nbcases.T, '--');
plt.xticks(selected_dates, dates[selected_dates]);
plt.title("COVID-19 cumulative cases from Jan 21 to Feb 3 2020");
```

O gráfico está esquisito entre 24 de Janeiro e 1 de Fevereiro. Olhando para a coluna `locations`, temos os nomes de regiões e países. Mas só as primeiras linhas tem dados na primeira coluna (nomes de província na China). Vamos então agrupar os dados da China numa única linha, usando [numpy.sum](https://numpy.org/devdocs/reference/generated/numpy.sum.html#numpy.sum) para somar todas a linhas selecionadas (`axis=0`):

```{code-cell} ipython3
china_total = nbcases[locations[:, 1] == 'China'].sum(axis=0)
china_total
```

Mas tem algo errado: não é pra termos dados negativos... 


## Dados ausentes

```{code-cell} ipython3
nbcases
```

Os `-1` são causados pela tentativa da `numpy.genfromtxt` de ler dados ausentes do arquivo `.csv` original.

```{code-cell} ipython3
from numpy import ma
nbcases_ma = ma.masked_values(nbcases, -1)
```

Agora:

```{code-cell} ipython3
nbcases_ma
```

Cuidado: o atributo `mask` tem valor `True` em elementos com valores **inválidos**.


Vamos excluir a primeira linha (dados da província de Hubei na China):

```{code-cell} ipython3
plt.plot(dates, nbcases_ma[1:].T, '--');
plt.xticks(selected_dates, dates[selected_dates]);
plt.title("COVID-19 cumulative cases from Jan 21 to Feb 3 2020");
```

```{code-cell} ipython3
china_masked = nbcases_ma[locations[:, 1] == 'China'].sum(axis=0)
china_masked
```

Note que `china_masked` é uma array com máscara e para acessar seus dados precisamos usar o atributo `.data`:

```{code-cell} ipython3
china_total = china_masked.data
china_total
```

Pronto, não temos mais valores negativos. Mas ainda tem algo estranho: mas o número acumulado de casos não poderia diminuir de um dia pro outro (de 835 pra 10, por exemplo). Olhando pros dados, vemos que no período em que tínhamos dados ausentes pra China, tínhamos dados válidos pra Hong Kong, Taiwan, Macau e regiões "Unspecified".

```{code-cell} ipython3
china_mask = ((locations[:, 1] == 'China') &
              (locations[:, 0] != 'Hong Kong') &
              (locations[:, 0] != 'Taiwan') &
              (locations[:, 0] != 'Macau') &
              (locations[:, 0] != 'Unspecified*'))
```

Observe que `china_mask` é um array de valores booleanos (`True` ou `False`); podemos verificar que os índices são como queríamos usando o método [ma.nonzero](https://numpy.org/devdocs/reference/generated/numpy.ma.nonzero.html#numpy.ma.nonzero):

```{code-cell} ipython3
china_mask.nonzero()
```

Pronto:

```{code-cell} ipython3
china_total = nbcases_ma[china_mask].sum(axis=0)
china_total
```

Substituindo esses dados, temos:

```{code-cell} ipython3
fig = plt.figure()
plt.plot(dates, china_total.T, '--');
plt.xticks(selected_dates, dates[selected_dates]);
plt.title("COVID-19 cumulative cases from Jan 21 to Feb 3 2020 - Mainland China");
```

## Ajuste de dados

Uma possibilidade seria interpolar os dados ausentes para estimar o número de casos no final de janeiro.

```{code-cell} ipython3
china_total.mask
invalid = china_total[china_total.mask]
invalid
```

Podemos acessar os valores válidos usando o operador de negação:

```{code-cell} ipython3
valid = china_total[~china_total.mask]
valid
```

Agora, para criar uma aproximação simples para estes dados, devemos levar em conta as entradas válidas perto das inválidas. Vamos selecionar as datas em que os dados são válidos; observe que podemos usar a máscara do array `china_total` também no array de datas:

```{code-cell} ipython3
dates[~china_total.mask]
```

Finalmente, vamos usar [numpy.polyfit](https://numpy.org/devdocs/reference/generated/numpy.polyfit.html#numpy.polyfit) e [numpy.polyval](https://numpy.org/devdocs/reference/generated/numpy.polyval.html#numpy.polyval) para criar um polinômio cúbico que faz um ajuste dos dados:

```{code-cell} ipython3
t = np.arange(len(china_total))
params = np.polyfit(t[~china_total.mask], valid, 3)
cubic_fit = np.polyval(params, t)
fig = plt.figure()
plt.plot(t, china_total);
plt.plot(t, cubic_fit, '--');
```

Melhorando o gráfico:

```{code-cell} ipython3
plt.plot(t, china_total);
plt.plot(t[china_total.mask], cubic_fit[china_total.mask], '--', color='orange');
plt.plot(7, np.polyval(params, 7), 'r*');
plt.xticks([0, 7, 13], dates[[0, 7, 13]]);
plt.yticks([0, np.polyval(params, 7), 10000, 17500]);
plt.legend(['Mainland China', 'Cubic estimate', '7 days after start']);
plt.title("COVID-19 cumulative cases from Jan 21 to Feb 3 2020 - Mainland China\n"
          "Cubic estimate for 7 days after start");
```
