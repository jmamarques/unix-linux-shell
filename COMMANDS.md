# Commands
- man - interface about commands (manual)
```console
# man <command>
foo@bar:~$ man ls
```
- which - show a pathname of files or links
```console
# which <file or link>
foo@bar:~$ which ls man
```
- clear - clean the terminal screen
```console
# clear
foo@bar:~$ clear
```
- echo - display a line of text [more detail](./commands/ECHO.md)
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
#
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
- nl
- less
- more
- vi
- nano
- \> , \>\>
- chmod
- chown
- chgrp
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
- tail - output the last part of files
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
- tree
- mkdir
- cd
- pwd
- mktemp
- rm
- /dev/null - black hole in linux
- mv
- cp
- touch
- date
- grep
- tee
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
foo
```

## References
- I used the man to each command

[Back](README.md)
