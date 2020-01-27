# Sort

## Content
 * **-u** - remove duplicates
 * **-r** - reverts
 * **-f** - sort is case insensitive
 * **-t'<DELIMITER>'** - each line is splinted by DELIMITER (IF doesn't exist then return entire line in first position)
 * **-k<column1[,<column2...]>**
 
 ```console
 # sort [OPTION]... [FILE]...
 foo@bar:~$ man sort
 foo@bar:~$ sort -u temp.data
 ```
 
## Examples
* Remove duplicates
```console
foo@bar:~$ sort -u temp.data
```
* Sort is case insensitive and reverts
```console
foo@bar:~$ sort -fr temp.data
```
* Splinted line by space and order based on fourth column
```console
foo@bar:~$ sort -t' ' -k4 temp.data
```

## References
- Linux manual

[Back](../COMMANDS.md)