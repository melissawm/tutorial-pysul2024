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

# O Ecossistema Científico no Python

```python
import numpy as np
import scipy as sp
import matplotlib as mpl
```

## Introdução

- [Quem sou eu?](https://github.com/melissawm) 👋
- [Código de Conduta](https://python.org.br/cdc/) 🤝

### O que é esse tutorial?

- Primeiro contato
- Conceitos básicos e quando usar essas bibliotecas
- Onde buscar ajuda

```{code-cell} ipython3
from IPython.display import Image
Image(url="https://i.imgur.com/MBQqSmT.jpg", width=600)
```

### Computação Científica

```{code-cell} ipython3
Image("imagens/comp_cientifica.png", width=600)
```

Na prática...

```{code-cell} ipython3
Image("imagens/comp_cientifica_2.png", width=600)
```

*Baseado [nessa imagem](https://agilescientific.com/blog/2018/1/10/what-is-scientific-computing)*

---

### O que é o ecossistema científico do Python? Quem mora nele?

```{code-cell} ipython3
Image(url="https://media.springernature.com/full/springer-static/image/art%3A10.1038%2Fs41586-020-2649-2/MediaObjects/41586_2020_2649_Fig2_HTML.png", width=600)
```

*Fonte: Harris et al., "Array Programming with NumPy", Nature volume 585, pages 357–362 (2020)*

---

#### Histórico

- BLAS (1979) - especificação de rotinas de baixo nível para operações comuns de álgebra linear
- LAPACK
- MATLAB
- Python
    - Numeric/Numarray
    - NumPy (2005)

---

#### Linguagens e habilidades

| github.com/numpy/numpy | github.com/scipy/scipy |
--------|---------------------------------------
| ![](https://i.imgur.com/z6zsl4Q.png) | ![](https://i.imgur.com/HJWrdZr.png) |

- Documentação/Escrita técnica
- Desenvolvimento Web
- Design/UX
- Comunicação
- Gerenciamento de projetos

---

#### Comunidades: usuários e desenvolvedores

- Cientistas
- Pessoas da indústria
- Voluntários
- Bolsas, patrocínio, doações

---

## Conteúdo desse tutorial

- [Parte 1. NumPy](01-Tutorial_NumPy.md)
- [Parte 2. Matplotlib](02-Tutorial_Matplotlib.md)
- [Parte 3. SciPy](03-Tutorial_SciPy.md)
- Parte 4. Exemplos práticos
    - [Aproximação de imagens usando a SVD](04-Exemplo_SVD.md)
    - [Queimadas](05-Exemplo_Queimadas.md)
- Exemplos extra
    - [Arrays com máscara (masked arrays)](0x-Exemplo_Masked_Arrays.md)

---

## Outras ferramentas

- [Pandas](https://pandas.pydata.org/https://pandas.pydata.org/)
- [SymPy](https://www.sympy.org/pt/index.htmlhttps://www.sympy.org/pt/index.html)
- [scikit-learn](https://scikit-learn.orghttps://scikit-learn.org)
- [scikit-image](https://scikit-image.org/https://scikit-image.org/)
- [Dask](https://dask.org/https://dask.org/)
- [PyTorch](https://pytorch.org/https://pytorch.org/)
- [TensorFlow](https://www.tensorflow.orghttps://www.tensorflow.org)
- [Julia](https://julialang.org/https://julialang.org/)
- [Fortran](https://fortran-lang.org/https://fortran-lang.org/)
<!-- #endregion -->

## Referências

Algumas referências interessantes e caminhos para mais informações além da documentação oficial.

- [From Python to NumPy](https://github.com/rougier/from-python-to-numpy), livro aberto de Nicolas Rougier.
- [Scientific Python Lectures](https://lectures.scientific-python.org/)
- [Elegant SciPy](https://github.com/elegant-scipy/elegant-scipy), livro aberto de Juan Nunez-Iglesias, Harriet Dashnow and Stéfan van der Walt (também publicado pela O'Reilly)

## Obrigada! 💖

`@melissawm`

```{code-cell} ipython3

```
