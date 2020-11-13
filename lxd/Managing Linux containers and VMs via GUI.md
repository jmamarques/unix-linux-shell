[TOC]

# Installing and starting LXDUI
Refer [here](https://bitbucket.org/asolidodev/ironman/wiki/LXD%20installation%20for%20Ubuntu%2020.04#markdown-header-adaptivescale-lxdui) for installation instructions

Here we assume that the bridge **br0** and the profile **bridge** have been created.

To sign in the UI use the default credentials:

* **Username**: admin
* **Password**: admin

# Container/VM creation
To create the container press the **New Instance** button:

![new_instance.png](https://bitbucket.org/repo/X577Lde/images/2573666454-new_instance.png)

Select the image and change any other settings required, and at Profiles choose only the **bridge** profile:

![instance_settings.png](https://bitbucket.org/repo/X577Lde/images/1593986682-instance_settings.png)

After creating the instance, in this case **c2**, if the network uses DHCP then the container will have an IP from the LAN assigned to it:

![containers.png](https://bitbucket.org/repo/X577Lde/images/1857219127-containers.png)

To change the container's network settings follow the instructions [here](https://bitbucket.org/asolidodev/ironman/wiki/Managing%20Linux%20containers%20and%20VMs%20via%20GUI#markdown-header-instance-network-settings).

# Instance network settings #
Since the bridge network is not managed by LXD, the GUI does not offer a direct way to change the network configuration of the instances. To change these settings we must manually set them by getting console access to the containers, by first selecting the desired container (in this case **c2** for example):

![container_select.png](https://bitbucket.org/repo/X577Lde/images/443633372-container_select.png)

Select the **Terminal** option at the top right of the screen:

![select_term.png](https://bitbucket.org/repo/X577Lde/images/2323847511-select_term.png)

The terminal window:

![terminal.png](https://bitbucket.org/repo/X577Lde/images/4076260874-terminal.png)

## Ubuntu network configuration ##
Instructions on how to setup network configurations with **Neplan** on Ubuntu can be found [here](https://linuxconfig.org/how-to-configure-static-ip-address-on-ubuntu-18-10-cosmic-cuttlefish-linux#h6-2-ubuntu-server).

## CentOS network configuration ##
Instructions on how to setup network configurations with **nmcli** on CentOS can be found [here](https://linuxconfig.org/rhel-8-configure-static-ip-address#h3-identifying-our-interface)

# Resource allocation #
Refer to the table [here](https://bitbucket.org/asolidodev/ironman/wiki/Managing%20Linux%20containers%20and%20VMs%20via%20Puppet#markdown-header-instance-resource-management) before changing instance resources.

## Memory and CPU allocation ##
To access the memory and CPU settings we must first select our container (c2 in this case):

![container_select.png](https://bitbucket.org/repo/X577Lde/images/1049597398-container_select.png)

After selecting we then proceed to the **Advanced** tab and change the desired fields and **Save**. More information on each field can be found [here](https://bitbucket.org/asolidodev/ironman/wiki/Managing%20Linux%20containers%20and%20VMs%20via%20CLI#markdown-header-instance-resource-management):

![container_configs.png](https://bitbucket.org/repo/X577Lde/images/3083462231-container_configs.png)

## Storage configurations ##
Currently the UI does not allow to set a disk limit for instances.