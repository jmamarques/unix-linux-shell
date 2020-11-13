[TOC]

# Instance minimum requirements #

For the tested instance flavours which are:

* Ubuntu 20.04
* Ubuntu 18.04
* Centos 8

All of the instances use between 40 to 80MB of memory on launch without any additional processes running.

We have the following minimum requirements for a minimal instance setup:

## Containers ##
### Ubuntu 18.04 ###

* **CPU** : 1
* ** Memory ** : 200MB
* ** Storage ** : 200MB

### Ubuntu 20.04 ###
* **CPU** : 1
* ** Memory ** : 200MB
* ** Storage ** : 100MB

### CentOS 8 ###

* **CPU** : 1
* ** Memory ** : 200MB
* ** Storage ** : 200MB

## VMs ##

Virtual machines use cloud-init to initialize the disk and have a default size of 10GB which can not be shrunk.

### Ubuntu 18.04 ###

* **CPU** : 1
* ** Memory ** : 500MB
* ** Storage ** : 10GB

### Ubuntu 20.04 ###
* **CPU** : 1
* ** Memory ** : 500MB
* ** Storage ** : 10GB

### CentOS 8 ###

Currently LXD Centos8 VM images are not working properly and issues have been documented [here](https://discuss.linuxcontainers.org/t/latest-centos-8-image-doesnt-work-for-vm-cant-launch-agent/8920/16).

* **CPU** : 1
* ** Memory ** : 500MB
* ** Storage ** : 10GB

# Instance creation on LXD Server node #
To create instances we must first have the base LXD installed through Puppet and the default images which is documented [here](https://bitbucket.org/asolidodev/ironman/wiki/Puppet%20based%20LXD%20installation#markdown-header-lxd-server).

The keywords for instance `type` must either be `container` or `virtual-machine`.

To check the instance configuration key values refer to the official [documentation](https://lxd.readthedocs.io/en/latest/instances/).
```
node 'lxd-server' {
    ...
    # LXD server initial setup
    ...

    # VM instance creation
    lxd::container { 'ubuntu-vm':
      state    => 'started',
      type     => 'virtual-machine',    # Keyword to identify the type of the instance
      profiles => ['vm-cli'],               # Profile to be used with all VM instances
      devices => {
        'root' => {
          'path' => '/',
          'pool' => 'default',
          'size' => '30GB',
          'type' => 'disk',
        },
      },
      image    => 'ubuntu2004vm',       # Alias of previously imported image
    }

    # Container instance creation
    lxd::container { 'ubuntu-container':
      state    => 'started',
      type     => 'container',     # Keyword to identify the type of the instance
      profiles => ['bridge-cli'],      # Profile to be used with all Container instances
      # Example configuration for container, exposes 2 physical CPU cores and limits memory to 700MB
      config => {
        'limits.cpu' => '2',
        'limits.memory' => '700MB',
      },
      devices => {
        'root' => {
          'path' => '/',
          'pool' => 'default',
          'size' => '3GB',
          'type' => 'disk',
        },
      },
      image    => 'ubuntu2004',    # Alias of previously imported image
    }
}
```

## Setting instance IP ##
In order to set the IP of an instance, the `set_ip` must be set to `true` and the following fields must be provided:

* New static IP address
* Netmask
* The instance's gateway address
* DNS Server(s)

```
lxd::container { 'static-ip-container':,
  state => 'started',
  type  => 'container',
  profiles => ['bridge-cli'],
  image => 'ubuntu2004',
  set_ip => 'true',
  ip_address => "192.168.1.120",
  netmask => "255.255.255.0",
  gateway => "192.168.1.1",
  dns => ["8.8.8.8","1.1.1.1"],
}

```

# Instance deletion  on LXD Server node #

The fields `type` and `image` are not required to delete an instance as only the name is needed, however the module defines those fields as mandatory.

```
node 'lxd-server' {
    ...
    # LXD server initial setup
    ...

    lxd::container { 'ubuntu-container':
      ensure => 'absent',
      type  => 'container',
      image => 'ubuntu2004',
    }

    lxd::container { 'ubuntu-vm':
      ensure => 'absent',
      type  => 'virutal-machine',
      image => 'ubuntu2004vm',
    }
}
```

# Instance resource management #
To update the resources used by an instance we simply need to add or change the desired [keys and values](https://lxd.readthedocs.io/en/latest/instances/) to the `config` parameter.
```
node 'lxd-server' {
    ...
    # LXD server initial setup
    ...

    # Assuming the instance was created previously

    lxd::container { 'ubuntu-container':
      type  => 'container',
      profiles => ['bridge'],
      config => {
        'limits.cpu' => '1',
        'limits.memory' => '900MB',
      },
      image => 'ubuntu2004',
    }

    # Example configuration to change disk limit
    lxd::container { 'ubuntu-vm':
      type  => 'container',
      profiles => ['bridge'],
      config => {
        'limits.cpu' => '1',
        'limits.memory' => '900MB',
      },
      device => {
        'root' => {
          'path' => '/',
          'pool' => 'default',
          'size' => '5GB', # define the size limit of the disk
          'type' => 'disk',
        }
      }
      image => 'ubuntu2004vm',
    }

}
```

Following are the requirements and expected behaviour of LXD instances when changing the given resources.

|             | Container behaviour | VM behaviour     | Command used to check |
| :---        |    :----:   |          :---: | :---: |
| Change CPUs | Changes are automatically applied to the container without interruption. | For CPUs to be changed the VM must be stopped first via CLI or UI - otherwise there will be an error in the puppet run. The new values will be available once the machine is started, after the puppet run. | lxc exec instance-name -- grep -c ^processor /proc/cpuinfo |
| Change Memory | Changes are automatically applied to the container without interruption. | As of LXD 4.7, decreasing the memory below the value at initial boot and increasing it back up until the same value can be done without stopping the VM . However to increase the memory beyond this threshold the VM must be stopped first via CLI or the UI - otherwise there will be an error in the puppet run. The new values will be available once the machine is started, after the puppet run. | lxc exec instance-name -- free -m|
| Increase disk space | Changes are automatically applied to the container without interruption. | For disk size to be increased the VM must be stopped firstvia CLI or UI. Otherwise there will be an error in the puppet run.  The new values will be available once the machine is started, after the puppet run.| lxc exec instance-name -- df -h |
| Decrease disk space | Changes are automatically applied to the container without interruption. If decreased below current disk usage, the new value is ignored. | VM disks do not allow decreasing disk space. | lxc exec instance-name -- df -h |