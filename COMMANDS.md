# Commands
- man -> interface about commands (manual)
```console
# man <command>
foo@bar:~$ man ls
```
- which -> show a pathname of files or links
```console
# which <file or link>
foo@bar:~$ which ls man
```
- clear -> clean the terminal screen
```console
# clear
foo@bar:~$ clear
```
- echo -> display a line of text ([more detail](./commands/ECHO.md))
```console
# echo <?options> <text | variables>
foo@bar:~$ echo -e "I care with backslash \n and you?"
```
- uname
```console
# uname <?options | -a -> all informations>
foo@bar:~$ man uname
foo@bar:~$ uname -a"
```
- ls -> list directory contents
```console
# ls <?options | -a -> hidden files> <path | name extsion -> *.exe>
# important options:
# -d -> directories
# -a -> hidden files
# -l -> long form with permissions, name, size, type, ...
# -lt and -ltr order by desc and asc respectively
foo@bar:~$ man ls
foo@bar:~$ ls -a"
```
- history -> GNU History Library
```console
# history -> display list of commands before executed
# !<number> -> execute that command
# !! -> las command
foo@bar:~$ man history
foo@bar:~$ history
foo@bar:~$ !1
foo@bar:~$ !!
```
- cat -> concatenate files and print on the standard output
```console
# cat – n <file> -> enumerate each line
# cat -n = nl (alias)
# -v -> show special signals
# nl -s ‘<string>’ <FILE> -> count how many times appear that word
foo@bar:~$ man cat
foo@bar:~$ ls | cat -n
```
- nl - number lines of files
```console
# nl <options> <file> -> enumerate each line
foo@bar:~$ man nl
foo@bar:~$ ll | nl
```
- less -> opposite of more
```console
# less <file> -> display file by end
foo@bar:~$ man less
foo@bar:~$ ll | less
foo@bar:~$ less dummyFile.txt
```
- more -> file perusal filter for crt viewing
```console
# more <file>
foo@bar:~$ man more
foo@bar:~$ ll | more
foo@bar:~$ more dummyFile.txt
```
- vim - Vi IMproved, a programmer's text editor
```console
# vi <file>
foo@bar:~$ man vi
foo@bar:~$ ll | vi
foo@bar:~$ vi dummyFile.txt
```
- nano -> Nano's ANOther editor, an enhanced free Pico clone
```console
# nano <file>
foo@bar:~$ man nano
foo@bar:~$ ll | nano
foo@bar:~$ nano dummyFile.txt
```
- \> , \>\>
- chmod -> change file mode bits ([more detail](./Permissions.md))
- chown -> change file owner and group ([more detail](./Permissions.md))
- chgrp -> change group ownership ([more detail](./Permissions.md))
```console
# chmod <permissions> <target>
# chmod u=rwx,g=rwx,o= <file>
# chmod 770 <file>
foo@bar:~$ man chmod 
foo@bar:~$ chmod 777 file.txt 
```
- ln -> make links
```console
# ln -s <path/destination – target> <name shortcut>
# -s – symbolic link, do a symbolic links instead of hard links
foo@bar:~$ man ln
foo@bar:~$ ln -s /bin executables
```
- readlink -> print resolved symbolic links or canonical file names
```console
# readlink <options> <link>
foo@bar:~$ man readlink
foo@bar:~$ readlink dummyLink
```
- head -> output the first part of files

```console
# head -<N> <FILE>
# head -1 <FILE> -> First line
# N -> number de lines to display
foo@bar:~$ man head
foo@bar:~$ ls | head -1
foo@bar:~$ head -1 dummyFile.txt
```
- tail -> output the last part of files
```console
# tail -<N> <FILE>
# tail -1 <FILE> -> last line
# N -> number de lines to display
foo@bar:~$ man tail
foo@bar:~$ ls | tail -1
foo@bar:~$ tail -1 dummyFile.txt
```
- wc -> print newline, word, and byte counts for each file
```console
# wc - > count words, lines and characters in files
# wc <OPTIONS> <FILE>
# -l -> number of lines
# -w -> number of words
# -c -> number of bytes
# wc <FILE> -> display counter of words, lines and characters
foo@bar:~$ man wc
foo@bar:~$ ll | wc -l
foo@bar:~$ wc dummyFile.txt
```
- tree –> recursive list the content of directory
```console
# tree <DIRECTORY> -> display a useful and friendly structure of content
foo@bar:~$ man tree
foo@bar:~$ tree .
```
- mkdir -> make directories
```console
mkdir <options> <nameDir>
-m <PERMISSION> - create a directory with default permission
-p – create the parents too
# tree <DIRECTORY> -> display a useful and friendly structure of content
foo@bar:~$ man mkdir
foo@bar:~$ mkdir test
foo@bar:~$ mkdir -m 777 test
foo@bar:~$ mkdir -p notexist/v1
```
- cd –> change directory
  
```console
#cd <DIR>
# .. back in directory
# ~ -> home
# $Home -> home
# / -> root
#cd - ->previous location
foo@bar:~$ cd ~
```
- pwd –> print full filename of current working directory
```console
# pwd <options>
foo@bar:~$ man pwd
foo@bar:~$ pwd
```
- mktemp –> create temporary file and return the full path 
```console
# mktemp <OPTIONS> <template need to be present many X's>
#   -d – create a directory
foo@bar:~$ man mktemp
foo@bar:~$ mktemp topXXXXXX
foo@bar:~$ mktemp -d
foo@bar:~$ mktemp
```
- rm -> remove files or directories
```console
# rm <OPTIONS> <PATTERN>
#   -r – recursive
#   -rf – recursive and force without asking-rf – recursive and force without asking
foo@bar:~$ man rm
foo@bar:~$ rm -f Test*
foo@bar:~$ rm -r dummyFolder
foo@bar:~$ rm -rf dummy*
```
- /dev/null - black hole in linux
- mv –> move a file or rename
```console
#  mv <TARGET> <DESTINATION>
foo@bar:~$ man mv
foo@bar:~$ mv dummyTarget dummyDestination
```
- cp –> copy a file
```console
#  cp <TARGET> <NAME> <DIRECTORY>
foo@bar:~$ man cp
foo@bar:~$ cp dummyTarget dummyName dummyDestination
```
- touch –> important to change date/time of files
```console
#  touch -t  201911271000.00 <FILE> -> create or change a date
#  touch `date “+%Y%m%d”` <FILE>
#  date “+%Y%m%d” -> date in this format
foo@bar:~$ man touch
foo@bar:~$ touch `date “+%Y%m%d”` dummyFile
```
- date -> print or set the system date and time
```console
# date [OPTION]... [+FORMAT]
foo@bar:~$ date +%T
foo@bar:~$ date +%C%y%m%d
```
- grep -> print lines matching a pattern ([more detail](./commands/GREP.md))
```console
# grep [OPTIONS] PATTERN [FILE...]
foo@bar:~$ grep ErroR *
```
- tee -> read from standard input and write to standard output and files
```console
#  tee <OPTIONS> <FILE> -> create a file and show the result
foo@bar:~$ man tee
foo@bar:~$ tee dummyFile
```
- sort
- find
- xargs
- time
- cut
- paste
- diff
- gzip
- gunzip
- zcat
- tar
- df
- du
- unset
- readonly
- export





sample :
```console
foo@bar:~$ whoami
```

## References
- I used the man to each command

[Back](README.md)
