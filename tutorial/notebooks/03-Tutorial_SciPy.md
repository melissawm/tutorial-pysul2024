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

```{code-cell} ipython3
from IPython.display import Image
Image(url="https://scipy.org/images/logo.svg", width=200)
```

<!-- #region tags=["chapter"] -->
# SciPy: algoritmos para computação científica

A SciPy é um conjunto de bibliotecas para computação científica, incluindo:
- integração numérica
- interpolação
- processamento de sinais
- álgebra linear
- estatística
- otimização matemática
- tratamento de matrizes esparsas

Sua base é a NumPy.
<!-- #endregion -->

```{code-cell} ipython3
import scipy as sp
print(sp.__doc__)
```

## Alguns exemplos curtos

+++

### Exemplo: Minimização de funções

```{code-cell} ipython3
from scipy.optimize import fmin
```

```{code-cell} ipython3
func = lambda x : x**2
```

```{code-cell} ipython3
fmin(func, -1)
```

### Exemplo: integração numérica


Dada a equação diferencial ordinária


$$\frac{dy(t)}{dt} = -y(t) + 1$$
$$y(0) = 0$$


Encontre uma solução numérica para a equação diferencial com condição inicial associada. Expanda o horizonte temporal até encontrar uma solução estável.

```{code-cell} ipython3
%matplotlib widget
import numpy as np
from scipy.integrate import odeint
import matplotlib.pyplot as plt

# function that returns dy/dt
def model(y,t):
    dydt = -y + 1.0
    return dydt

# initial condition
y0 = 0

# time points
t = np.linspace(0,5)

# solve ODE
y = odeint(model,y0,t)

# plot results
fig = plt.figure()
plt.plot(t,y)
plt.xlabel('time')
plt.ylabel('y(t)')
plt.show()
```

### Exemplo: Regressão usando 3 métodos

```{code-cell} ipython3
import numpy as np
from scipy.interpolate import interp1d
import matplotlib.pyplot as plt

# Criação de dados
x = np.arange(10)
y = x + 5*np.random.rand(10) - 6*np.random.rand(10)

# Regressão Linear
(a_linear, b_linear) = np.polyfit(x, y, 1)
# Ajuste quadrático
(a_quad, b_quad, c_quad) = np.polyfit(x, y, 2)
# Interpolação
f = interp1d(x, y)

# Gráfico
t = np.linspace(0, 9, 50)
plt.title('Exemplo: ajuste de curvas')
plt.plot(x, y, 'r*')
plt.plot(t, a_linear*t+b_linear,'g')
plt.plot(t, a_quad*t**2+b_quad*t+c_quad, 'm')
plt.plot(t, f(t), 'b')
plt.legend(['linear', 'quadrático', 'interpolação'])
plt.show();
```

### Exemplo: Teste de hipótese estatística

+++

_(baseado na documentação oficial da SciPy: https://scipy.github.io/devdocs/tutorial/stats/hypothesis_chi2_contingency.html)_

Em estatística, é comum usarmos [testes de hipóteses](https://pt.wikipedia.org/wiki/Testes_de_hip%C3%B3teses) para verificar se uma dada observação tem significado estatístico (ou seja, não pode ser atribuida ao acaso).

No artigo https://doi.org/10.1001/jama.295.3.306, um grupo de pesquisadores analisou o uso de aspirina para a prevenção de eventos cardiovasculares. O estudo concluiu que

> ...aspirin therapy reduced the risk of a composite of cardiovascular events due to its effect on reducing the risk of ischemic stroke in women

O artigo examina os resultados de um experimento em que participantes tomaram aspirina ou placebo regularmente por vários anos. Os casos de [isquemia cerebral](https://pt.wikipedia.org/wiki/Isquemia_cerebral) foram registrados na tabela seguinte:

+++

|                   | Aspirina | Controle/Placebo |
|-------------------|----------|------------------|
| Isquemia cerebral |   176    |       230        |
| Sem isquemia      |  21035   |      21018       |

+++

Existem evidências de que a aspirina reduz o risco de isquemias cerebrais? Começamos formulando uma hipótese nula:

+++

> O efeito da aspirina é equivalente ao efeito do placebo.

+++

Vamos avaliar a validade (ou não) dessa hipótese com um [teste qui-quadrado](https://pt.wikipedia.org/wiki/Qui-quadrado) usando a tabela acima como entrada.

```{code-cell} ipython3
import numpy as np
from scipy.stats import chi2_contingency
table = np.array([[176, 230], [21035, 21018]])
res = chi2_contingency(table)
res.statistic
```

```{code-cell} ipython3
res.pvalue
```

Usando um nível de significância de 5%, neste ponto rejeitaríamos a hipótese nula em favor da hipótese alternativa: "o efeito da aspirina não é equivalente ao efeito do placebo". No entanto, o teste que usamos aqui não permite decidirmos a direção do efeito. Podemos usar [a função `scipy.stats.contingency.odds_ratio`](https://scipy.github.io/devdocs/tutorial/stats/hypothesis_odds_ratio.html) - ([em português, Razão de possibilidades](https://pt.wikipedia.org/wiki/Raz%C3%A3o_de_possibilidades)) para isso. A razão de possibilidades é definida como a razão entre a chance de um evento ocorrer em um grupo e a chance de ocorrer em outro grupo. No nosso caso:

```{code-cell} ipython3
from scipy.stats.contingency import odds_ratio

res = odds_ratio(table)
res
```

Para nossa amostra, a chance de observarmos uma isquemia cerebral entre as pessoas que tomam aspirina regularmente é cerca de 0.76 vezes a chance de observar esse evento entre as pessoas que receberam placebo.

Para fazermos inferências estatísticas sobre a população estudada, podemos calcular o [intervalo de confiança](https://pt.wikipedia.org/wiki/Intervalo_de_confian%C3%A7a) para essa razão de possibilidades. O nível de confiança é a frequência com a qual o intervalo observado contém o parâmetro real de interesse quando o experimento é repetido várias vezes. Se um teste de hipótese for realizado, o nível de confiança é o complemento do nível de significância. Logo, calculamos o intervalo de confiança de 95% para essa população:

```{code-cell} ipython3
res.confidence_interval(confidence_level=0.95)
```

Como o intervalo de confiança não contém o valor 1, existe suporte para a conclusão dos autores de que o uso regular de aspirina está associado com uma redução na incidência de isquemias cerebrais nessa população.
