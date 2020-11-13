[TOC]

# Requirements #
## Server side ##

We need to download both the Windows Server 2019 ISO file and the virtIO drivers. Both must be placed in the `/home/extras/iso/` directory.

The ISO can be found [here](https://www.microsoft.com/en-US/evalcenter/evaluate-windows-server-2019?filetype=ISO) and the virtIO drivers can be downloaded as follows:
```
wget https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/stable-virtio/virtio-win.iso -P /home/extras/iso/
```

## Client side ##

To access our instances through RDP we must install an RDP client. Both Remmina and KRDC work:
```
apt install remmina
apt install krdc
```

# Image creation #

## Base VM creation ##

In order to create a Windows image we need to start by creating a VM from where it will be exported.

Before starting we must access the LXD server with the `-XC` options selected.
```
SERVER_IP=192.168.1.110
ssh -XC user@${SERVER_IP}
```

We must then create an empty VM that will be used to install Windows:
```
# The profile used will be bridge-cli instead of vm-cli since for Windows VMs we will not have the disk initialized by cloud-init
# Note: we must then increase the disk size so that there are no issues during installation (at least 25GB is recommended for testing, at least 100GB recommended for production)

CPU=4
MEMORY=4
STORAGE=25

lxc init wserver19 --empty --vm -c security.secureboot=false -c limits.cpu=$CPU -c limits.memory=${MEMORY}GB -p bridge -s default
lxc config device set wserver19 root size=${STORAGE}GB

```

We then add the ISO and the drivers to the VM's qemu data:
```
# The Windows ISO filename will vary depending on the downloaded version

WINDOWS_ISO=17763.737.190906-2324.rs5_release_svc_refresh_SERVER_EVAL_x64FRE_en-us_1.iso

echo -n "-drive file=/home/extras/iso/${WINDOWS_ISO},index=0,media=cdrom,if=ide -drive file=/home/extras/iso/virtio-win-0.1.173.iso,index=1,media=cdrom,if=ide" | lxc config set wserver19 raw.qemu -
```

Finnaly we grant access to the VM so it can read and write data to both files:
```
lxc config set wserver19 raw.apparmor "/home/** rk,"
```

## Windows Installation ##
Start the VM and immediately hit ESC as soon as any text prompt appears so we can enter the firmware menu:
```
lxc start wserver19 --console

# Immediately hit ESC repeatedly
```
If we fail to get into the menu, simply stop the VM with `lxc stop wserver19 --force` and run the command above.

On the boot menu use the arrow keys to select the **Boot Manager** and press Enter:

![boot_menu.png](https://bitbucket.org/repo/X577Lde/images/176055836-boot_menu.png)

On the Boot Manager menu select the **QM00001** drive and press enter multiple times to answer an invisible prompt. If the terminal stays empty after a few seconds then the operation succeeded and we can exit the console by pressing Ctrl+A and then Q:

![boot_drive.png](https://bitbucket.org/repo/X577Lde/images/1981928746-boot_drive.png)

After the boot drive is selected we can then proceed to the installation of Windows Server. We must first open a graphical interface:
```
lxc console wserver19 --type=vga
```

After getting access to the graphical interface, we must go through Windows Server's setup process. To escape the mouse from remote-viewer press Ctrl+Alt.

Select the language, time and keyboard settings, press Next and Install now.

On the setup page select Windows Server 2019 Standard Evaluation (Desktop Experience), press next accept the license and press next again:

![desktop_exp.png](https://bitbucket.org/repo/X577Lde/images/247850905-desktop_exp.png)

Choose the **Custom installation** type:

![custom.png](https://bitbucket.org/repo/X577Lde/images/3316333027-custom.png)

On the driver selection page, press Load driver:

![select_driver.png](https://bitbucket.org/repo/X577Lde/images/2390571751-select_driver.png)

Answer OK to the prompt:

![prompt_ok.png](https://bitbucket.org/repo/X577Lde/images/1499051157-prompt_ok.png)

Choose the **E:\amd64\w10\vioscsi.inf** controller and press next:

![controller.png](https://bitbucket.org/repo/X577Lde/images/3822174328-controller.png)

If the drive's Type is **Offline**, press the warning text below, select OK within the popup prompt and press next:

![offline.png](https://bitbucket.org/repo/X577Lde/images/2822502820-offline.png)

Windows will now begin installing:

![installing.png](https://bitbucket.org/repo/X577Lde/images/3321309960-installing.png)

After the installation process ends, the VM will reboot. We will have to get graphical console access again through remote-viewer to proceed with the installation process.

## Windows setup ##
After regaining console access, we must first input the Admin user password:

![admin_pass.png](https://bitbucket.org/repo/X577Lde/images/2186254199-admin_pass.png)

We will then enter Windows' login screen. Since we can't send a Ctrl+Alt key in remote viewer, we must user remote-viewer to send the Ctrl+Alt+Delete key signal under the Send key menu:

![unlock_login.png](https://bitbucket.org/repo/X577Lde/images/155229855-unlock_login.png)

After logging in with the previously defined password, we must install the virtIO drivers so our VM has internet access.

To install the drivers select `File Explorer > This PC > CD Drive (E:) > virtio-win-gt-x64` and double click to execute:

![driver_install.png](https://bitbucket.org/repo/X577Lde/images/1709034249-driver_install.png)

Accept all the prompts until driver installation is finished. We should now have internet access in our VM and a LAN IP address if the `bridge-cli` profile was used:

![ip_address.png](https://bitbucket.org/repo/X577Lde/images/1010063521-ip_address.png)

### RDP access ###
In order to connect to our Windows VM's graphical interface remotely, we must setup RDP access.

We can enable RDP access through our Server Manager. Select Start and then Server Manager:

![server_manager.png](https://bitbucket.org/repo/X577Lde/images/3254448077-server_manager.png)

Inside Server Manager select Local Server:

![disabled.png](https://bitbucket.org/repo/X577Lde/images/1158874510-disabled.png)

We can see that Remote Desktop is set to **Disabled**. Press the **Disabled** text and a System Properties prompt will appear:

![disabled_prompt.png](https://bitbucket.org/repo/X577Lde/images/3267280069-disabled_prompt.png)

Select **Allow remote connections to this computer** and click **Windows Firewall with Advanced Security** on the prompt:

![firewall_select.png](https://bitbucket.org/repo/X577Lde/images/1706642032-firewall_select.png)

In the firewall page select Inbound Rules and scroll down to **Remote Desktop - User Mode (TCP-in)** and Enable Rule:

![firewall_rule.png](https://bitbucket.org/repo/X577Lde/images/3623625099-firewall_rule.png)

Close the firewall application, select OK on the prompt and OK in System Properties. Remote Desktop should now be enabled:

![remote_enabled.png](https://bitbucket.org/repo/X577Lde/images/3903131605-remote_enabled.png)

To test that RDP is Enabled we can use Remmina to connect to our VM's graphical interface.

Open Remmina and click the top left corner to add a new connection:

![remmina.png](https://bitbucket.org/repo/X577Lde/images/573453426-remmina.png)

Enter the settings accordingly. The server will be the VM's IP address which can be checked by running ipconfig. The username will be Administrator and the password will be the one previously defined:

![remmina_settings.png](https://bitbucket.org/repo/X577Lde/images/2622744367-remmina_settings.png)

Press save and Connect. We should now be connected to our VM:

![remmina_connected.png](https://bitbucket.org/repo/X577Lde/images/2223060109-remmina_connected.png)

### SSH access ###
To enable SSH access for our Windows VMs, we must first download the SSH server application.

Press Start and go to Settings:

![start_settings.png](https://bitbucket.org/repo/X577Lde/images/69353003-start_settings.png)

Press the Apps menu:

![setting_apps.png](https://bitbucket.org/repo/X577Lde/images/533407122-setting_apps.png)

Press Manage optional features and then Add a feature:

![add_feature.png](https://bitbucket.org/repo/X577Lde/images/578548825-add_feature.png)

Scroll down to OpenSSH Server and press Install:

![openssh_install.png](https://bitbucket.org/repo/X577Lde/images/1665289056-openssh_install.png)

After installation ends, go to Start, type Services and select the first App:

![services.png](https://bitbucket.org/repo/X577Lde/images/2128312575-services.png)

Scroll down to OpenSSH SSH Server, right click and select properties:

![ssh_properties.png](https://bitbucket.org/repo/X577Lde/images/4001741473-ssh_properties.png)

Set the Startup type to Automatic and press Start:

![start_ssh.png](https://bitbucket.org/repo/X577Lde/images/3180365553-start_ssh.png)

We can now access our VM through SSH:

![ssh_access.png](https://bitbucket.org/repo/X577Lde/images/3752309401-ssh_access.png)

![ssh_win.png](https://bitbucket.org/repo/X577Lde/images/3770749746-ssh_win.png)


## Exporting the Windows Image ##
After granting access to both SSH and RDP we can now ready the image to be exported.

Press Start, search for `run` and select the first application:

![run.png](https://bitbucket.org/repo/X577Lde/images/3979826907-run.png)

On the Run application, search for `sysprep` and press OK:

![run_sysprep.png](https://bitbucket.org/repo/X577Lde/images/371402066-run_sysprep.png)

Run the executable inside the folder:

![sysprep_exec.png](https://bitbucket.org/repo/X577Lde/images/180590404-sysprep_exec.png)

Select Out-of-Box Experience (OOBE), Generalize and Shutdown:

![sysprep_options.png](https://bitbucket.org/repo/X577Lde/images/2457806683-sysprep_options.png)

The VM will shutdown after Sysprep finishes executing, after which we can remove the raw qemu data added intially for our ISO and virtIO drivers:
```
lxc config unset wserver19 raw.qemu
```

We can now publish our VM's image:
```
# Note: This process may take a few minutes as LXD tries to compress the qemu image
lxc publish wserver19 --alias wserver19
```

After publishing we should be able to see our image in the image list:
```
$ lxc image list
+-----------+--------------+--------+---------------------------------------------+--------------+-----------------+-----------+-------------------------------+
|   ALIAS   | FINGERPRINT  | PUBLIC |                 DESCRIPTION                 | ARCHITECTURE |      TYPE       |   SIZE    |          UPLOAD DATE          |
+-----------+--------------+--------+---------------------------------------------+--------------+-----------------+-----------+-------------------------------+
| wserver19 | cee305252934 | no     |                                             | x86_64       | VIRTUAL-MACHINE | 5357.71MB | Nov 10, 2020 at 12:13pm (UTC) |
+-----------+--------------+--------+---------------------------------------------+--------------+-----------------+-----------+-------------------------------+

```