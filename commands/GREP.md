# Grep

## Content
 * **-c** - repeat times
 * **-i** - inactive case-sensitive
 * **-v** - opposite of search
 * **-l** - name of file where the string was found
 * **-ln** - prints the line number where the string was found
 * **-F** - fixed-strings
 * **-E** - extended-regexp
 * **-r** - recursive
 
 ```console
 # grep [OPTIONS] PATTERN [FILE...]
 # grep [OPTIONS] -e PATTERN ... [FILE...]
 # grep [OPTIONS] -f FILE ... [FILE...]
 foo@bar:~$ man grep
 foo@bar:~$ grep Error *
 ```
 
## Examples
* Regex
```console
foo@bar:~$ ls -la | grep <REGEX>
```
* Search this word in all files of this directory
```console
foo@bar:~$ grep test ./*
```
* Opposite of Search
```console
foo@bar:~$ grep -v test *
```

## References
- Linux manual

[Back](../COMMANDS.md)