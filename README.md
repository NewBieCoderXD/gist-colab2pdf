# About
Python notebook in colab to pdf converter.

# Usage
  - Copy code in below into last cell of colab.
  - Change url of colab as argument of gist_colab2pdf function and run.

# Code
```
! wget -nc https://raw.githubusercontent.com/NewBieCoderXD/gist-colab2pdf/refs/heads/main/gist_colab2pdf.ipynb
%run gist_colab2pdf.ipynb
gist_colab2pdf("https://colab.research.google.com/gist/...",Method.WEBPDF)
# or
# gist_colab2pdf("https://colab.research.google.com/gist/...",Method.LATEX)
```
