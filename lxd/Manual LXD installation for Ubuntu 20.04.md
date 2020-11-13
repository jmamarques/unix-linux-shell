[TOC]

# Caveats #

Things to have in mind before starting:

* LXD must not be uninstalled manually through snap as it does not execute a clean install leaving hidden cached files which causes errors when creating LXD resources with names that were previously used.
* LXD does not officially support version downgrading. Doing so will lead to database errors which blocks LXD's service from executing.

# LXD Installation #
It is advised to install LXD through the Snap package manager on Ubuntu, it can be installed by running the following command:

```
snap install lxd
```

This will install the latest stable release of LXD on the current machine (LXD 4.0 in the case of Ubuntu 20.04). After installing, run the following command to configure LXD and follow the prompts shown on the command line:

```
lxd init
```

More information regarding each option can be found [here](https://linuxcontainers.org/lxd/getting-started-cli/#initial-configuration).

## remote-viewer ##
In order to initialize Windows VMs we must have remote-viewer installed to access the VM's graphical interface for the initial setup:
```
apt install virt-viewer
```

For LXD to detect the newly installed remote-viewer we must reload its service:
```
systemctl reload snap.lxd.daemon
```

## Ubuntu bridge configuration ##
In order for containers and VMs created to have a LAN IP, we must first configure a bridge so we can add it as a device inside an LXD profile. On Ubuntu, network can be managed by using netplan by changing the YAML configuration file located at `/etc/netplan/01-network-manager-all.yaml`. The name may have slight variation regarding the number but it should be the only file within the directory.

Before any changes are made, it is advised to make a backup of the original configuration file to reset network configuration if needed.

We first have to determine our ethernet interface name,gateway IP and the current machine's IP:
```
# The ethernet interface should be named similar to enp0s31f6

# Get the gateway IP
ip route | grep default

# Get the machine's IP, it will be under the ethernet interface defined as inet
ip a
```

After extracting the information needed we should have something similar to the following:

* **IP Address**: 192.168.1.11/24
* **Gateway Address**: 192.168.1.1
* **Ethernet Interface**: enp0s31f6

We then have to input this information into the configuration file:
```
# As stated before, it is advised to create a backup for this file before any modifications
sudo nano /etc/netplan/01-network-manager-all.yaml
```

The configuration file should look like this:
```
# Here we defined our bridge to be called br0

network:
 version: 2
 renderer: networkd
 ethernets:
   enp0s31f6:
     dhcp4: false
 bridges:
   br0:
     interfaces: [enp0s31f6]
     dhcp4: false
     addresses: [192.168.1.11/24]
     gateway4: 192.168.1.1
     nameservers:
       addresses: [8.8.8.8]

# Input the data accordingly then Save and Exit
```

After editing, the config file has to be applied and the machine rebooted:
```
sudo netplan apply
reboot
```

Our new network configuration in `ifconfig` should look similar to this:
```
br0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.11  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fe80::e2d5:5eff:fe2c:65f4  prefixlen 64  scopeid 0x20<link>
        ether 42:f2:11:11:30:1b  txqueuelen 1000  (Ethernet)
        RX packets 61725  bytes 82404264 (82.4 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 27703  bytes 4187003 (4.1 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

enp0s31f6: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        ether e0:d5:5e:2c:65:f4  txqueuelen 1000  (Ethernet)
        RX packets 70572  bytes 84852844 (84.8 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 28224  bytes 4360682 (4.3 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device interrupt 16  memory 0xef300000-ef320000  

```

## LXD UI bridge profile ##

Since LXDUI does not currently support disk or device management, we must create profiles with the root disk predefined as a temporary solution.

### Container bridge profile ###
After creating the bridge on our host, we then want to attach it to an LXD profile so that container instances can be easily launched with a LAN IP:
```
# Create the profile
lxc profile create bridge

# Add our br0 bridge to the profile and name it eht0 for containers
lxc profile device add bridge eth0 nic nictype=bridged parent=br0 name=eth0
```

We also have to apply the disk configurations for the profile so we do not need to initialize the instance with the default profile:

```
lxc profile device add bridge root disk pool=default path=/
```

This profile can then be applied to any container instance launched with LXD.

### VM bridge profile ###
For VMs the setup is similar however we need an initial `cloud-init` so that network, storage and other crucial components of the VM are initialized:

```
# Create the profile
lxc profile create vm

# Add disk device to the profile
lxc profile device add vm config disk source=cloud-init:config

# Add the bridge to the profile
lxc profile device add vm eth0 nic nictype=bridged parent=br0 name=eth0

# Add the root disk to the profile so we need not use the default profile's disk configuration
lxc profile device add vm root disk pool=default path=/
```

This profile is to be applied equally as the container profile.

## LXD CLI bridge profile ##

In order to be able to manage instance's disk sizes, we must create profiles that do not include the root disk as these can not be managed and must be overridden to the instance's `devices` section in order to apply any changes.

### Container bridge profile
Similar to UI bridge profile creation, we only need to omit the addition of a root disk within our profile:
```
# Create the profile
lxc profile create bridge-cli

# Add our br0 bridge to the profile and name it eht0 for containers
lxc profile device add bridge-cli eth0 nic nictype=bridged parent=br0 name=eth0
```

### VM bridge profile
Also similar to UI VM profile, we only remove the addition of the root disk, however the cloud-init disk initialization must be added to the profile:
```
# Create the profile
lxc profile create vm-cli

# Add disk device to the profile
lxc profile device add vm-cli config disk source=cloud-init:config

# Add the bridge to the profile
lxc profile device add vm-cli eth0 nic nictype=bridged parent=br0 name=eth0
```

# AdaptiveScale LXDUI#

Before the installation we must enable network access for our host, as well as a password, these will be needed for the containers to access our host:
```
lxc config set core.https_address [::]
lxc config set core.trust_password secret
```


## Container Installation ##
To install LXDUI inside a container we must first create it:
```
lxc launch ubuntu:20.04 lxdui-container -p bridge
```

After launching we must get terminal access and install the dependencies [here](https://github.com/AdaptiveScale/lxdui/wiki/Installing-the-Prerequisites):
```
lxc exec lxdui-container bash

# After getting terminal access
apt update
apt install -y git build-essential libssl-dev python3-venv python3-pip python3-dev bridge-utils
```

We must then add our host as a remote LXD server for our container so it can access and manage the containers remotely:
```
# After executing, a prompt will come up asking for the password, in our case it will be 'secret' as defined above
lxc remote add host HOST_IP_ADDRESS

# Check the host's containers
lxc list host:
```

After installing the dependencies we must then clone the LXDUI repository and switch to a [branch](https://github.com/AdaptiveScale/lxdui/pull/252) in which remote LXD access was implemented and remote terminal access was fixed:
```
git clone https://github.com/ye-yng/lxdui.git
cd lxdui
git checkout dev_pam
```
### \_\_metadata\_\_.py file changes ###
To enable remote access to LXD, we must input the IP address of our host in the metadata file and disable SSL as it throws errors due to it not being verified:
```
nano app/__metadata__.py

# Once inside this file navigate to the three commented lines
#   - lxdui.lxd.remote
#   - lxdui.lxd.sslverify
#   - lxdui.lxd.remote.name
#
# Uncomment the three lines and in front of remote add the host machines IP address
#   - E.g.: lxdui.lxd.remote = https://HOST_IP_ADDRESS:8443
#
# Add false in front of ssl verify
#   - E.g.: lxdui.lxd.sslverify = false
#
# Leave the remote name as is since the default is the equal to the one we previously defined (host)
#
# Save and Exit
```

The `lxdui.lxd.remote.name` field introduced in this PR is used to access terminals remotely as previously LXDUI tried to access local containers when using the terminal functionality. This is done by running the `lxc exec` command as follows: `lxc exec REMOTE_NAME:instance -- /bin/bash`, the field before the colon `:` indicates the remote LXD server name, which in our case is named **host**.

After performing the required changes we can then install the UI:
```
python3 setup.py install
```


SSL verification causes issues when executing the UI therefore it was disabled. Since this connection is only between the container and its host, there are no apparent security risks.
Since SSL verification is not done, Python warnings will come up when launching the UI, to lessen the clutter in our terminal we can execute the following:
```
export PYTHONWARNINGS="ignore:Unverified HTTPS request"

```

## Authentication ##
In the fork used to develop LXDUI extra features, PAM authentication was implemented. All users with access to LXDUI's (at `/etc/shadow`) can access the UI with their respective credentials.

## Execution ##
After completing the installation the UI can be started through the CLI by running:
```
lxdui start
```

The UI will run on the container's localhost address, to access it through our host we must first determine the conainer's IP address:
```
# Open a new terminal on the Host machine
lxc list container-name

# Check the IPV4 address of the container and enter it in the browser on port 15151
#   - E.g.: 10.250.5.40:15151

# Default username: admin
# Default password: admin
```