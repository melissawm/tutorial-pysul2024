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

# Exemplo: Álgebra Linear em arrays n-dimensionais


*(baseado no tutorial [Linear algebra on n-dimensional arrays](https://numpy.org/doc/stable/user/tutorial-svd.html) da documentação oficial da NumPy)*


## Resumo

Vamos usar uma [decomposição matricial](https://en.wikipedia.org/wiki/Matrix_decomposition), a Decomposição em Valores Singulares (também conhecida como SVD), para gerar uma aproximação de uma imagem. Vamos usar a imagem `face` do módulo [scipy.datasets](https://docs.scipy.org/doc/scipy/reference/datasets.html). Para isso, vamos instalar a dependência adicional [pooch](https://pypi.org/project/pooch/):

```{code-cell} ipython3
!pip install pooch
```

```{code-cell} ipython3
from scipy import datasets
img = datasets.face()
```

Agora, `img` é uma array do NumPy, como podemos ver usando a função `type`:

```{code-cell} ipython3
type(img)
```

Podemos ver a imagem usando o comando [matplotlib.pyplot.imshow](https://matplotlib.org/api/_as_gen/matplotlib.pyplot.imshow.html#matplotlib.pyplot.imshow):

```{code-cell} ipython3
import matplotlib.pyplot as plt
%matplotlib inline
```

```{code-cell} ipython3
plt.imshow(img)
plt.show()
```

### Imagens como arrays

Note que uma imagem é apenas uma array. No entanto, ela não é 2D:

```{code-cell} ipython3
img.shape
```

Essa imagem é representada como uma [tuple](https://docs.python.org/dev/tutorial/datastructures.html#tut-tuples) com 3 elementos, ou seja, `img` é uma array 3D. Como a imagem é colorida, os dados estão organizados em 3 arrays 2D representando os canais de cores (RGB): cada uma das matrizes (correspondentes a cada cor) tem shape 768x1024.

Veja que

```{code-cell} ipython3
img.ndim
```

Cada "dimensão" dessa matriz é um *axis* (eixo). Nesse caso, o *primeiro índice do terceiro eixo* representa os dados dos pixels vermelhos na nossa imagem. Podemos acessá-lo usando

```{code-cell} ipython3
img[:, :, 0]
```

Veja que cada entrada é um valor entre 0 e 255, representando o nível de vermelho em cada pixel da imagem. Como já mencionamos,

```{code-cell} ipython3
img[:, :, 0].shape
```

Já que vamos realizar operações nessa matriz, vamos converter os valores para floats (usando broadcasting):

```{code-cell} ipython3
img_array = img / 255
```

Agora,

```{code-cell} ipython3
img_array.max(), img_array.min()
```

e

```{code-cell} ipython3
img_array.dtype
```

Podemos também separar os dados de cor:

```{code-cell} ipython3
red_array = img_array[:, :, 0]
green_array = img_array[:, :, 1]
blue_array = img_array[:, :, 2]
```

### Operações em um eixo

Podemos usar álgebra linear para aproximar nosso conjunto de dados. Aqui, vamos usar a [decomposição SVD (Singular Value Decomposition)](https://en.wikipedia.org/wiki/Singular_value_decomposition) para reconstruir uma imagem com menos informações do que a original, mas ainda representando a mesma imagem.

```{code-cell} ipython3
from scipy import linalg
```

Para extrairmos informações de uma matriz dada, vamos usar a SVD para obtermos 3 arrays que, quando multiplicadas, nos retornarão a matriz original. Da definição, dada uma matriz $A$, podemos calcular matrizes $U$, $\Sigma$ e $V$ tais que

$$U \Sigma V^T = A$$

em que $U$ e $V^T$ são quadradas e $\Sigma$ tem o mesmo shape que $A$. $\Sigma$ é diagonal e contém os [valores singulares](https://en.wikipedia.org/wiki/Singular_value) de $A$, organizados do maior para o menor. Esses valores são sempre não-negativos e podem ser usados como indicadores da importância de algumas características representadas pela matriz $A$.

Na prática, vamos usar uma matriz apenas para simplificar. Note que, de acordo com a [colorimetria](https://en.wikipedia.org/wiki/Grayscale#Colorimetric_(perceptual_luminance-reserving)_conversion_to_grayscale),
é possível obtermos uma versão em preto-e-branco da nossa imagem colorida se aplicarmos a fórmula

$$Y = 0.2126 R + 0.7152 G + 0.0722 B$$

em que $Y$ é uma array representando a imagem cinza, e $R$, $G$ e $B$ são as matrizes dos canais vermelho, verde e azul que calculamos antes.

```{code-cell} ipython3
img_gray = img_array @ [0.2126, 0.7152, 0.0722]
```

Agora, `img_gray` tem shape

```{code-cell} ipython3
img_gray.shape
```

Para observarmos nossa imagem em tons de cinza, precisamos escolher o mapa de cores correto da Matplotlib:

```{code-cell} ipython3
plt.imshow(img_gray, cmap="gray")
plt.show()
```

Agora, aplicando [linalg.svd](https://docs.scipy.org/doc/scipy/reference/generated/scipy.linalg.svd.html), obtemos

```{code-cell} ipython3
U, s, Vt = linalg.svd(img_gray)
```

Veja que

```{code-cell} ipython3
U.shape, s.shape, Vt.shape
```

Note que `s` tem um shape diferente: só tem uma dimensão. Precisamos ter isso em mente pois operações voltadas para matrizes 2D podem não funcionar imediatamente. Por exemplo,

```{code-cell} ipython3
:tags: [raises-exception]

s @ Vt
```

Podemos contornar essa maneira econômica de armazenar os valores de `s` reconstruindo a matriz $\Sigma$ com as dimensões apropriadas (768x1024, já que `U` é 768x768 e `Vt` é 1024x1024.)

```{code-cell} ipython3
import numpy as np
Sigma = np.zeros((768, 1024))
for i in range(768):
    Sigma[i, i] = s[i]
```

Agora, vamos verificar se nossa aproximação está correta usando a [norma da diferença](https://docs.scipy.org/doc/scipy/reference/generated/scipy.linalg.norm.html#scipy.linalg.norm):

```{code-cell} ipython3
linalg.norm(img_gray - U @ Sigma @ Vt)
```

Também poderíamos ter usado [numpy.allclose](https://numpy.org/devdocs/reference/generated/numpy.allclose.html#numpy.allclose):

```{code-cell} ipython3
np.allclose(img_gray, U @ Sigma @ Vt)
```

Para ver uma aproximação será razoável, vamos analisar os valores de `s`:

```{code-cell} ipython3
plt.plot(s)
plt.show()
```

No gráfico podemos ver que, apesar de termos 768 valores singulares em `s`, a maioria destes (depois do 150o valor, aproximadamente) são muito pequenos comparados aos primeiros. Então pode ser interessante usar apenas a informação dos primeiros `k` *valores singulares* para obtermos uma aproximação mais econômica da nossa imagem.

Por exemplo, se escolhemos

```{code-cell} ipython3
k = 10
```

construimos nossa aproximação com

```{code-cell} ipython3
approx = U @ Sigma[:, :k] @ Vt[:k, :]
```

Note que usamos apenas as primeiras `k` linhas de `Vt`, já que todas as outras linhas seriam multiplicadas por zeros correspondentes aos valores singulares que eliminamos da aproximação.

```{code-cell} ipython3
plt.imshow(approx, cmap="gray")
plt.show()
```

Podemos agora escolher outros valores de `k` e observar aproximações melhores ou piores.

+++

### Aplicar às outras cores

Agora queremos fazer a mesma operação a todas as cores - idealmente, ao mesmo tempo!

Podemos fazer isso com a `numpy.linalg` ao invés da `scipy.linalg` - note que esses dois submódulos são ligeiramente diferentes (verifique a documentação para mais detalhes: https://numpy.org/doc/stable/reference/routines.linalg.html)

+++

No entando, precisamos organizar nossos dados para que a matriz tenha dimensões `(N, M, M)`, em que a primeira entrada representa o número de matrizes nessa _stack_ (no nosso caso, 3).

Por enquanto,

```{code-cell} ipython3
img_array.shape
```

então precisamos permutar os eixos dessa matriz para obtermos `(3, 768, 1024)`. Podemos usar [numpy.transpose](https://numpy.org/devdocs/reference/generated/numpy.transpose.html#numpy.transpose):
```
np.transpose(x, axes=(i, j, k))
```
em que a matriz resultante terá eixos reordenados de acordo com os índices `(i, j, k)`.

Para nosso caso:

```{code-cell} ipython3
img_array_transposed = np.transpose(img_array, (2, 0, 1))
img_array_transposed.shape
```

Agora podemos aplicar a SVD da `numpy.linalg`:

```{code-cell} ipython3
from numpy.linalg import svd as numpy_svd
```

```{code-cell} ipython3
U, s, Vt = numpy_svd(img_array_transposed)
```

Finalmente, precisamos reconstruir nossa imagem. Note que

```{code-cell} ipython3
U.shape, s.shape, Vt.shape
```

### Produtos com arrays n-dimensionais

Se você está acostumado a matrizes 1- ou 2-D, talvez pense em usar [numpy.dot](https://numpy.org/devdocs/reference/generated/numpy.dot.html#numpy.dot) e [numpy.matmul](https://numpy.org/devdocs/reference/generated/numpy.matmul.html#numpy.matmul) (ou o operador `@`) sem ver diferenças entre eles. No entanto, para matrizes n-dimensionais, isso não é verdade (veja a documentação para mais detalhes: https://numpy.org/devdocs/reference/generated/numpy.matmul.html).

Vamos reconstruir `Sigma` com [fill_diagonal](https://numpy.org/devdocs/reference/generated/numpy.fill_diagonal.html), usando cada uma das 3 linhas em `s` como a diagonal de cada uma das 3 matrizes em `Sigma`:

```{code-cell} ipython3
Sigma = np.zeros((3, 768, 1024))
for j in range(3):
    np.fill_diagonal(Sigma[j, :, :], s[j, :])
```

Agora,

```{code-cell} ipython3
reconstructed = U @ Sigma @ Vt
```

Note que

```{code-cell} ipython3
reconstructed.shape
```

Finalmente,

```{code-cell} ipython3
plt.imshow(np.transpose(reconstructed, (1, 2, 0)))
plt.show()
```

Essa imagem deve ser visualmente indistinguível da original (apesar de que podemos ter introduzido erros numéricos com a reconstrução). Uma mensagem de erro do tipo `"Clipping input data to the valid range for imshow with RGB data ([0..1] for floats or [0..255] for integers)."` é esperada já que manipulamos os dados originais e eles provavelmente não estão mais no intervalo [0, 1].

Agora, assim como antes, escolhemos o número `k` de valores singulares que usaremos na reconstrução:

```{code-cell} ipython3
approx_img = U @ Sigma[..., :k] @ Vt[..., :k, :]
```

Veja que selecionamos
- apenas os `k` primeiros componentes do último eixo de `Sigma` (ou seja, usamos apenas as primeiras `k` colunas de cada uma das três matrizes nesse stack)
- apenas os primeiros `k` componentes do penúltimo eixo de `Vt` (ou seja, usamos apenas as primeiras `k` linhas de cada matriz da stack `Vt` e todas as colunas).

Para ler mais sobre a sintaxe de elipse (que denota uma abreviação dos outros eixos), veja [Indexing](https://numpy.org/devdocs/user/basics.indexing.html).

Agora,

```{code-cell} ipython3
approx_img.shape
```

que não é o shape correto para nossa imagem. Mais uma vez, vamos transpor nossa matriz, obtendo

```{code-cell} ipython3
plt.imshow(np.transpose(approx_img, (1, 2, 0)))
plt.show()
```

A imagem será mais precisa com mais valores singulares, e menos precisa com menos valores singulares. Mesmo assim, podemos ver as características mais importantes da imagem usando menos "dados".
