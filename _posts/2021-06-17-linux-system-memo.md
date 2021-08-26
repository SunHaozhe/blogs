---
layout: post
category: Misc     
title: Linux system memo  
tagline: by SunHaozhe
tags: 
  - memo
mathjax: true
comments: true
published: true
---

# Printing and writing to files

`echo` is usually used for printing message in the terminal, much like the printing function in programming languages. `echo` can also be used to write a string into a file if it is used together with a redirection operator. For example:

```bash
echo "blablabla" > [file_path]
```

### Redirect output of a program to a file

```bash
program [arguments...] > logs.txt
```

However, by doing the above no message will be output to the terminal (`stdout`). 

### Redirect output of a program both to a file and stdout

`2>&1` dumps the `stderr` and `stdout` streams. `tee logs.txt` takes the stream it gets and writes it to the screen and to the file `logs.txt`.

```bash
program [arguments...] 2>&1 | tee logs.txt
```


# Users and groups in linux

### Users

In order to list all users in Linux

```bash
cat /etc/passwd
```

Each line corresponds to a user, for example, 

```
mike:x:1242:1308:,,,:/home/mike:/bin/bash
```

where `mike` is the user name or login name. `x` is the encrypted password is stored in the `/etc/shadow` file. `1242` is the UID (user ID number). `1308` is the primary GID (group ID number). Then it is GECOS. It may includes user’s full name (or application name, if the account is for a program), building and room number or contact person, office telephone number, home telephone number and any other contact information. `/home/mike` is the home directory for the user. `/bin/bash` is the login shell for the user. Pathnames of valid login shells comes from the `/etc/shells` file.

### Groups

In order to list all groups in Linux

```bash
cat /etc/group
```

Like `/etc/passwd`, there is one entry per line, all fields are separated by a colon `:`. The first field is the name of group `group_name`. If you run `ls -l` command, you will see this name printed in the group field. The second field is the password, generally password is not used, hence it is empty/blank. It can store encrypted password. This is useful to implement privileged groups. The third field is the Group ID (GID), each user must be assigned a group ID. You can see this number in your `/etc/passwd` file. The fourth field is the group List, it is a list of user names of users who are members of the group. The user names, must be separated by commas. 

In order to list all groups the currently logged in user belongs to (the first group is the primary group.)

```bash
groups
```

To get a list of all groups a specific user belongs to, provide the username to the groups command as an argument

```bash
groups [user_name]
```

### ID command

The `id` command prints information about the specified user and its groups. 

If the username is omitted it shows information for the current user.

```bash
id
```

To get information about a specific user

```bash
id [user_name]
```

### Adding a new user

Example using `adduser`

```bash
adduser --disabled-password --gecos '' --shell /bin/bash [user_name]
```

* `--disabled-password` login only possible using SSH RSA keys
* `--gecos` sets the gecos field for the new entry generated. 
* `--shell` sets the user's login shell, rather than the default specified by the configuration file. 


# File mode

In Linux, access to the files is controlled by the operating system using file permissions, attributes, and ownership. Each file is owned by a particular user and a group and assigned with permission access rights for three different classes of users:

* The file owner
* The group members
* Others (everybody else)


There are three file permissions types that apply to each user class and allows you to specify which users are allowed to read the file, write to the file, or execute the file. The same permission attributes apply for both files and directories with a different meaning:

* **The read permission** The file is readable. For example, when the read permission is set, the user can open the file in a text editor. The directory’s contents can be viewed. The user can list files inside the directory with the `ls` command.
* **The write permission** The file can be changed or modified. The directory’s contents can be altered. The user can create new files , delete existing files , move files , rename files ..etc.
* **The execute permission** The file can be executed. The directory can be entered using the `cd` command.

File permissions can be viewed using the `ls -l` command. For example:

```bash
ls -l filename.txt
```

```
-rw-r--r-- 12 linuxize users 12.0K Apr  8 20:51 filename.txt
|[-][-][-]-   [------] [---]
| |  |  | |      |       |
| |  |  | |      |       +-----------> 7. Group
| |  |  | |      +-------------------> 6. Owner
| |  |  | +--------------------------> 5. Alternate Access Method
| |  |  +----------------------------> 4. Others Permissions
| |  +-------------------------------> 3. Group Permissions
| +----------------------------------> 2. Owner Permissions
+------------------------------------> 1. File Type
```

The first character shows the file type. It can be a regular file `-`, directory `d`, a symbolic link `l`, or any other special type of file. The next nine characters represent the file permissions, three triplets of three characters each. The first triplet shows the owner permissions, the second one group permissions, and the last triplet shows everybody else permissions.

File permission can be represented in a numeric or symbolic format. The permission number can consist of three or four digits, ranging from 0 to 7. When 3 digits number is used, the first digit represents the permissions of the file’s owner, the second one the file’s group and the last one all other users. 

* r (read) = 4
* w (write) = 2
* x (execute) = 1
* no permissions = 0

The permissions digit of a specific user class is the sum of the values of the permissions for that class. Each digit of the permissions number may be a sum of 4, 2, 1 and 0:

* 0 (0+0+0) – No permission
* 1 (0+0+1) – Only execute permission
* 2 (0+2+0) – Only write permission
* 3 (0+2+1) – Write and execute permissions
* 4 (4+0+0) – Only read permission
* 5 (4+0+1) – Read and execute permission
* 6 (4+2+0) – Read and write permissions
* 7 (4+2+1) – Read, write, and execute permission

When the 4 digits number is used, the first digit is used for something else. We do not cover it in this memo.

`chmod` changes the file mode (permission) bits of each given file according to mode, which can be either a symbolic representation of changes to make, or an octal number representing the bit pattern for the new mode bits.

```
-R, --recursive
```



# Ownership



`chown` changes the user and/or group ownership of each given file. 

* If only an owner (a user name or numeric user ID) is given, that user is made the owner of each given file, and the files' group is not changed. 
* If the owner is followed by a colon and a group name (or numeric group ID), with no spaces between them, the group ownership of the files is changed as well. 
* If a colon but no group name follows the user name, that user is made the owner of the files and the group of the files is changed to that user's login group. 
* If the colon and group are given, but the owner is omitted, only the group of the files is changed; in this case, chown performs the same function as `chgrp`. 
* If only a colon is given, or if the entire operand is empty, neither the owner nor the group is changed.

```
-R, --recursive
```

To change the group ownership of a file/directory `Target` to the group `GroupA`

```bash
chgrp -R project_name file_or_directory
```




