---
layout: post
category: Misc     
title: Latex compilation  
tagline: by SunHaozhe
tags: 
  - Utilities
mathjax: true
comments: true
published: true
---

# Compilers


pdfLaTeX is usually the default compiler:

* pdfLaTeX supports `.png`, `.jpg`, `.pdf` image formats. It will convert `.eps` images to .pdf on-the-fly during compilation, which may prolong the compilation time required. (pdfLaTeX may not be able to handle pstricks well on Overleaf.)
* LaTeX supports only `.eps` and `.ps` image formats for use with `\includegraphics`. If all the images in your project are `.eps` files, then this compiler setting is recommended.
* XeLaTeX and LuaLaTeX both supports UTF-8 robustly out of the box, as well as Truetype and OpenType. They are therefore recommended if you need to typeset non-Latin scripts on Overleaf, in conjunction with the polyglossia pacakge. They also support all of the `.png`, `.jpg`, `.pdf` and `.eps` image formats.
* XeLaTeX supports pstricks; but LuaLaTeX doesn't.


# Output formats

* (DVI) Device independent file format consists of binary data describing the visual layout of a document in a manner not reliant on any specific image format, display hardware or printer.
* (PS) PostScript file format describes text and graphics on page and it is based on vector graphics. PostScript is, until now, a standard in desktop publishing areas.
* (PDF) Portable Document Format is a file format, based on PostScript, used to represent documents in a manner independent of application software, hardware, and operating systems. It is now widely used as a file format for printing and for distribution on the Web.


If you are required to produce a DVI file from your Overleaf project: 

* Make sure you're using only .eps and .ps images in your project.
* Click on the Overleaf menu icon above the file list panel, and set the Compiler setting to 'LaTeX'.
* Recompile your project.
* Click on the "Logs and output files" button next to the Recompile button.
* Scroll right to the bottom, and click on "Other logs and output files".
* You should then able to download the generated `.dvi` file.

If you need to convert the DVI file into PS yourself

* `cd` to a dedicated folder
* Place the generated/downloaded `.dvi` file into the dedicated folder (for example, let's call it `output.dvi`).
* Place all `.eps` image files in the dedicated folder. Even if the images were previously placed into a `fig/` subfolder, you need to place `.eps` image files at the same directory level as the `.dvi` file. 
* Type the following (`dvips` can be installed with MacTex or equivalent): 

```bash
dvips -P pdf -o output.ps output.dvi

# The -P pdf option generates a 
# PS file smoother on the screen 
# when further converted to PDF.
```



# References

* [https://www.overleaf.com/learn/latex/Choosing_a_LaTeX_Compiler](https://www.overleaf.com/learn/latex/Choosing_a_LaTeX_Compiler)










































