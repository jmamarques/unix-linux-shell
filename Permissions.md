# Permissions

Type - 1 | Owner - 3 | Group - 3 | Others - 3

###### Permissions value
- r = read permission 
- w = write permission 
- x = execute permission 
- \- = no permission

###### Type 
- d directory 
- \- file 
- l link

![Permissions](https://www.guru99.com/images/permission(1).png)

###### User Denotations
user/owner……u 	
group…………….g 	
other.……………o 	
all………….…….a	

###### Operator Description
- \+ 	Adds a permission to a file or directory
- \- 	Removes the permission
- \= 	Sets the permission and overrides the permissions set earlier.

```console
#chmod <permissions> <target>
#chmod u=rwx,g=rwx,o= <file>
#chmod 770 <file>
foo@bar:~$ chmod 777 file.txt 
```

Minimum require to access a directory is read and executable otherwise will throw a error
* chown – change file owner and group – chown <root> <owner> -> owner to root
* chgrp – change the group of each <FILE> to <GROUP>. 

Example 
```console
#chgrp <GROUP> <FILE> 
foo@bar:~$ chgrp group file.txt 
```

## References
- [Guru99](https://www.guru99.com/file-permissions.html)

[Back](README.md)
