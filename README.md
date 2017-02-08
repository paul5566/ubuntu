# download iso

open <http://www.ubuntu.com/download/desktop/>
![download_iso](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/download_iso.png "download_iso")

use other software (e.g. ultraiso) to make boot disk if necessary

***
# install system

![install_start](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/install_start.png "install_start")
![select_language](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/select_language.png "select_language")
![select_custom](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/select_custom.png "select_custom")
![select_type](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/select_type.png "select_type")
![select_location](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/select_location.png "select_location")
![select_keyboard](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/select_keyboard.png "select_keyboard")
![select_hostname](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/select_hostname.png "select_hostname")
![install_wait](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/install_wait.png "install_wait")
![install_complete](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/install_complete.png "install_complete")

***
# configure network

bind eth0 to staic address for legacy linux network (not recommended)
>$ sudo vi /etc/network/interfaces

    #auto lo
    #iface lo inet loopback
    auto eth0
    iface eth0 inet static
    address 192.168.0.7
    netmask 255.255.255.0

reset eth0 to static address once for legacy linux network (not recommended)

>$ ifconfig eth0 [ip_address] netmask [ip_netmask] up

add connection in network manager gui mode (recommended)

![ethernet_connection](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/ethernet_connection.png "ethernet_connection")

set connection in network manager config file (strongly recommended)

>$ sudo vi /etc/NetworkManager/system-connections/wifi_ssid

    [connection]
    id=ChinaNet-ouiyeah
    uuid=cb9d0600-2d5f-4430-b874-9aeb67914d2f
    type=802-11-wireless / 802-3-ethernet
    autoconnect=true

    [802-3-ethernet]
    duplex=full
    mac-address=0:1d:72:37:a9:df

    [802-11-wireless]
    ssid=ChinaNet-ouiyeah
    mode=infrastructure
    mac-address=40:E2:30:C3:76:43
    security=802-11-wireless-security

    [802-11-wireless-security]
    key-mgmt=wpa-psk
    psk=Can@jingt0

    [ipv4]
    method=manual / shared
    dns=192.168.0.1;
    addresses1=192.168.0.7/24,192.168.0.1

    [ipv6]
    method=auto

remember to set the link for network connection if failed to visit websites after installation

>$ sudo ln -s /run/resolvconf/resolv.conf /etc/resolv.conf

***
# remote access

set remmina if it is used as terminal
![remmina_client](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/remmina_client.png "remmina_client")
![remmina_add](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/remmina_add.png "remmina_add")

set desktop sharing if it is used as host
![desktop_sharing](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/desktop_sharing.png "desktop_sharing")
![desktop_settings](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/desktop_settings.png "desktop_settings")

set vnc4server for connecting ubuntu from other system terminal (e.g. windows / ubuntu)

>$ sudo apt-get install vnc4server

>$ sudo apt-get install xrdp

>$ sudo apt-get install dconf-editor

open dconf-editor and visit org > gnome > desktop > remote-access
![dconf_editor](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/dconf_editor.png "dconf_editor")
uncheck the “requlre-encryption” attribute

set x11vnc for connecting odroid-ubuntu from windows

>$ sudo apt-get install x11vnc

>$ sudo apt-get install xrdp

>$ sudo vi /etc/init/x11vnc.conf

    start on login-session-start
    script
        x11vnc -display :0 -auth /var/run/lightdm/root/:0 -forever -shared -bg -o /var/log/x11vnc.log -rfbport 5900 -tightfilexfer
    end script

use remote desktop from rdp to vnc
![remote_rdp_vnc](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/remote_rdp_vnc.png "remote_rdp_vnc")

use [tightvnc](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/pkg/tightvnc-2.7.10-setup-64bit.msi) to copy and paste clipboard between windows and linux

use scp to copy files between linux systems and use [pscp](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/pkg/putty.zip) to copy files from or to windows

