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
- history
- cat
- nl
- less
- more
- vi
- nano
- \> , \>\>
- chmod
- chown
- chgrp
- ln
- readlink
- head
- tail
- wc
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
