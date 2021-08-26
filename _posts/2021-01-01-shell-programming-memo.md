---
layout: post
category: Misc     
title: Shell programming memo 
tagline: by SunHaozhe
tags: 
  - memo
mathjax: true
comments: true
published: true
---

* https://learnxinyminutes.com/docs/bash/
* https://devhints.io/bash 
* https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html 


Shell programming in Linux/Unix

# Steps to create a shell script

* Create a text file any text editor, name this script file with extension `.sh`, for example `xxx.sh`
* Start the script with `#!/bin/zsh` or `#!/bin/bash` or `#!/bin/sh` 
* Write some code and save this file. 
* Currently, permission is not yet granted to this script so it cannot be run directly from shells (`./xxx.sh`). Instead, one can do:
  * `zsh xxx.sh`
  * `bash xxx.sh`
  * `sh xxx.sh` 




`#!` is an operator called shebang which directs the script to the interpreter location. 

Each command starts on a new line, or after a semicolon, for example:

```zsh
# simple hello world example
echo Hello world!
```

```zsh
echo 'This is the first line'; echo 'This is the second line'
```



# Adding shell comments

`#` is used to add a comment in shell programming.


# Shell variables

By convention, Unix shell variables will have their names in UPPERCASE.

### Variable Types

When a shell is running, three main types of variables are present

* **Local Variables** − A local variable is a variable that is present within the current instance of the shell. It is not available to programs that are started by the shell. They are set at the command prompt.
* **Environment Variables** − An environment variable is available to any child process of the shell. Some programs need environment variables in order to function correctly. Usually, a shell script defines only those environment variables that are needed by the programs that it runs.
* **Shell Variables** − A shell variable is a special variable that is set by the shell and is required by the shell in order to function correctly. Some of these variables are environment variables whereas others are local variables.

### Special variables

```zsh
# The following line prints the process 
# ID number, or PID, of the current shell 
echo $$ 
```

Similarly, the following table shows a number of special variables that one can use in shell scripts:

* `$0` The filename of the current script.
* `$n` These variables correspond to the arguments with which a script was invoked. Here n is a positive decimal number corresponding to the position of an argument (the first argument is `$1`, the second argument is `$2`, and so on).
* `$#` The number of arguments supplied to a script.
* `$?` The exit status of the last command executed.
* `$$` The process number of the current shell. For shell scripts, this is the process ID under which they are executing.
* `$!` The process number of the last background command. 


### Array variable

Besides scalar variables, there are also array variables.

`zsh` arrays start at index position 1, `bash` arrays start at index position 0.

For the following script, `bash` and `sh` will execute properly but `zsh` will generate an error.

```bash
NAME[0]="Zara"
NAME[1]="Qadir"
NAME[2]="Mahnaz"
NAME[3]="Ayan"
NAME[4]="Daisy"
echo "First Index: ${NAME[0]}"
echo "Second Index: ${NAME[1]}"
```



### Examples

Create a shell variable and then prints it: 

```zsh
VARIABLE="Hello" # declaring a variable
echo $VARIABLE # this line prints the variable to the shell 
```

`=` cannot be surrounded by any space, which is different from Python programming. Otherwise, the shell will decide that `VARIABLE` (or the other part) is a command it must execute and give an error because it can't be found. 


Shell enables you to store any value you want in a variable. 

```zsh
VAR1="Zara Ali"
VAR2=100 
```

Shell provides a way to mark variables as read only:

```zsh
NAME="Zara Ali"
readonly NAME
NAME="Qadiri" # this will generate an error 
```

Unsetting or deleting a variable directs the shell to remove the variable from the list of variables that it tracks. Once you unset a variable, you cannot access the stored value in the variable:

```zsh
NAME="Zara Ali"
unset NAME
echo $NAME
```

The above example does not print anything (empty line with break line). You cannot use the `unset` command to unset variables that are marked `readonly`


Read variable from user's input: 

```zsh
echo "what is your name?"
read NAME
echo "How do you do, $NAME?"
read REMARK
echo "I am $REMARK too!"
```

```
% zsh xxx.sh
what is your name?
sf
How do you do, sf?
gsgs
I am gsgs too!
```

Our current directory is available through the command `pwd`. We can also use the built-in variable `$PWD`. 

```bash
# The following are equivalent

echo "I'm in $(pwd)" # execs `pwd` and interpolates output
echo "I'm in $PWD" # interpolates the variable
```

String quotes:

```bash
NAME="John"
echo "Hi $NAME"  # Hi John
echo 'Hi $NAME'  # Hi $NAME
```

# set command in bash 

`set` command is used by `bash` (see `help set` in `bash`), not by `zsh` 



# Functions

Defining functions

```bash
myfunc() {
    echo "hello $1"
}
```

Alternate syntax:

```bash
function myfunc() {
    echo "hello $1"
}
```

Returning values

```bash
# Only the last line will print to user's screen 

myfunc() {
    local myresult='some value'
    echo $myresult
}

result="$(myfunc)" 
echo $result # some value 
```

























































