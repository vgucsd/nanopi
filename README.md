# NanoPi Setup Guide

Victor Gandarillas <vgandari@eng.ucsd.edu>

<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [NanoPi Setup Guide](#nanopi-setup-guide)
	- [Install UbuntuCore](#install-ubuntucore)
	- [Initializing NanoPi Neo After Installing Ubuntu-core](#initializing-nanopi-neo-after-installing-ubuntu-core)
	- [Test Internet Connection](#test-internet-connection)
	- [Set Up Mirrors List](#set-up-mirrors-list)
	- [Install Git](#install-git)
	- [Install ROS](#install-ros)
		- [Set up Keys](#set-up-keys)
		- [Install Base Packages](#install-base-packages)
	- [Configure WiFi Connection](#configure-wifi-connection)
	- [Configure Static IP](#configure-static-ip)
		- [Configure network interface](#configure-network-interface)
		- [Configure DNS servers](#configure-dns-servers)
		- [Add DNS servers to network interfaces file](#add-dns-servers-to-network-interfaces-file)
		- [Restart network card](#restart-network-card)
	- [Common Issues](#common-issues)
	- [Troubleshooting](#troubleshooting)
	- [References](#references)

<!-- /TOC -->

 ## Revision History

Date       | Modification
---------- | ------------------------------------------------------------------------------------------------------------------
2017.02.14 | Initial Release
2017.02.22 | Moved Git, ROS installation to front; rely on Ethernet connection for installation prior to configuring WiFi; removed instructions for connecting manually to drnenet

This guide is based on the official NanoPi Neo Wiki.

<http://wiki.friendlyarm.com/wiki/index.php/NanoPi_NEO>

In this guide, NanoPi Neo runs Ubuntu 16.04.1 xenial.

## Install UbuntuCore

1. Download `nanopi-neo-core-qte-sd4g.img.zip.img.zip` from <https://www.mediafire.com/folder/n5o8ihvqhnf6s/Nanopi-NEO>]. Use "official-ROMs".
2. Extract (unzip) the image. It should have a `.img.zip` extension before extraction and a `.img` extension after extraction.
3. Open Disks application.
4. Remove partitions from SD card and format as MBR. Make sure to overwrite existing data (slow). **_CAUTION:_** IT IS OF VITAL IMPORTANCE THAT YOU CHOOSE THE CORRECT DRIVE. WITH ADMINISTRATOR PRIVILEGES, IT IS POSSIBLE TO FORMAT ALL THE NON BOOTABLE PARTITIONS ON THE MAIN DRIVE. DON'T DO THIS.
5. Restore disk image using gear icon at top right of Disks window.

## Initializing NanoPi Neo After Installing Ubuntu-core

**_NOTE:_** Go to [Initializing NanoPi Neo After Setting Up Connections and Static IP](#initializing-nanopi-neo-after-setting-up-connections-and-static-ip) if NanoPiNeo is set up to establish connections on its own.

1. Insert mini-SD card into NanoPi Neo. **_NOTE:_** mini-SD card must be in NanoPi Neo _before_ power is applied.
2. Plug in antenna dongle into desktop, connect to dronenet
3. Connect NanoPi to router via Ethernet
4. Plug in grey dongle into NanoPi Neo
5. Connect laptop to router. Connect to dronenet via Ethernet and UCSD-PROTECTED via WiFi. This will enable you to connect the NanoPi Neo to the internet and download ROS. **_NOTE:_** Internet access on NanoPi Neo lasts until laptop goes to sleep.
6. Plug in NanoPi Neo USB cable for power.
7. Go to <http://192.168.0.1/> in desktop browser
8. username: admin, password: admin
9. Verify that NanoPi Neo shows up in list of wired clients (MAC Address: FA:BE:6A:EF:51:3F).
10. Write down NanoPi Neo IP address (e.g. 192.168.0.113)
11. `ssh` into NanoPi Neo:

```
ssh root@192.168.0.XXX
password: fa

The authenticity of host '192.168.0.XXX (192.168.0.XXX)' can't be established.
ECDSA key fingerprint is XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX.
Are you sure you want to continue connecting (yes/no)? yes
```

## Test Internet Connection

Check that there is a route to the internet and update repositories list:

```
$ ping www.google.com
$ sudo apt-get update
```

## Set Up Mirrors List

Download the Ubuntu-core image to run Ubuntu 16.04.1 xenial.

**_NOTE:_** If you download the core image instead of the Ubuntu core image, you will run Ubuntu 15.10 wily.

Update Ubuntu sources repository list:

```
$ sudo vi /etc/apt/sources.list
```

Make sure all non-`xenial` mirrors are commented out and all `xenial` repositories (restricted, universe, and multiverse) are uncommented. Otherwise, the following error will result after the next commands:

```
Temporary failure resolving 'ports.ubuntu.com'
```

## Install Git

Update package list and upgrade packages. Install git (use `git-core`, not `git` (more info [here](http://git.661346.n2.nabble.com/git-core-vs-git-package-on-ubuntu-td7576083.html), [here](http://stackoverflow.com/questions/16164261/apt-get-does-not-find-package-git-on-ubuntu-12-04), and [here](http://askubuntu.com/questions/14685/what-does-package-package-has-no-installation-candidate-mean)):

```
$ sudo apt-get update && sudo apt-get upgrade
$ apt-get install git-core
```

**_NOTE:_** This is what happens if one attempts to use `git` instead of `git-core`:

```
Package git is not available, but is referred to by another package.
This may mean that the package is missing, has been obsoleted, or
is only available from another source
However the following packages replace it:
  git-svn git-el

E: Package 'git' has no installation candidate
```

## Install ROS

This guide for setting up ROS Kinetic is based on this guide:

[Installing Kinetic in Ubuntu](http://wiki.ros.org/kinetic/Installation/Ubuntu)

Copy one of the commands from the [ROS Mirrors](http://wiki.ros.org/ROS/Installation/UbuntuMirrors) list into the Terminal. Replace `$DISTRIB_CODENAME` with `xenial`. Also change

```
$ sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```

to

```
$ sudo sh -c '. /etc/lsb-release && echo "deb http://mirror.umd.edu/packages.ros.org/ros/ubuntu xenial main" > /etc/apt/sources.list.d/ros-latest.list'
```

### Set up Keys

```
$ sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key 421C365BD9FF1F717815A3895523BAEEB01FA116
```

Expected output:

```
Executing: /tmp/tmp.nqeK76xy64/gpg.1.sh --keyserver
hkp://ha.pool.sks-keyservers.net:80
--recv-key
421C365BD9FF1F717815A3895523BAEEB01FA116
gpg: requesting key B01FA116 from hkp server ha.pool.sks-keyservers.net
gpg: key B01FA116: public key "ROS Builder <rosbuild@ros.org>" imported
gpg: Total number processed: 1
gpg:               imported: 1
```

### Install Base Packages

```
$ sudo apt-get update
$ sudo apt-get install ros-kinetic-ros-base
```

Possible error:

```
E: dpkg was interrupted, you must manually run 'sudo dpkg --configure -a' to correct the problem.
```

Run `$ sudo dpkg --configure -a` and run `$ sudo apt-get install ros-kinetic-ros-base` again.

Initialize rosdep:

```
$ sudo rosdep init
$ rosdep update
$ sudo rosdep fix-permissions
$ rosdep update
```

Set up Environment

```
$ echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
$ source ~/.bashrc
```

Get rosinstall

```
$ sudo apt-get install python-rosinstall
```

Ensure that everything is up to date:

```
$ sudo apt-get update && sudo apt-get upgrade
```

<!-- ## Connect to dronenet Manually If you would like to connect to dronenet manually, type ~~~~ $ wpa_passphrase dronenet >> /etc/wpa_supplicant.conf ...type in the passphrase and hit enter... ~~~~ No password, just hit enter. -->

## Configure WiFi Connection

Store hostname and configure WiFi:

```
$ vi /etc/hosts
127.0.1.1    FriendlyARM
$ ip link show wlan0
$ sudo ip link set wlan0 up
```
If `$ sudo ip link set wlan0 up` produces an error, disconnect and reconnect power and try again.

Result should be the following:

```
7: wlan0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc pfifo_fast state DOWN mode DEFAULT group default qlen 1000
    link/ether 00:22:2d:0e:11:3a brd ff:ff:ff:ff:ff:ff
```

1. Run `$ ifconfig -a` to show connections. Check for `wlan0`.
2. Open the WPA config file:

```
$ vi /etc/wpa_supplicant/wpa_supplicant.conf
```

Add the following lines to automatically connect to dronenet:

```
network={
        ssid="dronenet"
        key_mgmt=NONE
}
```

Check that WiFi is properly set up:

```
$ ifup wlan0
$ ifdown wlan0
```

Expected output:

```
Internet Systems Consortium DHCP Client 4.3.3
Copyright 2004-2015 Internet Systems Consortium.
All rights reserved.
For info, please visit https://www.isc.org/software/dhcp/

Listening on LPF/wlan0/00:22:2d:0e:11:3a
Sending on   LPF/wlan0/00:22:2d:0e:11:3a
Sending on   Socket/fallback
DHCPDISCOVER on wlan0 to 255.255.255.255 port 67 interval 3 (xid=0xa66b782e)
DHCPDISCOVER on wlan0 to 255.255.255.255 port 67 interval 5 (xid=0xa66b782e)
DHCPREQUEST of 192.168.0.100 on wlan0 to 255.255.255.255 port 67 (xid=0x2e786ba6)
DHCPOFFER of 192.168.0.100 from 192.168.0.1
DHCPACK of 192.168.0.100 from 192.168.0.1
bound to 192.168.0.100 -- renewal in 41989 seconds.
```

```
Killed old client process
Internet Systems Consortium DHCP Client 4.3.3
Copyright 2004-2015 Internet Systems Consortium.
All rights reserved.
For info, please visit https://www.isc.org/software/dhcp/

Listening on LPF/wlan0/00:22:2d:0e:11:3a
Sending on   LPF/wlan0/00:22:2d:0e:11:3a
Sending on   Socket/fallback
DHCPRELEASE on wlan0 to 192.168.0.1 port 67 (xid=0x1783018d)
```

Close ssh connection. Shut down (disconnect USB power) NanoPi Neo and disconnect Ethernet cable. Reboot (connect USB power) NanoPi Neo and check network for wireless clients. NanoPi Neo should automatically connect to dronenet via WiFi.

## Configure Static IP

***NOTE:*** DISREGARD THIS SECTION

### Configure network interface

~~~~
$ sudo vi /etc/network/interfaces
~~~~

Change line that says `iface wlan0 inet dhcp` to `iface wlan0 inet static` and add:

~~~~
address 192.168.0.113
netmask 255.255.255.0
gateway 10.42.0.47
~~~~

### Configure DNS servers

~~~~
$ sudo vi /etc/resolv.conf
~~~~

Add:

~~~~
nameserver 8.8.8.8
nameserver 8.8.4.4
~~~~

### Add DNS servers to network interfaces file

~~~~
$ sudo vi /etc/network/interfaces
~~~~

Add:

~~~~
dns-nameservers 8.8.8.8
~~~~

### Restart network card

~~~~
$ systemctl restart ifup@eth0
$ ifconfig
~~~~


<!-- ## Initializing NanoPi Neo After Setting Up Connections and Static IP -->

 ## Initializing NanoPi Neo After Setting Up Connections

**_NOTE:_** Go to [Initializing NanoPi Neo After Installing Ubuntu-core](#initializing-nanopi-neo-after-installing-ubuntu-core) if NanoPiNeo is not set up to establish connections on its own.

1. Insert mini-SD card into NanoPi Neo. **_NOTE:_** mini-SD card must be in NanoPi Neo before power is applied.
2. Plug in antenna dongle into desktop, connect to dronenet
3. Plug in grey dongle into NanoPi Neo
4. Connect laptop to router. Connect to dronenet via Ethernet and UCSD-PROTECTED via WiFi. This will enable you to connect the NanoPi Neo to the internet and download ROS. **_NOTE:_** Internet access on NanoPi Neo lasts until laptop goes to sleep.
5. Plug in NanoPi Neo USB cable for power.
6. Go to <http://192.168.0.1/> in desktop browser
7. username: admin, password: admin
8. Verify that NanoPi Neo/FriendlyARM shows up in list of wireless clients.
9. `ssh` into NanoPi Neo/FriendlyARM:

```
$ ssh root@192.168.0.XXX
password: fa
```

Check that there is a route to the internet and update software:

```
$ ping www.google.com
$ sudo apt-get update
```

## Common Issues

- Keep the laptop awake to maintain internet access through dronenet.
- Desktop connection to dronenet isn't always consistent.
- When installing packages, make sure to follow [Set Up Mirrors List](#set-up-mirrors-list) closely. That's where most of my errors came from.

## Troubleshooting

- [git-core vs git](http://git.661346.n2.nabble.com/git-core-vs-git-package-on-ubuntu-td7576083.html)
- [apt-get not finding package](http://stackoverflow.com/questions/16164261/apt-get-does-not-find-package-git-on-ubuntu-12-04)
- [apt-get no package installation candidate](http://askubuntu.com/questions/14685/what-does-package-package-has-no-installation-candidate-mean))
- [apt-get update fails to fetch files, "Temporary failure resolving ..." error](http://askubuntu.com/questions/91543/apt-get-update-fails-to-fetch-files-temporary-failure-resolving-error)
- [Install ROS on CHIP](https://bbs.nextthing.co/t/robot-operating-system-ros/8470)

## References

- [NanoPi Neo Wiki](http://wiki.friendlyarm.com/wiki/index.php/NanoPi_NEO)
- [Set up static IP](https://www.howtoforge.com/linux-basics-set-a-static-ip-on-ubuntu)
- [Kinetic Installation](http://wiki.ros.org/kinetic/Installation/Ubuntu)
- [ROS Mirrors](http://wiki.ros.org/ROS/Installation/UbuntuMirrors)
- [Manage software repositories from the command line](https://help.ubuntu.com/community/Repositories/CommandLine)
