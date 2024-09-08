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

# Análise de dados de queimadas no Brasil

Esse notebook analisa e mostra no mapa dados sobre queimadas no Brasil.

Vamos usar os dados do INPE, recuperados em https://terrabrasilis.dpi.inpe.br/queimadas/bdqueimadas/

Os dados incluidos neste tutorial são referentes ao período de 01/07 até 01/09 de 2024.

A NASA também fornece dados de queimadas: https://firms.modaps.eosdis.nasa.gov/

```{code-cell} ipython3
!ls
```

```{code-cell} ipython3
zipfile_inpe = "dados/Focos_BDQueimadas.zip"
```

```{code-cell} ipython3
from zipfile import ZipFile
```

```{code-cell} ipython3
with ZipFile(zipfile_inpe, 'r') as zip: 
    zip.printdir() 
    print(f'Extracting file {zipfile_inpe} now...') 
    zip.extractall(path="dados") 
    print('Done!')
```

```{code-cell} ipython3
!ls dados
```

```{code-cell} ipython3
import os
csv_inpe = os.path.join("dados", "focos_qmd_inpe_2024-07-01_2024-09-01_58.414406.csv")
```

```{code-cell} ipython3
with open(csv_inpe, 'r') as f:
    data = f.readlines()
```

```{code-cell} ipython3
print(data[0:10])
```

## Pandas

+++

