---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.16.4
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

```python
from IPython.display import Image
Image(url="https://matplotlib.org/_static/logo2_compressed.svg", width=600)
```

<!-- #region tags=["chapter"] -->
# Matplotlib: figurinhas maneiras

- Criada por John Hunter (2003) para ser similar ao MATLAB;
- Hoje, possui sua própria API orientada a objetos.
<!-- #endregion -->

```python
%matplotlib widget
```

<!-- #region tags=["section"] -->
## O módulo `pyplot`
<!-- #endregion -->

```python
import matplotlib.pyplot as plt
```

```python
print(plt.__doc__)
```

### Exemplo 1

```python
import numpy as np
t = np.arange(-5, 5, 0.1)
```

```python
plt.plot(t, t**2);
```

```python
plt.plot(t, t**2, 'r*')
plt.close()
```

```python
plt.plot(t, t**2, linewidth=3)
plt.show()
```

<!-- #region tags=["section"] -->
## Outra maneira: API Orientada a objetos
<!-- #endregion -->

```python
fig, ax = plt.subplots()
ax.plot(t, t**2, 'r*')
```

```python
ax.plot(t, t**2, linewidth=3)
```

```python
fig2, ax2 = plt.subplots()
ax2.plot(t, t**2, 'm--');
```

## Customização

```python
fig, ax = plt.subplots()
ax.plot(t, t**2, 'm--');
```

```python
fig
```

```python
fig.clear()
```

```python
fig.set_facecolor('black')
```

## Exemplo boxplot


https://matplotlib.org/search.html?q=boxplot


## Exemplo 3D

```python
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

```python
Image(url="https://matplotlib.org/_images/anatomy.png")
```

---


[Voltar ao notebook principal](00-Tutorial_Python_Sul_2024.md)

[Ir para o notebook Masked Arrays](03-Exemplo_Masked_Arrays.md)

[Ir para o notebook SVD](04-Exemplo_SVD.md)

[Ir para o notebook Queimadas](05-Exemplo_Queimadas.md)

```python

```
