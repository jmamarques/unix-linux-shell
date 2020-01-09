# Utilities

- /dev/null -> black hole in linux
```console
foo@bar:~$ ls | cat -n | > /dev/null
```
- | -> use to consecutive commands
```console
foo@bar:~$ ls | cat -n
```
- ; -> allow write consecutive commands
```console
foo@bar:~$ ls; echo `ls` > /dev/null 
```
- ` -> allow execute a command inside of other command
```console
foo@bar:~$ echo `ls` 
```

## References
- I used the man to each command

[Back](README.md)
