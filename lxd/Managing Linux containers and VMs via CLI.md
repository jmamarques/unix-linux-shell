[TOC]

# Container creation #

Before starting, make sure to follow the instructions [here](https://bitbucket.org/asolidodev/ironman/wiki/LXD%20installation%20for%20Ubuntu%2020.04#markdown-header-ubuntu-bridge-configuration) to setup a network bridge on ubuntu. We will assume that the bridge is named **br0** for the following example and the profile is named **bridge-cli**.

Through LXD's CLI containers (image list can be found [here](https://us.images.linuxcontainers.org/)) can be created as follows:

```
# E.g.: Create an Ubuntu container
# On creation we apply the previously created profile with the -p flag and add the root disk's source with -s
lxc launch ubuntu:20.04 container-name -p bridge-cli -s default

# Check the created container
lxc list container-name

# Check the disk type device added with the -s flag
lxc config device show container-name

# Configure the container network (ignore if you want DHCP)
/usr/local/AS/bin/lxd-network.sh container-name IP NETMASK GATEWAY DNS1 [DNS2] [DN3]

```


# VM creation #
VM creation is similar to container creation, needing only an extra `--vm` option. The creation of the `vm` profile applied can be found [here](https://bitbucket.org/asolidodev/ironman/wiki/LXD%20installation%20for%20Ubuntu%2020.04#markdown-header-vm-bridge-profile):
```
lxc launch ubuntu:20.04 vm-name -p vm-cli -s default --vm

# Check the created vm
lxc list vm-name

# Check the disk type device added with the -s flag
lxc config device show vm-name

# Configure the VM network (ignore if you want DHCP)
/usr/local/AS/bin/lxd-network.sh vm-name IP NETMASK GATEWAY DNS1 [DNS2] [DN3]
```

# Container and VM deletion #
To delete either a container or a VM their execution must first be stopped:
```
# Check the container or VM's name to be stopped
lxc list

# Stop the execution
lxc stop container-vm-name

# Delete the container or VM
lxc delete container-vm-name
```
# Instance resource management #
For all possible options, consult [here](https://lxd.readthedocs.io/en/stable-4.0/instances/) for more information regarding configuration options.

Before setting any configuration options we must first stop the instance from running:
```
lxc stop instance
```
## CPU management ##
We can manage both the number of cores usable by an instance, the amount of processing power it can extract from those cores and priority:
```
# Sets a maximum of 3 cores to the container
lxc config set instance limits.cpu 3

# Alternatively:
# 0-2 exposes the first 3 cores of the CPU to the container
lxc config set instance limits.cpu 0-2

# sets the relative share of assigned CPU time across containers
lxc config set instance limits.cpu.allowance 20%

# Start the instance
lxc start instance

# To check the changes
lxc exec instance bash

# After having terminal access inside the container
htop
```


## Memory management ##
To set the memory we can specify the units used, for example MB for megabytes. Without the keyword it will be processed as bytes:
```
# Set the maximum amount of memory
lxc config set instance limits.memory 500MB

# By setting to hard it ensures that the instance does not exceed the 500MB allocated to it
lxc config set instance limits.memory.enforce=hard

# Start the instance
lxc start instance

# To check the set memory
lxc exec instance bash

# After having terminal access inside the container
free -m
```

## Storage management ##

### Containers ###

Containers can be kept in a running state when applying changes to disk size:
```
# Set the disk size
lxc config device set container root size=1GB

# Check the storage
lxc exec instance bash

# After having terminal access inside the container
df -h
```

Container images launch with around 300-600MB of used storage used depending on the OS launched. If the value of the storage set is below the amount used, it will be ignored.

### VMs ###
VM images have their disks initialized by cloud-init and only support increasing the disk size.

VM's must be in a stopped execution state in order for disk settings to be applied:
```
# Stop the VM
lxc stop vm

# Set the disk size
lxc config device set vm root size=25GB

# Start the VM
lxc start vm

# Check the storage
lxc exec instance bash

# After having terminal access inside the container
df -h
```

# Notes on CentOS 8 #
CentOS 8 images do not come prebuilt with cloud-init installed, however there is a variante of the image in LXD that has it available:
```
# To create a CentOS instance with cloud init pre installed
lxc launch images:centos/8/cloud centos -p bridge
```
As of 10/07/2020 CentOS 8 VM images have an issue with the 9p kernel module as can be read [here](https://discuss.linuxcontainers.org/t/latest-centos-8-image-doesnt-work-for-vm-cant-launch-agent/8920) and currently can not execute terminal commands with `lxc exec`.