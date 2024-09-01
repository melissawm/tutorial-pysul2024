---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

# Queimadas


Dados do INPE: http://queimadas.dgi.inpe.br/queimadas/bdqueimadas/

Dados da NASA: https://firms.modaps.eosdis.nasa.gov/

```{code-cell}
!ls
```

```{code-cell}
zipfile_inpe = "dados/Focos_BDQueimadas.zip"
```

```{code-cell}
from zipfile import ZipFile
```

```{code-cell}
with ZipFile(zipfile_inpe, 'r') as zip: 
    zip.printdir() 
    print(f'Extracting file {zipfile_inpe} now...') 
    zip.extractall(path="dados") 
    print('Done!')
```

```{code-cell}
!ls dados
```

```{code-cell}
import os
csv_inpe = os.path.join("dados", "Focos_2020-07-01_2020-09-30.csv")
```

```{code-cell}
with open(csv_inpe, 'r') as f:
    data = f.readlines()
```

```{code-cell}
print(data[0:10])
```

```{code-cell}
import pandas as pd
```

```{code-cell}
with open(csv_inpe, 'r') as f:
    df = pd.read_csv(f)
```

```{code-cell}
df
```

```{code-cell}
df = df[df['riscofogo']!=0.0]
```

```{code-cell}
df['satelite'].unique()
```

```{code-cell}
df = df[df['satelite']=='TERRA_M-M']
```

```{code-cell}
del df['satelite']
del df['pais']
```

```{code-cell}
df
```

FRP: https://revistapesquisa.fapesp.br/como-monitorar-o-fogo/

Risco de Queima: http://queimadas.dgi.inpe.br/queimadas/portal/informacoes/perguntas-frequentes#p23

Monografia: https://monografias.ufrn.br/jspui/bitstream/123456789/9704/1/tcc_dias_alexandre_henrique.pdf

```{code-cell}
%matplotlib widget
from ipyleaflet import Map, Marker, CircleMarker

center = (-11.7997134,-53.8335376)

m = Map(center=center, zoom=3)

#marker = Marker(location=center, draggable=True)
#m.add_layer(marker);

display(m)
```

```{code-cell}
frp_notnull = df[df['frp'].notnull()]
frp_notnull = frp_notnull.loc[frp_notnull['datahora'].str.contains('2020/09/30')]
```

```{code-cell}
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

```{code-cell}
lista_municipios = df['municipio'].unique()
type(lista_municipios), len(lista_municipios)
```

```{code-cell}
corumba = df[df['municipio'] == "CORUMBA"]
corumba
```

```{code-cell}
riscofogo = corumba['riscofogo']
diasemchuva = corumba['diasemchuva']
```

```{code-cell}
import numpy as np
corumba = corumba.replace(-999, np.nan)
```

```{code-cell}
agrupado = corumba.groupby('datahora').mean()
```

```{code-cell}
agrupado
```

```{code-cell}
datas = list(agrupado.index)
datas = [item[0:10] for item in datas]
datas
len(datas)
```

```{code-cell}
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

```{code-cell}
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
