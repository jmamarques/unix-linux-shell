# Echo

## Content
 * **-e** - enable interpretation of backslash escapes
 * **-E** - opposite of -e
 
 ```console
 # echo <?options> <text | variables>
 foo@bar:~$ echo -e "I care with backslash \n and you?"
 ```
 
## Examples
* Care about backslash
```console
# echo <?options> <text | variables>
foo@bar:~$ echo -e "I care with backslash \n and you?"
```
* Create a File
```console
foo@bar:~$ echo -e "Header -> how print to a file a text message" > <sample.txt |file name>
```
* Read a variable
```console
foo@bar:~$ echo -e "echo $JAVA_HOMME"
```

## References
- references.. TODO

[Back](../COMMANDS.md)