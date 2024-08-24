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

# O Ecossistema Científico no Python

<!-- #region -->
```python
import numpy as np
import scipy as sp
import matplotlib as mpl
```
<!-- #endregion -->

---

```python
#! cp /home/shared/tutorial.zip .
#! unzip tutorial.zip
```

<!-- #region tags=["chapter"] -->
## Introdução

- [Quem sou eu?](https://github.com/melissawm) 👋
- [Código de Conduta](https://python.org.br/cdc/) 🤝
<!-- #endregion -->

<!-- #region tags=["section"] -->
### O que é esse tutorial?
<!-- #endregion -->

- Primeiro contato
- Conceitos básicos e quando usar essas bibliotecas
- Onde buscar ajuda

```python
from IPython.display import Image
Image(url="https://i.imgur.com/MBQqSmT.jpg", width=600)
```

---

<!-- #region tags=["section"] -->
### Computação Científica
<!-- #endregion -->

```python
Image("imagens/comp_cientifica.png", width=600)
```

Na prática...

```python
Image("imagens/comp_cientifica_2.png", width=600)
```

*Baseado [nessa imagem](https://agilescientific.com/blog/2018/1/10/what-is-scientific-computing)*


---

<!-- #region tags=["section"] -->
### O que é o ecossistema científico do Python? Quem mora nele?
<!-- #endregion -->

```python
Image(url="https://media.springernature.com/full/springer-static/image/art%3A10.1038%2Fs41586-020-2649-2/MediaObjects/41586_2020_2649_Fig2_HTML.png", width=600)
```

*Fonte: Harris et al., "Array Programming with NumPy", Nature volume 585, pages 357–362 (2020)*


#### Histórico

- BLAS (1979) - especificação de rotinas de baixo nível para operações comuns de álgebra linear
- LAPACK
- MATLAB
- Python
    - Numeric/Numarray
    - NumPy (2005)


#### Linguagens e habilidades

| github.com/numpy/numpy | github.com/scipy/scipy |
--------|---------------------------------------
| ![](https://i.imgur.com/z6zsl4Q.png) | ![](https://i.imgur.com/HJWrdZr.png) |

- Documentação/Escrita técnica
- Desenvolvimento Web
- Design/UX
- Comunicação
- Gerenciamento de projetos


#### Comunidades: usuários e desenvolvedores

- Cientistas
- Pessoas da indústria
- Voluntários
- Bolsas, patrocínio, doações


---

<!-- #region tags=["section"] -->
## Conteúdo
<!-- #endregion -->

<!-- #region -->
- [Parte 1. NumPy](01-Tutorial_NumPy.ipynb)


- [Parte 2. Matplotlib](02-Tutorial_Matplotlib.ipynb)


- Parte 3. Exemplos práticos

    - [Arrays com máscara (masked arrays)](03-Exemplo_Masked_Arrays.ipynb)
    - [Aproximação de imagens usando a SVD](04-Exemplo_SVD.ipynb)
    - [Queimadas](05-Exemplo_Queimadas.ipynb)


- [Parte 4. SciPy](06-Tutorial_SciPy.ipynb)
    - [Regressão](07-Exemplo_Regressao.ipynb)
    
<!-- #endregion -->

---


## Para terminar...

```python
Image(url="https://pics.me.me/lee-lee-dibango-pmishraworld-math-problem-2-2-me-import-numpy-35881429.png", width=600)
```

<!-- #region tags=["chapter"] -->
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
- [SciPy Lecture Notes](https://scipy-lectures.org/)
- [Elegant SciPy](https://github.com/elegant-scipy/elegant-scipy), livro aberto de Juan Nunez-Iglesias, Harriet Dashnow and Stéfan van der Walt (também publicado pela O'Reilly)

<!-- #region tags=["chapter"] -->
## Obrigada! 💖

`@melissawm`
<!-- #endregion -->
