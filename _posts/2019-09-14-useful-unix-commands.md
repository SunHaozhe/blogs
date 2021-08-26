---
layout: post
category: Misc
title: Useful unix commands
tagline: by SunHaozhe
tags: 
  - Common knowledge
  - memo
mathjax: true
comments: true
published: true
---

************************************************************************************************

https://stackoverflow.com/questions/43702546/tensorboard-doesnt-show-all-data-points 

```bash
tensorboard --samples_per_plugin scalars=0 --logdir xxx/
```

************************************************************************************************

For Mac OS, the `caffeinate` command is used to prevent a Mac from going to sleep. 

```bash
# stay until you tell the command to stop running 
# or close the terminal
caffeinate 

# wait for the process with ID 41734 to finish
caffeinate -w 41734  

# 18000 is the timeout value in seconds 
caffeinate -t 18000  

# Option -d prevents the display from sleeping.
# Option -i prevents the system from idle sleeping.
# Option -s prevents the system from sleeping. 
# Option -s is valid only when system is running on AC power.
# Some other options also exist. 
```

************************************************************************************************

To get the summary of a grand total disk usage size of a directory in "Human Readable Format": 

```bash
du -sh *
# 1.8G	XXX
```

Report file system disk space usage. For example, to get the amount of the remaining disk size in "Human Readable Format":

```bash
# information of the system
df -h

# information of a directory
df -h *
```

************************************************************************************************

On Mac OS, check CPU temperature (this needs to be installed `sudo gem install istats`)

```bash
istats 
```

************************************************************************************************

On Mac OS, change the default shell to Zsh

```bash
chsh -s /bin/zsh
```

On Mac OS, change the default shell to Bash

```zsh
chsh -s /bin/bash
```

************************************************************************************************

Check sum on Mac OS

```zsh
# user manual
shasum --help

# check sha1 sum 
shasum -a 1 [file_name]
```



************************************************************************************************

zip using command line

```zsh
# zip a directory
zip -r new_zip_file.zip target_directory/

# zip all the content in the current directory 
zip -r new_zip_file.zip . 

# -X excludes extra file attributes
zip -r -X new_zip_file.zip target_directory/

# zip several files 
zip new_zip_file.zip target_file_1 target_file_2
```

unzip using command line 

```zsh
# unzip into a separate new directory 
unzip -d name_of_a_new_directory blabla.zip

# unzip and put all the files directly 
# into the current directory
unzip blabla.zip
```

************************************************************************************************

NVIDIA System Management Interface (`nvidia-smi`) 

```zsh
nvidia-smi

# If we just want the GPU name
nvidia-smi --query-gpu=name --format=csv,noheader

# The following will loop and call 
# the view at every second. 
nvidia-smi -l 1
```

************************************************************************************************

Print newline, word, and byte counts for each file

```bash
wc

# count the number of lines
wc -l

# count the number of files in [name_of_directory]
ls [name_of_directory] | wc -l
```

```bash
man wc

-c, --bytes
    print the byte counts

-m, --chars
    print the character counts

-l, --lines
    print the newline counts
    
-w, --words
    print the word counts
```


************************************************************************************************

Search pattern (regular expression)

```bash
grep

# print the line containing the pattern [pattern]
ls [name_of_directory] | grep [pattern]
```


************************************************************************************************























