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
Image("imagens/scipy_med.png", width=100)
```

<!-- #region tags=["chapter"] -->
# SciPy 

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

```python
import scipy as sp
print(sp.__doc__)
```

## Exemplo: Minimização de funções

```python
from scipy.optimize import fmin
```

```python
func = lambda x : x**2
```

```python
fmin(func, -1)
```

## Exemplo: integração numérica


Dada a equação diferencial ordinária


$$\frac{dy(t)}{dt} = -y(t) + 1$$
$$y(0) = 0$$


Encontre uma solução numérica para a equação diferencial com condição inicial associada. Expanda o horizonte temporal até encontrar uma solução estável.  

```python
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
