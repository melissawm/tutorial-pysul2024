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

# Exemplo: Regressão usando 3 métodos

```{code-cell}
%matplotlib widget
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

