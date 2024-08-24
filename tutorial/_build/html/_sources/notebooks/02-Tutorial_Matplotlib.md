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

```{code-cell}
from IPython.display import Image
Image(url="https://matplotlib.org/_static/logo2_compressed.svg", width=600)
```

<!-- #region tags=["chapter"] -->
# Matplotlib: figurinhas maneiras

- Criada por John Hunter (2003) para ser similar ao MATLAB;
- Hoje, possui sua própria API orientada a objetos.
<!-- #endregion -->

```{code-cell}
%matplotlib widget
```

<!-- #region tags=["section"] -->
## O módulo `pyplot`
<!-- #endregion -->

```{code-cell}
import matplotlib.pyplot as plt
```

```{code-cell}
print(plt.__doc__)
```

### Exemplo 1

```{code-cell}
import numpy as np
t = np.arange(-5, 5, 0.1)
```

```{code-cell}
plt.plot(t, t**2);
```

```{code-cell}
plt.plot(t, t**2, 'r*')
plt.close()
```

```{code-cell}
plt.plot(t, t**2, linewidth=3)
plt.show()
```

<!-- #region tags=["section"] -->
## Outra maneira: API Orientada a objetos
<!-- #endregion -->

```{code-cell}
fig, ax = plt.subplots()
ax.plot(t, t**2, 'r*')
```

```{code-cell}
ax.plot(t, t**2, linewidth=3)
```

```{code-cell}
fig2, ax2 = plt.subplots()
ax2.plot(t, t**2, 'm--');
```

## Customização

```{code-cell}
fig, ax = plt.subplots()
ax.plot(t, t**2, 'm--');
```

```{code-cell}
fig
```

```{code-cell}
fig.clear()
```

```{code-cell}
fig.set_facecolor('black')
```

## Exemplo boxplot


https://matplotlib.org/search.html?q=boxplot


## Exemplo 3D

```{code-cell}
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

```{code-cell}
Image(url="https://matplotlib.org/_images/anatomy.png")
```

---


[Voltar ao notebook principal](00-Tutorial_Python_Sul_2024.md)

[Ir para o notebook Masked Arrays](03-Exemplo_Masked_Arrays.md)

[Ir para o notebook SVD](04-Exemplo_SVD.md)

[Ir para o notebook Queimadas](05-Exemplo_Queimadas.md)

