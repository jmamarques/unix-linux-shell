# Find

## Content
 * **-mtime +/- <NUMBER>** - remove duplicates
 * **-type f/d** - reverts
 * **-prune** - sort is case insensitive
 * **-perm <PERMISSIONS>** - each line is splinted by DELIMITER (IF doesn't exist then return entire line in first position)
 * **-quit** – stop in first match
 * **-print** – display a result in standard output
 * **-exec <command>** – execute a script
 
 ```console
 # find . -iname "sample*" -exec programa.sh {}\;
 # find . -iname "sample*" -exec `ls -la` {}\;
 # {} -> arguments
 # \; -> each one – slowest
 # \+ -> all -> faster
 # find . -name "*" -type f -mtime +360 -exec `ls -l` {}\+ -print
 foo@bar:~$ man find
 foo@bar:~$ find . -name "*" -type f -mtime +360 -exec `ls -l` {}\+ -print
 ```
 
## Examples
* find files with permissions 600
```console
foo@bar:~$ find . -perm 600
```
* find files with 0 days
```console
foo@bar:~$ find . -name "*" -type f -mtime +0
```
* find directory and print in final
```console
foo@bar:~$ find . -type d -print
```
* Time took to find files with xargs
```console
time find . -name “*.exe*” | xargs ls -l
```
## References
- Linux manual

[Back](../COMMANDS.md)