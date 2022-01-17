---
layout: post
category: Misc     
title: Submitting latex project to arXiv  
tagline: by SunHaozhe
tags: 
  - Utilities
mathjax: true
comments: true
published: true
---

# bibtex

If you use `.bib` file, you need to upload the corresponding `.bbl` file onto arXiv. `.bbl` file can be obtained by locally compile the source latex files. You may also download the `.bbl` file from OverLeaf.


# Which files/folders to upload?

Folders containing all figures, `.tex` files, `.bib` files, `.bbl` files, `.sty` files should be included. Other files should be deleted before uploading to arXiv, for example `.pdf`, `.aux`,  `.log`, `.blg`, `.out`, `.synctex.gz` files. 

The name (before `.tex` or `.bib`) of the `.bib` file should be the same as the corresponding `.tex` file. For example, `main.tex` and `main.bib` (thus `main.bbl`). 

If you have a second `.tex` file, say `supplement.tex`. It will also be compiled and the generated pdf will be concated at the end of the generated pdf of `main.tex`. Consider adding `supplement.bib` and `supplement.bbl`.


Zip all the files (the whole folder) and upload the `.zip` file to arXiv.


# OverLeaf support for arXiv

Overleaf has a feature to submit to arXiv. 


![arxiv_submit_button.png](/blogs/assets/images/blog/arxiv_submit_button.png)

![arxiv_submit_2.png](/blogs/assets/images/blog/arxiv_submit_2.png)

![arxiv_submit_3.png](/blogs/assets/images/blog/arxiv_submit_3.png)



See: [LaTeX checklist for arXiv submissions (OverLeaf)](https://www.overleaf.com/learn/how-to/LaTeX_checklist_for_arXiv_submissions)


# natbib issues

If some of your bib items do not contain year information, `natbib` package might raise errors which prevent you from compiling your project on arXiv. In this case, add the option `numbers` when you include `natbib`.

```latex
\usepackage[numbers]{natbib}
```


Even if you did not include `natbib` yourself, it might be included by other packages, for example `neurips_data_2021`. In the case of `neurips_data_2021`, you must add `\PassOptionsToPackage{numbers}{natbib}` before `\usepackage[final]{neurips_data_2021}`.

# PDFLaTeX? 


Add `\pdfoutput=1` in the first 5 lines of `.tex` files.

# Figures

Figures can be put in folders. 

The name of figures should not use symbols like `[` or `]`. 

Figures can be `.png`, `.pdf`. 

# hyperref package 

```
arXiv Warning: user included plain hyperref directive
```


Remove `\usepackage{hyperref}`, do not only comment it out


[https://tex.stackexchange.com/a/616620/212587](https://tex.stackexchange.com/a/616620/212587)



# How to add references? 

Just use the usual way: `\bibliography{main}`, you don't need to replace that with `\input{main.bbl}`.


```latex
{
\small
\bibliography{main}
%\input{main.bbl}
}
```

















