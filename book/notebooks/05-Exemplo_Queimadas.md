---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.16.4
  kernelspec:
    display_name: Python 3
    language: python
    name: python3
---

# Queimadas


Dados do INPE: http://queimadas.dgi.inpe.br/queimadas/bdqueimadas/

Dados da NASA: https://firms.modaps.eosdis.nasa.gov/

```python
!ls
```

```python
zipfile_inpe = "Focos_BDQueimadas.zip"
```

```python
from zipfile import ZipFile
```

```python
with ZipFile(zipfile_inpe, 'r') as zip: 
    zip.printdir() 
    print(f'Extracting file {zipfile_inpe} now...') 
    zip.extractall(path="dados") 
    print('Done!')
```

```python
!ls dados
```

```python
import os
csv_inpe = os.path.join("dados", "Focos_2020-07-01_2020-09-30.csv")
```

```python
with open(csv_inpe, 'r') as f:
    data = f.readlines()
```

```python
print(data[0:10])
```

```python
import pandas as pd
```

```python
with open(csv_inpe, 'r') as f:
    df = pd.read_csv(f)
```

```python
df
```

```python
df = df[df['riscofogo']!=0.0]
```

```python
df['satelite'].unique()
```

```python
df = df[df['satelite']=='TERRA_M-M']
```

```python
del df['satelite']
del df['pais']
```

```python
df
```

FRP: https://revistapesquisa.fapesp.br/como-monitorar-o-fogo/

Risco de Queima: http://queimadas.dgi.inpe.br/queimadas/portal/informacoes/perguntas-frequentes#p23

Monografia: https://monografias.ufrn.br/jspui/bitstream/123456789/9704/1/tcc_dias_alexandre_henrique.pdf

```python
%matplotlib widget
from ipyleaflet import Map, Marker, CircleMarker

center = (-11.7997134,-53.8335376)

m = Map(center=center, zoom=3)

#marker = Marker(location=center, draggable=True)
#m.add_layer(marker);

display(m)
```

```python
frp_notnull = df[df['frp'].notnull()]
frp_notnull = frp_notnull.loc[frp_notnull['datahora'].str.contains('2020/09/30')]
```

```python
for index, row in frp_notnull.iterrows():
    lat = row['latitude']
    lon = row['longitude']
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

```python
lista_municipios = df['municipio'].unique()
type(lista_municipios), len(lista_municipios)
```

```python
corumba = df[df['municipio'] == "CORUMBA"]
corumba
```

```python
riscofogo = corumba['riscofogo']
diasemchuva = corumba['diasemchuva']
```

```python
import numpy as np
corumba = corumba.replace(-999, np.nan)
```

```python
agrupado = corumba.groupby('datahora').mean()
```

```python
agrupado
```

```python
datas = list(agrupado.index)
datas = [item[0:10] for item in datas]
datas
len(datas)
```

```python
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

```python
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

[Voltar ao notebook principal](00-Tutorial_Python_Brasil_2020.ipynb)

[Ir para o notebook SciPy](06-Tutorial_SciPy.ipynb)

```python

```
