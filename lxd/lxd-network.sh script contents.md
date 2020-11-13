[TOC]

# Script usage #
This script is used for changing network configurations of LXD instances given the input. It makes use of *cloud-init* to reinitialize network settings.

All Ubuntu images come with cloud-init pre installed, for CentOS we must specify the build as can be seen [here](https://bitbucket.org/asolidodev/ironman/wiki/Managing%20Linux%20containers%20and%20VMs%20via%20CLI#markdown-header-notes-on-centos-8).

## Ethernet interface name ##

The script extracts the ethernet interface name dynamically as it can change between a **eth[0-9]+** notation to an **enp*** depending on whether it is a container or a VM instance. This name is then used to target the network settings to the correct interface.

```
INTERFACE_NAME=$(lxc exec $INSTANCE_NAME -- ip a | grep enp.*: -o | sed 's/.$//')

if [ -z "$INTERFACE_NAME" ]
then
    INTERFACE_NAME=$(lxc exec $INSTANCE_NAME -- ip a | grep eth[0-9] -o | head -1)
fi

```

## cloud-init network configuration file ##
The given parameters for each field of the network is then written inside a `cloud-config` file, this file is then placed inside the configurations folder `/etc/cloud/cloud.cfg.d/` which is processed at the instance's startup time.

```
# NOTE: the #cloud-config tag is required for cloud-init to process the file

#cloud-config
network:
  version: 1
  config:
  - type: physical
    name: $INTERFACE_NAME
    subnets:
      - type: static
        address: $IP_ADDRESS
        netmask: $NETMASK
        gateway: $GATEWAY
        dns_nameservers:
          - $DNS1
```

The file can easily be pushed inside the instance with the `lxc push` function:
```
lxc file push network-config.cfg $INSTANCE_NAME/etc/cloud/cloud.cfg.d/ 
```

The instance then resets its cloud-init setup and reboots. After restarting the instance should have the previously given IP address which can be seen with `lxc list instance_name`.