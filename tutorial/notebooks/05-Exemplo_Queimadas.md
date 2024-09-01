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

Dados do INPE: https://terrabrasilis.dpi.inpe.br/queimadas/bdqueimadas/

Dados da NASA: https://firms.modaps.eosdis.nasa.gov/

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
csv_inpe = os.path.join("dados", "focos_qmd_inpe_2024-07-01_2024-09-01_12.910553.csv")
```

```{code-cell} ipython3
with open(csv_inpe, 'r') as f:
    data = f.readlines()
```

```{code-cell} ipython3
print(data[0:10])
```

## Pandas

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

```{code-cell} ipython3
pd.isnull(df['RiscoFogo'])
```

```{code-cell} ipython3
df = df[~pd.isnull(df['RiscoFogo'])]
```

```{code-cell} ipython3
df = df[df['RiscoFogo']!=0]
```

```{code-cell} ipython3
df
```

```{code-cell} ipython3
df['Satelite'].unique()
```

```{code-cell} ipython3
#df = df[df['satelite']=='TERRA_M-M']
```

```{code-cell} ipython3
del df['Satelite']
del df['Pais']
```

```{code-cell} ipython3
df
```

FRP: https://revistapesquisa.fapesp.br/como-monitorar-o-fogo/

Risco de Queima: http://queimadas.dgi.inpe.br/queimadas/portal/informacoes/perguntas-frequentes#p23

Monografia: https://monografias.ufrn.br/jspui/bitstream/123456789/9704/1/tcc_dias_alexandre_henrique.pdf

```{code-cell} ipython3
# !pip install ipyleaflet
```

```{code-cell} ipython3
%matplotlib widget
from ipyleaflet import Map, Marker, CircleMarker

center = (-11.7997134,-53.8335376)

m = Map(center=center, zoom=3)

#marker = Marker(location=center, draggable=True)
#m.add_layer(marker);

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

```{code-cell} ipython3
riscofogo = pv['RiscoFogo']
diasemchuva = pv['DiaSemChuva']
```

```{code-cell} ipython3
import numpy as np
pv = pv.replace(-999, np.nan)
```

```{code-cell} ipython3
agrupado = pv.groupby('DataHora').mean()
```

```{code-cell} ipython3
agrupado
```

```{code-cell} ipython3
datas = list(agrupado.index)
datas = [item[0:10] for item in datas]
datas
len(datas)
```

```{code-cell} ipython3
import matplotlib.pyplot as plt

fig, ax = plt.subplots(2, 1, figsize=(8, 6))
ax[0].plot(agrupado['riscofogo'], 'r')
ax[1].plot(agrupado['diasemchuva'], 'bo')

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

ax.plot(agrupado['riscofogo'], 'r')
plt.draw()

ax2 = ax.twinx()  # instantiate a second axes that shares the same x-axis
ax2.plot(agrupado['diasemchuva'], 'bo')

ax2.set_xticks(range(len(datas))[::10]);
ax.set_xticklabels(datas[::10], rotation=30);

ax.legend(['Risco Fogo'], loc='upper left')
ax2.legend(['Dias sem chuva'], loc='upper right')

ax.set_title('Dados INPE sobre queimadas em Corumba, MS')
ax.set_ylabel('Risco fogo')
ax2.set_ylabel('Dias sem chuva')
fig.tight_layout()
```

[Voltar ao notebook principal](00-Tutorial_Python_Sul_2024.md)

[Ir para o notebook SciPy](06-Tutorial_SciPy.md)
