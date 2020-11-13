[TOC]


# Caveats #

Things to have in mind before starting:

* LXD must not be uninstalled manually through snap as it does not execute a clean install leaving hidden cached files which causes errors when creating LXD resources with names that were previously used.
* LXD does not officially support version downgrading. Doing so will lead to database errors which blocks LXD's service from executing.

# Requirements #

The puppet base configuration requires the following custom modules:

* [is_puppet_base](https://bitbucket.org/asolidodev/is_puppet_base/src/master/) (internal)
* [is_lxd](https://bitbucket.org/asolidodev/ironman/src/master/) (internal)
* [lxd-puppet-module](https://github.com/ye-yng/lxd-puppet-module) (cloned and renamed to lxd)
* [ethanhs-snapd](https://github.com/ye-yng/puppet-snapd) (cloned and renamed to snapd)

and the following standard modules installed directly with the puppet module install command:

* zehweh-netplan
* puppetlabs-firewall
* puppetlabs-apt
* puppetlabs-vcsrepo

Notes:

* The LXD Puppet Module was forked from the original author's [repository](https://github.com/ovh/lxd-puppet-module) to update LXD API calls and importing images from LXD's official image server.

* The snap Puppet module did not implement version upgrade which was added in the forked repository.

* It had to be renamed because puppet does not allow module names with '-'

# LXD Server #

In order to create VM instances with [OVH's LXD Puppet module](https://github.com/ovh/lxd-puppet-module) changes had to be made within the API calls as the modules uses an older version of LXD's API that still requires 2 separate API calls for containers and VMs. The most recent API encases both in an `instances` API call as can be seen  [here](https://lxd.readthedocs.io/en/latest/rest-api/#instances-containers-and-virtual-machines).

In order to specify the type of the instance, a new parameter was added to the container Puppet type that accepts either `container` or `virtual-machine`. The forked repository with the changes can be found [here](https://github.com/ye-yng/lxd-puppet-module).

The official LXD image repository can be found [here](https://uk.images.linuxcontainers.org/images/) and the path must to the image must be given similar to the examples below.

The list for the available LXD snap channels can be found [here](https://snapcraft.io/lxd) and can be placed in the `lxd_version` field. If no value is given, it installs LXD 4.7 as the default option.

Following is an LXD server configuration example, the lines commented out with `#` are optional:
```
node 'lxd-server' {
    include is_puppet_base::node_base
    include passwd_common
    class{ 'is_lxd::lxd_server_base' :
        gw4 => '192.168.1.1',                      # The gateway IP address, must be passed manually
        secret_psswd   => 'ASECRETPASS',             # The password the remote management (UI or other LXD hosts) will need
        lxd_version    => '4.7/stable'                # Defines the version LXD will be installed in. If none is given, 4.7 will be installed by default
        storage_driver => 'YOURCHOSEDRIVER',       # Use zfs when the server is on a physical machine
                                                   # TODO check arguments for when Ceph storage cluster is implemented
        # disk_size    => '10GB',                     # Sets the size of the storage pool to be used
        # dns_servers  => ['8.8.8.8','1.1.1.1'],  # The default DNS servers are 8.8.8.8 and 1.1.1.1 and can be changed to any desired address
    }

    # This firewall rule accepts connections on port 15151 from the UI container's address since the default policy is DROP
    firewall { '200 allow input from UI to host': chain => 'INPUT', dport => 15151, proto => 'tcp', action => 'accept', source => '192.168.1.120', }

    # This rule allows other servers or containers in the local network to access the current server's instances remotely
    firewall { '201 allow remote connection to LXD server': chain => 'INPUT', dport => 8443, proto => 'tcp', action => 'accept', source => '192.168.1.0/24', }


    # Make Ubuntu and Centos container + VM images available

    lxd::image { 'ubuntu2004vm':
        ensure      => 'official-vm', # Required keyword to download the VM image files
        repo_url    => 'https://uk.images.linuxcontainers.org/images',
        image_file  => 'ubuntu/focal/amd64/cloud/20201012_07:42/',
        image_alias => 'ubuntu2004vm'
    }

    lxd::image { 'ubuntu2004':
        ensure      => 'official', # Required keyword to download the Container image files
        repo_url    => 'https://uk.images.linuxcontainers.org/images',
        image_file  => 'ubuntu/focal/amd64/cloud/20201012_07:42/',
        image_alias => 'ubuntu2004',

    }

    lxd::image { 'centos8vm':
        ensure      => 'official-vm', # Required keyword to download the VM image files
        repo_url    => 'https://uk.images.linuxcontainers.org/images',
        image_file  => 'centos/8/amd64/cloud/20201011_07:08/',
        image_alias => 'centos8vm'
    }

    lxd::image { 'centos8':
        ensure      => 'official', # Required keyword to download the VM image files
        repo_url    => 'https://uk.images.linuxcontainers.org/images',
        image_file  => 'centos/8/amd64/cloud/20201011_07:08/',
        image_alias => 'centos8'
    }

    lxd::image { 'ubuntu1804vm':
        ensure      => 'official-vm', # Required keyword to download the VM image files
        repo_url    => 'https://uk.images.linuxcontainers.org/images',
        image_file  => 'ubuntu/bionic/amd64/cloud/20201011_07:42/',
        image_alias => 'ubuntu1804vm'
    }

    lxd::image { 'ubuntu1804':
        ensure      => 'official', # Required keyword to download the Container image files
        repo_url    => 'https://uk.images.linuxcontainers.org/images',
        image_file  => 'ubuntu/bionic/amd64/cloud/20201011_07:42/',
        image_alias => 'ubuntu1804',

    }

    # This container will be used to install LXDUI
    lxd::container { 'lxdui-container':
      state      => 'started',
      type       => 'container',
      profiles   => ['bridge-cli'],
      image      => 'ubuntu2004',
      set_ip     => 'true',
      ip_address => "192.168.1.120",
      netmask    => "255.255.255.0",
      gateway    => "192.168.1.1",
      dns        => ["8.8.8.8","1.1.1.1"],
    }
}
```

# LXD UI Container #
The UI container's IP must be set to be a static address as can be seen above.

Following is an LXD UI Container node configuration example, the lines commented out with `#` are optional:
```
node 'lxdui-container' {
    include is_puppet_base::node_base
    include passwd_common

    # Ensures apt update is run first
    Class['is_puppet_base::node_base']

    # Review later
    # This disables IPv6 from the interfaces which prevents the pip installer from getting stuck
    -> sysctl  { 'net.ipv6.conf.all.disable_ipv6': value => '1'  }

    -> class{ 'is_lxd::lxd_server_ui' :
      # group         => 'somegroup',            # This parameter will determine which user groups are allowed to access the UI. By default it is set to sudo and can be changed to another group.
      remote          => '192.168.1.149',        # The IP address of our remote LXD host
      secret_passwd   => 'ASECRETPASS',          # Must be set according to the password set on the LXD server
    }

    # This firewall rule will allow all machines within the given LAN IP range to access the UI. The source can be changed to fit the network settings
    firewall { '200 allow remote server to access UI': chain => 'INPUT', dport => 15151, proto => 'tcp', action => 'accept', source => '192.168.1.0/24', }

}

```
After running the Puppet agent on the node, the UI can be accessed through a browser at `NODE_IP:15151`.