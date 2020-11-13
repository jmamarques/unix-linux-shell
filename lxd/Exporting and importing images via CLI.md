[TOC]

# Exporting images #
To export an existing LXD image we must first get either its alias or fingerprint:
```
$ lxc image list
+-----------+--------------+--------+---------------------------------------------+--------------+-----------------+-----------+-------------------------------+
|   ALIAS   | FINGERPRINT  | PUBLIC |                 DESCRIPTION                 | ARCHITECTURE |      TYPE       |   SIZE    |          UPLOAD DATE          |
+-----------+--------------+--------+---------------------------------------------+--------------+-----------------+-----------+-------------------------------+
| wserver19 | cee305252934 | no     |                                             | x86_64       | VIRTUAL-MACHINE | 5357.71MB | Nov 10, 2020 at 12:13pm (UTC) |
+-----------+--------------+--------+---------------------------------------------+--------------+-----------------+-----------+-------------------------------+
|           | 2f456602cd86 | no     | ubuntu 20.04 LTS amd64 (release) (20201014) | x86_64       | VIRTUAL-MACHINE | 503.69MB  | Oct 27, 2020 at 4:30pm (UTC)  |
+-----------+--------------+--------+---------------------------------------------+--------------+-----------------+-----------+-------------------------------+
```

For this example we will be exporting our Windows Server 2019 image, to do so we simply place its alias in the system call:
```
# This image will be exported as a tar archive in the working directory
lxc image export wserver19
```

# Importing images #
To import an LXD image we must have the tar archive of said image:
```
lxc image import /path/to/tar/wserver19.tar.gz --alias wserver19
```

After the import finishes we can check if the image was succesfuly imported:
```
$ lxc image list
+--------------+--------------+--------+----------------------------------------------+--------------+-----------------+-----------+-------------------------------+
|    ALIAS     | FINGERPRINT  | PUBLIC |                 DESCRIPTION                  | ARCHITECTURE |      TYPE       |   SIZE    |          UPLOAD DATE          |
+--------------+--------------+--------+----------------------------------------------+--------------+-----------------+-----------+-------------------------------+
| wserver19    | cee305252934 | no     |                                              | x86_64       | VIRTUAL-MACHINE | 5357.71MB | Nov 11, 2020 at 12:12pm (UTC) |
+--------------+--------------+--------+----------------------------------------------+--------------+-----------------+-----------+-------------------------------+

```