# Windows VM creation #
We will assume that SSH access to the LXD Server has been granted as follows:
```
# The C option compresses all the data which makes accessing the remote-viewer through SSH much faster
ssh -XC user@server_address
```

To create a VM from an existing Windows Server image we need only create an instance with the required resources:
```
# Here we will assume that the image is named wserver19

CPU=4
MEMORY=4
STORAGE=100
NAME=windows-test

lxc init wserver19 $NAME --vm -c security.secureboot=false -c limits.cpu=$CPU -c limits.memory=${MEMORY}GB -p bridge -s default
lxc config device set win10 root size=${STORAGE}GB
lxc start $NAME
```

We must then use lxc's console in order to perform the initial Windows setup and create the Administrator user so we can have both RDP and SSH access:
```
# This export must be done so that X11 forwarding is correctly detected when launching a console
export XAUTHORITY=$HOME/.Xauthority

lxc console $NAME --type=vga

# The VM will finish some initial setup and restart so we will have to get console access again after it disconnects

lxc console $NAME --type=vga
```

Choose the desired default settings and press Next:

![settings.png](https://bitbucket.org/repo/X577Lde/images/2472122692-settings.png)

Accept the License Terms and enter the password for the Admin user:

![admin_user.png](https://bitbucket.org/repo/X577Lde/images/4176671225-admin_user.png)

We can now exit the remote-viewer interface.

LXD uses ARP and NDP neighbour cache to match IP addresses to the instance's MAC addresses. In order for the VM's IP address to be displayed under `lxc list` we must have some communication between the host and the VM. We will perform host discovery using `nmap` to only check for online hosts and not their ports to decrease network traffic:
```
NETWORK='192.168.1.0/24'

nmap -sn $NETWORK
```

After nmap finishes the host discovery process, the IP of the VM should be listed under `lxc list`. We can then access the VM with RDP using the credentials defined above.