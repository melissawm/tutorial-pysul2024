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
Image(url="https://matplotlib.org/_static/logo_light.svg")
```

<!-- #region tags=["chapter"] -->
# Matplotlib: figurinhas maneiras

- Criada por John Hunter (2003) para ser similar ao MATLAB;
- Hoje, possui sua própria API orientada a objetos.
<!-- #endregion -->

+++

Para trabalhar com o Matplotlib em notebooks Jupyter, podemos escolher o [backend interativo](https://matplotlib.org/stable/users/explain/figure/backends.html#interactive-backends) desejado:

```{code-cell} ipython3
%matplotlib widget
```

## Interfaces: implícita (`matplotlib.pyplot`) e orientada a objetos

+++

Por razões históricas, o Matplotlib implementa uma interface explícita para acesso às funções de criações de gráficos - o módulo `pyplot`.

+++

### Exemplo

```{code-cell} ipython3
import matplotlib.pyplot as plt
```

```{code-cell} ipython3
print(plt.__doc__)
```

### Exemplo 1

```{code-cell} ipython3
import numpy as np
t = np.arange(-5, 5, 0.1)
```

```{code-cell} ipython3
plt.plot(t, t**2);
```

### API Orientada a objetos (recomendada)

+++

A maneira moderna e recomendade de criar gráficos com o Matplotlib é através da API orientada a objetos. (Leia mais em https://matplotlib.org/stable/users/explain/figure/api_interfaces.html)

```{code-cell} ipython3
fig, ax = plt.subplots()
ax.plot(t, t**2, 'r*')
```

```{code-cell} ipython3
ax.plot(t, t**2, linewidth=3)
```

```{code-cell} ipython3
fig2, ax2 = plt.subplots()
ax2.plot(t, t**2, 'm--');  # Acrescente um ponto-e-vírgula ao final da 
                           # linha para não imprimir uma mensagem
```

## Customização

```{code-cell} ipython3
fig, ax = plt.subplots()
ax.plot(t, t**2, 'm--');
```

```{code-cell} ipython3
fig
```

```{code-cell} ipython3
fig.clear()
```

```{code-cell} ipython3
fig.set_facecolor('black')
```

## Exemplo boxplot


https://matplotlib.org/stable/gallery/statistics/boxplot_demo.html


## Exemplo 3D

```{code-cell} ipython3
import matplotlib.pyplot as plt
import numpy as np

from matplotlib import cm
from matplotlib.ticker import LinearLocator

fig, ax = plt.subplots(subplot_kw={"projection": "3d"})

X = np.arange(-5, 5, 0.25)
Y = np.arange(-5, 5, 0.25)
X, Y = np.meshgrid(X, Y)
R = np.sqrt(X**2 + Y**2)
Z = np.sin(R)

surf = ax.plot_surface(X, Y, Z, cmap=cm.coolwarm, linewidth=0, antialiased=False)

ax.set_zlim(-1.01, 1.01)
ax.zaxis.set_major_locator(LinearLocator(10))
ax.zaxis.set_major_formatter("{x:.02f}")

fig.colorbar(surf, shrink=0.5, aspect=5)

plt.show()
```

## Guia do usuário: galeria e exemplos

- https://matplotlib.org/tutorials/introductory/usage.html
- https://matplotlib.org/tutorials/introductory/usage.html#parts-of-a-figure

```{code-cell} ipython3
Image(url="https://matplotlib.org/_images/anatomy.png")
```

## Documentação oficial

+++

Além de tutorials e guias de usuário, a Matplotlib também mantém uma galeria de exemplos bastante completa:

https://matplotlib.org/stable/gallery/index.html

```{code-cell} ipython3
from IPython.display import IFrame
```

```{code-cell} ipython3
IFrame("https://matplotlib.org/stable/gallery/index.html", width="100%", height=600)
```

```{code-cell} ipython3

```
