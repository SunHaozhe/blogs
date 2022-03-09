---
layout: post
category: Misc     
title: Startup scripts of bash and zsh  
tagline: by SunHaozhe
tags: 
  - System
  - Shell
mathjax: true
comments: true
published: true
---


All shells have startup files, containing commands which are executed as soon as the shell starts. 


# Bash

For Bash, they work as follows. Read down the appropriate column. Executes A, then B, then C, etc. The B1, B2, B3 means it executes only the first of those files found. 

For an “interactive non-login shell”, it reads .bashrc, but for an “interactive login shell” it reads from the first of `.bash_profile`, `.bash_login` and `.profile` (only). 

Shells could be simply divided into three types as below: interactive login, interactive non-login, and just simple script that runs without any interaction with user action.


```
+----------------+-----------+-----------+------+
|                |Interactive|Interactive|Script|
|                |login      |non-login  |      |
+----------------+-----------+-----------+------+
|/etc/profile    |   A       |           |      |
+----------------+-----------+-----------+------+
|/etc/bash.bashrc|           |    A      |      |
+----------------+-----------+-----------+------+
|~/.bashrc       |           |    B      |      |
+----------------+-----------+-----------+------+
|~/.bash_profile |   B1      |           |      |
+----------------+-----------+-----------+------+
|~/.bash_login   |   B2      |           |      |
+----------------+-----------+-----------+------+
|~/.profile      |   B3      |           |      |
+----------------+-----------+-----------+------+
|BASH_ENV        |           |           |  A   |
+----------------+-----------+-----------+------+
|                |           |           |      |
+----------------+-----------+-----------+------+
|                |           |           |      |
+----------------+-----------+-----------+------+
|~/.bash_logout  |    C      |           |      |
+----------------+-----------+-----------+------+
```

Please note that each script can add or undo changes made in previously called script. 


When invoked as an interactive login shell, Bash looks for the `/etc/profile` file, and if the file exists , it runs the commands listed in the file. Then Bash searches for `~/.bash_profile`, `~/.bash_login`, and `~/.profile files`, in the listed order, and executes commands from the first readable file found (only).

When Bash is invoked as an interactive non-login shell, it reads and executes commands from `~/.bashrc`, if that file exists, and it is readable.


* `/etc/profile` - It contains Linux system wide environment and startup programs. This file runs first when a user logs in to the system. This file also acts as a system-wide profile file for the bash shell.
* `.bash_profile` is read and executed when Bash is invoked as an interactive login shell, while `.bashrc` is executed for an interactive non-login shell.
* Use `.bash_profile` to run commands that should run only once, such as customizing the `$PATH` environment variable. Put the commands that should run every time you launch a new shell in the .bashrc file. This include your aliases and functions, etc. Typically, `~/.bash_profile` contains lines source the `.bashrc` file. This means each time you log in to the terminal, both files are read and executed. 
* Most Linux distributions are using `~/.profile` instead of `~/.bash_profile`. The `~/.profile` file is read by all shells, while `~/.bash_profile` only by Bash.
* On OS X, Terminal by default runs a login shell every time, this is a little different to most other systems, but you can configure that in the preferences.
* OSX's Terminal App does something non-standard: it creates every new tab or window as if it were a login shell, which means that .bash_profile is called. 


![bash_startup_scripts_summary.png](/blogs/assets/images/blog/bash_startup_scripts_summary.png)


# Zsh

Zsh always executes `zshenv`. Then, depending on the case:

* run as a login shell, it executes `zprofile`
* run as an interactive, it executes `zshrc`
* run as a login shell, it executes `zlogin`


![zsh_startup_scripts_summary.png](/blogs/assets/images/blog/zsh_startup_scripts_summary.png)

# Types of shell: interactive and login shells

* Basically, the shell is just there to take a list of commands and run them; it doesn't really care whether the commands are in a file, or typed in at the terminal. In the second case, when you are typing at a prompt and waiting for each command to run, the shell is **interactive**; in the other case, when the shell is reading commands from a file, it is, consequently, **non-interactive**.
* When you first log into the computer, the shell you are presented with is **interactive**, but it is also a **login** shell. If you type `bash`, it starts up a new interactive shell: because you didn't give it the name of a file with commands in, it assumes you are going to type them interactively. Now you've got two interactive shells at once, one waiting for the other: it doesn't sound all that useful, but there are times when you are going to make some radical changes to the shell's settings temporarily, and the easiest thing to do is to start another shell, do what you want to do, and exit back to the original, unaltered, shell --- so it's not as stupid as it sounds. However, that second shell will not be a **login** shell. One way of making a shell a login shell is to run it yourself with the option `-l`; typing `bash -l` will start a bash that also thinks it's a login shell. 
* In simple terms, an interactive shell is a shell that reads and writes to a user’s terminal, while a non-interactive shell is a shell that is not associated with a terminal, like when executing a script. An interactive shell can be either login or non-login shell.
* Login shells don't have to be interactive. 
* A login shell is invoked when a user login to the terminal either remotely via ssh or locally, or when Bash is launched with the --login option. An interactive non-login shell is invoked from the login shell, such as when typing bash in the shell prompt or when opening a new Gnome terminal tab.





# References

* https://shreevatsa.wordpress.com/2008/03/30/zshbash-startup-files-loading-order-bashrc-zshrc-etc/
* https://youngstone89.medium.com/unix-introduction-bash-startup-files-loading-order-562543ac12e9
* https://linuxize.com/post/bashrc-vs-bash-profile/#:~:text=bash_profile%20is%20read%20and%20executed,customizing%20the%20%24PATH%20environment%20variable%20.
* https://tanguy.ortolo.eu/blog/article25/shrc#:~:text=Both%20Bash%20and%20Zsh%20use,for%20interactive%20or%20login%20shells.














































