Como nossos dados estão em formato de tabela, vamos usar a biblioteca [pandas](https://pydata.pandas.dev) para analisar os dados. O pandas fornece um tipo de dados chamado `DataFrame`, que permite tratar com facilidade dados em tabelas.

```{code-cell} ipython3
import pandas as pd
```

```{code-cell} ipython3
with open(csv_inpe, 'r') as f:
    df = pd.read_csv(f)
```

```{code-cell} ipython3
df
```

(repare no número de linhas dessa tabela!)

+++

Como queremos analisar dados de queimadas (representadas pelas linhas em que o valor de `RiscoFogo` é não-nulo) vamos eliminar as linhas que não nos interessam da tabela.

Aqui, temos dois tipos de dados "nulos":
- Linhas em que o valor de `RiscoFogo` é NaN (correspondente, provavelmente, a dados faltantes);
- Linhas em que o valor de `RiscoFogo` é zero.

Vamos eliminar ambos.

```{code-cell} ipython3
len(df[pd.isnull(df['RiscoFogo'])])
```

```{code-cell} ipython3
pd.isnull(df['RiscoFogo'])
```

Para eliminarmos essas linhas, vamos usar um filtro booleano e criar uma nova tabela com apenas os valores em que estamos interessados.

```{code-cell} ipython3
df = df[~pd.isnull(df['RiscoFogo'])]
```

```{code-cell} ipython3
df
```

```{code-cell} ipython3
df[df['RiscoFogo']==0]
```

```{code-cell} ipython3
df = df[df['RiscoFogo']!=0]
```

```{code-cell} ipython3
df
```

Agora, vamos filtrar por satélite:

```{code-cell} ipython3
df['Satelite'].unique()
```

```{code-cell} ipython3
df = df[df['Satelite']=='TERRA_M-M']
```

Como todos os nossos dados são referentes ao Brasil e eliminamos os dados de outros satélites, podemos eliminar as colunas `Pais` e `Satelite` da tabela.

```{code-cell} ipython3
del df['Satelite']
del df['Pais']
```

```{code-cell} ipython3
df
```

Note os índices da tabela; eles ainda retém as informações da tabela original. Para resetarmos os índices, caso isso não seja necessário, usamos:

```{code-cell} ipython3
df.reset_index()
```

e, para eliminar totalmente os índices antigos, usamos

```{code-cell} ipython3
df = df.reset_index(drop=True)
```

```{code-cell} ipython3
df
```

**Nota:** Para saber mais sobre os valores dessa tabela, inclusive sobre como é calculado e o que significa o valor de `RiscoFogo` veja as referências abaixo:

- FRP: https://revistapesquisa.fapesp.br/como-monitorar-o-fogo/
- Risco de Queima: http://queimadas.dgi.inpe.br/queimadas/portal/informacoes/perguntas-frequentes#p23
- Monografia: https://monografias.ufrn.br/jspui/bitstream/123456789/9704/1/tcc_dias_alexandre_henrique.pdf

+++

---

+++

## Gráfico no mapa

+++

Para fazermos não só um gráfico dos dados, mas sobrepor esses dados em um mapa, vamos usar a biblioteca `ipyleaflet`:

```{code-cell} ipython3
# !pip install ipyleaflet
```

Para começarmos, vamos criar um mapa e adicionar uma camada de marcadores que serão usados mais tarde para sinalizar as coordenadas com RiscoFogo positivo.

```{code-cell} ipython3
%matplotlib widget
from ipyleaflet import Map, CircleMarker

center = (-11.7997134,-53.8335376)

m = Map(center=center, zoom=3)
display(m)
```

```{code-cell} ipython3
frp_notnull = df[df['FRP'].notnull()]
frp_notnull = frp_notnull.loc[frp_notnull['DataHora'].str.contains('2024/08/30')]
```

```{code-cell} ipython3
for index, row in frp_notnull.iterrows():
    lat = row['Latitude']
    lon = row['Longitude']
    circle_marker = CircleMarker()
    circle_marker.location = (lat, lon)
    circle_marker.radius = 1
    circle_marker.color = "red"
    circle_marker.fill_color = "red"
    circle_marker.weight = 1
    m.add_layer(circle_marker)
```

**Nota** Com uma instância regular do JupyterLab, você pode observar o seguinte erro ao executar o comando acima:

```
IOPub message rate exceeded.
The Jupyter server will temporarily stop sending output
to the client in order to avoid crashing it.
To change this limit, set the config variable
`--ServerApp.iopub_msg_rate_limit`.

Current values:
ServerApp.iopub_msg_rate_limit=1000.0 (msgs/sec)
ServerApp.rate_limit_window=3.0 (secs)
```

Para resolver isso, você deve iniciar a instância do JupyterLab com o comando
```
jupyter lab --ServerApp.iopub_msg_rate_limit=10000
```
ou criar um arquivo de configurações usando o comando
```
jupyter notebook --generate-config
```
e alterar o valor dessa configuração no arquivo.

+++

- para uma mesma cidade, pegar o risco em função do tempo
- para um grupo de cidades plotar o risco em um mesmo gráfico

```{code-cell} ipython3
lista_municipios = df['Municipio'].unique()
type(lista_municipios), len(lista_municipios)
```

```{code-cell} ipython3
pv = df[df['Municipio'] == "PORTO VELHO"]
pv
```

## O objeto pd.Series

```{code-cell} ipython3
riscofogo = pv['RiscoFogo']
diasemchuva = pv['DiaSemChuva']
```

```{code-cell} ipython3
type(diasemchuva)
```

```{code-cell} ipython3
diasemchuva
```

O objeto `Series` é uma array unidimensional com rótulos, capaz de conter qualquer tipo de dados:

```{code-cell} ipython3
diasemchuva[0]
```

```{code-cell} ipython3
diasemchuva[332]
```

```{code-cell} ipython3
riscofogo.mean()
```

```{code-cell} ipython3
diasemchuva.max()
```

```{code-cell} ipython3
pv[pv['DiaSemChuva'] == 88.0]
```

---

+++

## Risco de fogo em função do tempo para Porto Velho, RO

```{code-cell} ipython3
pv = pv[['DataHora', 'RiscoFogo', 'DiaSemChuva']].reset_index(drop=True)
```

```{code-cell} ipython3
pv
```

Para cada grupo de entradas com o mesmo valor de `DataHora`, calcule a média dos valores de `RiscoFogo` e `DiaSemChuva`.

```{code-cell} ipython3
agrupado = pv.groupby(['DataHora'], as_index=False)[["RiscoFogo", "DiaSemChuva"]].mean()
```

```{code-cell} ipython3
agrupado
```

```{code-cell} ipython3
pv[pv['DataHora']=='2024/08/22 01:52:00']
```

Surpresa! 🤡

```{code-cell} ipython3
pv = pv[pv['RiscoFogo']!=-999.0]
```

```{code-cell} ipython3
agrupado = pv.groupby(['DataHora'])[["RiscoFogo", "DiaSemChuva"]].mean()
agrupado
```

Veja https://pandas.pydata.org/docs/user_guide/groupby.html e https://pandas.pydata.org/docs/user_guide/10min.html#grouping.

```{code-cell} ipython3
datas = list(agrupado.index)
```

```{code-cell} ipython3
datas = [item[0:10] for item in datas]
datas
len(datas)
```

```{code-cell} ipython3
import matplotlib.pyplot as plt

fig, ax = plt.subplots(2, 1, figsize=(8, 6))
ax[0].plot(agrupado['RiscoFogo'], 'r')
ax[1].plot(agrupado['DiaSemChuva'], 'bo')

# Preparar rótulos da primeira imagem
ax[0].set_xticks(range(len(datas))[::10]);
ax[0].set_xticklabels(datas[::10], rotation=30)

# Preparar rótulos da segunda imagem
ax[1].set_xticks(range(len(datas))[::10]);
ax[1].set_xticklabels(datas[::10], rotation=30)

fig.tight_layout()
```

```{code-cell} ipython3
fig, ax = plt.subplots(figsize=(8, 4))

ax.plot(agrupado['RiscoFogo'], 'r')
plt.draw()

ax2 = ax.twinx()  # instantiate a second axes that shares the same x-axis
ax2.plot(agrupado['DiaSemChuva'], 'bo')

ax2.set_xticks(range(len(datas))[::10]);
ax.set_xticklabels(datas[::10], rotation=30);

ax.legend(['Risco Fogo'], loc='upper left')
ax2.legend(['Dias sem chuva'], loc='upper right')

ax.set_title('Dados INPE sobre queimadas em Corumba, MS')
ax.set_ylabel('Risco fogo')
ax2.set_ylabel('Dias sem chuva')
fig.tight_layout()
```