use [teamviewer](https://www.teamviewer.com/en/download/) to fulfill remote access worldwide

***
# set permissions

cancel sudo password

>$ sudo sed -i -e "/%sudo\s*ALL=(ALL:ALL)\s*ALL/ c %sudo\tALL=(ALL:ALL) NOPASSWD:ALL" /etc/sudoers

use pkexec command if sudo failed

>$ pkexec visudo -f /etc/sudoers

save (ctrl^o + return) and exit (ctrl^x)

change powerbtn event to shutdown immediately

>$ sudo sed -i -e "/action=\/etc\/acpi\/powerbtn.sh/ c action=sudo /sbin/shutdown -h now" /etc/acpi/events/powerbtn

add current user to dialout group for tty authority

>$ sudo usermod -aG dialout $(whoami)

create tty rule file for current user

>$ echo 'KERNEL=="ttyS[0-9]*", MODE="0666"' | sudo tee -a /etc/udev/rules.d/70-persistent-tty.rules

>$ echo 'KERNEL=="ttyUSB[0-9]*", MODE="0666"' | sudo tee -a /etc/udev/rules.d/70-persistent-tty.rules

lookup and bind the device id

>$ udevadm info /dev/ttyUSB0 (e.g. USB0 ID_PATH=pci-0000:00:1a.0-usb-0:1.2:1.0)

![lsusb_lookup](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/lsusb_lookup.png "lsusb_lookup")

>$ echo 'SUBSYSTEM=="tty", ENV{ID_PATH}=="pci-0000:00:1a.0-usb-0:1.2:1.0", SYMLINK+="alias_name(e.g.)"' | sudo tee -a /etc/udev/rules.d/70-persistent-tty.rules

revise grub file in order to skip boot-in check if necessary

>$ sudo sed -i '177s/ ro / rw /' /etc/grub.d/10_linux

>$ sudo update-grub

***
# install softwares

install google input source for ibus (or fcitx)

>$ sudo apt-get install ibus-googlepinyin (or fcitx-googlepinyin)

install basic development toolkits

>$ sudo apt-get install vim ssh htop cutecom setserial imagemagick

install sublime text editor

>$ sudo add-apt-repository ppa:webupd8team/sublime-text-3

>$ sudo apt-get update

>$ sudo apt-get install sublime-text-installer

set git config for user name and email

>$ git config --global user.name $USER

>$ git config --global user.email `hostname`@hitrobotgroup.com

install gitg for git and rapidsvn for svn

>$ sudo apt-get install gitg rapidsvn meld

link svn repository if rapidsvn is failed to get permanent certification

>$ svn list https://10.1.11.10/svn/LaserGPS1 (e.g.)

remove all backup~ files from svn if necessary

>$ find . -name *~ -exec rm {} \;

set "subl" and "meld" in the preference of rapidsvn

***
# auto startup

![startup_applications](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/startup_applications.png "startup_applications")
![edit_preferences](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/edit_preferences.png "edit_preferences")

edit the startup program command as follow if running ros file before calling .bashrc

> gnome-terminal -x bash -c '~/catkin_ws/boot.sh'

***
# change hostname

>$ sudo vi /etc/hostname

    [hostname]

>$ sudo vi /etc/hosts

    127.0.0.1       localhost
    127.0.1.1       [hostname]

***
# remastersys backup

download [remastersys_3.0.3-1_all.deb](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/pkg/remastersys_3.0.3-1_all.deb)
![install_remastersys](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/install_remastersys.png "install_remastersys")
note that remastersys should be re-installed if it is already a remastersys backup system

change "WORKDIR" to custom directory if necessary (e.g. /home/remastersys)

>$ sudo nano /etc/remastersys.conf

do the remastersys backup

>$ sudo remastersys backup

wait for a while and get the generated file <custom-backup.iso> at /home/remastersys/

note that teminate the backup ctrl+c and do the following if the basename warning happened

>$ sudo apt-get remove popularity-contest

>$ sudo apt-get remove ubiquity*

>$ sudo apt-get remove remastersys

>$ sudo apt-get update

>$ sudo apt-get -f install 

use other software (e.g. ultraiso) to make boot disk if necessary
