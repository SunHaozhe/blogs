---
layout: post
category: Misc   
title: Latex memo 
tagline: by SunHaozhe
tags: 
  - memo
mathjax: true
comments: true
published: true
---

# Line spacing and margins 

Change line spacing for the whole document:

```latex
\renewcommand{\baselinestretch}{1.0}
```

Change margins:

```latex
\usepackage[left=0.75in,top=0.6in,right=0.75in,bottom=0.6in]{geometry}
```

[Microsoft engineers](http://www.metricationmatters.com/docs/PageBordersInchesORmillimetres.pdf) set the defaults for an A4 page (210 mm x 297 mm) at 1 inch (25.4 mm) top and bottom, with 1 and a 1/4 inch (31.7 mm) on each side. 

# Citations

Easily switch between (number) and (number+author) in the same document. Year is not shown. With `plainnat`, the number is different from `unsrt`, the ordering of citation numbers are not determined by the order of appearance. 

```latex
\usepackage[numbers]{natbib}
\bibliographystyle{plainnat}
% Hyperlinks bib references.
% This should be used together with \bibliographystyle{plainnat}
\usepackage{hyperref}  

\cite{} % citation with number
\citet{} % citation with number and author
```

# 1-column abstract in 2-column document


[https://www.texfaq.org/FAQ-onecolabs](https://www.texfaq.org/FAQ-onecolabs)

```latex
\documentclass[twocolumn]{article}


...


\begin{document}

% 1-column abstract in 2-column document 
\twocolumn[
  \begin{@twocolumnfalse}
    \maketitle
    \begin{abstract}
    Blablabla
    \end{abstract}
  \end{@twocolumnfalse}
]


%\section{Introduction}

\end{document}
```

Be careful when using long equations that would span across two columns. 


# Figures 

```latex
\begin{figure}[h]
    \centering
    \includegraphics[width=0.8\linewidth]{images/xxx.png}
    \caption{}
    \label{fig:xxx}
\end{figure}
```

# Positioning of floating elements

In order to be able to use the option `[H]`, do the following: 

```latex
\usepackage{float}
```

![figure_position_specifiers](/assets/images/blog/figure_position_specifiers.png)


In order to placing a figure spanning the two columns of a `twocolumn` document, we need to use the starred version of the `{figure*}` environment to enable the figure occupy the two columns. Example:

```latex
\begin{figure*}[h]
    \centering
    \includegraphics[width=0.8\linewidth]{Figures/xxx.png}
    \caption{}
    \label{fig:xxx}
\end{figure*}
```

`{figure*}` should not be used together with `[H]`. 


# Table

In order to make a table with 3 rows and 4 columns:

```latex
\usepackage{adjustbox}


\begin{table}[h]
	\centering
	\begin{adjustbox}{}
	\begin{tabular}{|c|c|c|c|}
	\hline
     &  &  &  \\\
    \hline
     &  &  &  \\\
    \hline
     &  &  &  \\\
    \hline
    \end{tabular}
	\end{adjustbox}
	\caption{}
	\label{tab:}
\end{table}
```

If the above table has too many columns that do not correctly fit the page width, one can use `\begin{adjustbox}{width=\linewidth}`

The signification of the parameters of `\begin{tabular}`:

* `l`	left-justified column
* `c`	centered column
* `r`	right-justified column
* `|`	vertical line
* `||`	double vertical line
* `|||` triple vertical line


Once in the `tabular` environment, `&` is column separator, `\\` is start new row, `\hline` is horizontal line.


`\checkmark` and `$\times$` can be used for binary information. 

```latex
\usepackage{makecell}

% bold table headers, maybe optional  
\renewcommand\theadfont{\bf} 
```

`\makecell{A \\B \\C}` can be used to break lines in table cells. `\thead{A \\B \\C}` additionally makes the text bold. 

If one only needs the header to be bold, one can just put `\bf` before the name (no need to put any `{` or `}` around).

### Import a csv file as table

The solution I found is to use a tool to transform a `.csv` file to a `.tex` file and then copy the text content onto our document. A tool that works well is **CSV2LaTeX** ([http://brouits.free.fr/csv2latex/](http://brouits.free.fr/csv2latex/)). 

The stable version can be downloaded here: [http://brouits.free.fr/csv2latex/csv2latex-0.22.tar.gz](http://brouits.free.fr/csv2latex/csv2latex-0.22.tar.gz) Unzip the downloaded file, then run `make` within the unzipped directory. This `make` command will not install the software in the system (at least not on my MacOS), it only generates an executable file called `csv2latex`. Put the executable file called `csv2latex` and the target `.csv` file under the same directory, do:

```zsh
./csv2latex table.csv > document.tex
```

We can also specify the format of the csv file, and some output disposition.

* The separator is a comma, block delimiter is a double-quote, and produces a LaTeX document with 40 lines per table and where the text is flushed left in each cell. 40 lines is a good average for A4 paper:

```zsh
./csv2latex --separator c --block d --lines 40 --position l table.csv > document.tex
```

* The separator is a semi-colon, block delimiter is a simple quote and produces a LaTeX document with 20 lines per table and where the text is centered in each cell.

```zsh
./csv2latex --separator s --block q --lines 20 --position c table.csv > document.tex
```

* If you have tricky block delimiter such as $ and separators like tabs, let the program guess

```zsh
./csv2latex --guess table.csv > document.tex
```

The generated `document.tex` contains `tabular` code. We can copy-paste it into something like this:

```latex
\begin{table}[h]
\centering
\begin{adjustbox}{}
% TODO
\end{adjustbox}
\caption{}
\label{tab:}
\end{table}
```

Below are some other solutions. These solutions do not work well. 

This solution will ignore rows whose row index contains special symbols such as `%`

```latex
\usepackage{csvsimple,booktabs,siunitx}


\begin{table}[h]
\centering
\begin{adjustbox}{}
\csvreader[tabular={|c|c|c|c|c|c|},
           table head=\toprule A & B & C & D & E & F \\\ \midrule,
           table foot=\bottomrule]
           {csv_files/figure1.csv}
           {1=\a,2=\b,3=\c,4=\d,5=\e,6=\f}
           {\a & \b & \c & \d & \e & \f}
\end{adjustbox}
\caption{}
\label{tab:}
\end{table}
```

This solution does not ignore rows whose row index contains special symbols such as `%`. It also automatically set up column names and columns. However, it is not clear how to specify table format. Another problem is that if the special symbol `_` appears in a column name, it will become another symbol in the compiled pdf.

```latex
\usepackage{csvsimple,booktabs,siunitx}

\csvreader[
  respect all,
  autotabular
]{csv_files/figure1.csv}{}{\csvlinetotablerow}
```

This solution automatically generate column names as `Column1`, `Column2`, etc. The generated table does not have lines inside the table.

```latex
% This solution does not need \usepackage{csvsimple}

\usepackage{datatool}

\begin{document}
...

\DTLloadrawdb[noheader,keys={},headers={}]{figure1}{csv_files/figure1.csv}

\begin{table}[h]
    \centering
\begin{tabular}{|c|c|c|c|}\hline
    \DTLdisplaydb{figure1}
    \\ \hline
    \end{tabular}
\end{table}

\end{document}
```


# Hyperlinks

```latex
\href{URL}{Text}
```


# Multi-line (block) comments 

```latex
\iffalse
blablabla...
\fi
```

# Mathematics

```latex
\usepackage{amsmath}
\usepackage{amssymb}
\usepackage{bbm}
\usepackage{amsthm}
\DeclareMathOperator*{\argmax}{arg\,max}
\DeclareMathOperator*{\argmin}{arg\,min}
```


# Glossary

```latex
\usepackage{glossaries}
\usepackage[nonumberlist]{glossaries} % disable page numbers after each glossaries

\makeglossaries

% does not allow line break in description
\newglossaryentry{}
{
    name=,
    description={}
}

% allows line break in description
\longnewglossaryentry{}
{
    name=,
    description={}
}

% lists all glossaries even if they are not used
\glsaddall

\begin{document}

...

\printglossaries

...

% Using defined terms
%% <label> is the X in \newglossaryentry{X}
%% which can be different from the "name" 
%% (Y in name=Y,)
\gls{<label>}
\glspl{<label>}
\Gls{<label>}
\Glspl{<label>}
\glslink{<label>}{<alternate text>}
\glssymbol{<label>}
\glsdesc{<label>}

\end{document}
```