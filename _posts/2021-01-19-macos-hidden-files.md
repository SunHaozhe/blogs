---
layout: post
category: Misc     
title: MacOS hidden files  
tagline: by SunHaozhe
tags: 
  - Common knowledge
mathjax: true
comments: true
published: true
---


This post talks about hidden files (or folders) created by Apple MacOS.

# .DS_Store file

In the Apple MacOS operating system, **.DS_Store** is a file that stores custom attributes of its containing folder, such as the position of icons or the choice of a background image. The name is an abbreviation of Desktop Services Store. It is created and maintained by the Finder application in every folder, and has functions similar to the file desktop.ini in Microsoft Windows. Starting with a full stop (period) character, it is hidden in Finder and many Unix utilities. Its internal structure is proprietary, but has since been reverse-engineered. Starting at macOS 10.12 16A238m, Finder will not display **.DS_Store** files (even with com.apple.finder AppleShowAllFiles YES set).


Pretty much every folder on your hard disk is likely to contain a **.DS_Store** file.


### To delete .DS_STORE files in current folder and all subfolders from command line 

The `find` utility walks a file hierarchy, it recursively descends the directory tree for each path listed, evaluating an expression in terms of each file in the tree. `.` represents the current directory. The option `-name` specifies the pattern to match. The option `-delete` deletes found files and/or directories. The option `-type f` is for extra caution, it excludes directories. Here, `f` means regular file, `d` means directory, `s` means socket, `l` means symbolic link, etc..

```zsh
find . -name '.DS_Store' -type f -delete 
```



# __MACOSX folder


The **__MACOSX** folder is created when a Mac user creates and archive (also called a zip file) using the Mac. If the Mac user sends the zip file to another Mac user, the folder will not appear - this is a hidden folder. Many files on the Mac have two parts: a data fork, and a resource fork. The built in zip utility on the Mac sequesters all of the resource forks into this **__MACOSX** folder when creating a zip archive. For certain files (like some font files), these resource forks are necessary to be left intact. 

When the Mac user sends the zip file to a PC user, however, all of the hidden files are shown. PC users are often confused by these (seemingly superfluous) files and folders. 







# References

* https://en.wikipedia.org/wiki/.DS_Store
* https://gotoes.org/sales/Zip_Mac_Files_For_PC/What_Is__MACOSX.php#:~:text=The%20__MACOSX%20folder%20is,fork%2C%20and%20a%20resource%20fork.
* https://www.publicspace.net/ABetterFinderAttributes/Manual_RemoveInvisibleFiles.html 

























































































