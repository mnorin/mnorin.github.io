---
id: 39
title: Cisco VPN Client 4.8.02 (0030) на Debian Squeeze
date: 2013-02-01T00:06:11+03:00
author: Maxim Norin
layout: post
guid: https://mnorin.com/?p=39
permalink: /cisco-vpn-client-4-8-02-0030-na-debian-squeeze.html
categories:
  - Статьи
tags:
  - cisco
  - client
  - debian
  - linux
  - squeeze
  - vpn
---
Дано: Linux 2.6.32-5-686, vpn client не собирается
<!--more-->
```
# ./vpn_install
Cisco Systems VPN Client Version 4.8.02 (0030) Linux Installer
Copyright (C) 1998-2006 Cisco Systems, Inc. All Rights Reserved.
By installing this product you agree that you have read the
license.txt file (The VPN Client license) and will comply with its terms.
Directory where binaries will be installed [/usr/local/bin]
Automatically start the VPN service at boot time [yes]

In order to build the VPN kernel module, you must have the
kernel headers for the version of the kernel you are running.

Directory containing linux kernel source code [/lib/modules/2.6.32-5-686/build]

* Binaries will be installed in "/usr/local/bin".
* Modules will be installed in "/lib/modules/2.6.32-5-686/CiscoVPN"
* The VPN service will be started AUTOMATICALLY at boot time.
* Kernel source from "/lib/modules/2.6.32-5-686/build" will be used to build the module.
Is the above correct [y]
Making module
make -C /lib/modules/2.6.32-5-686/build SUBDIRS=/usr/src/vpnclient modules
make[1]: Entering directory `/usr/src/linux-headers-2.6.32-5-686'
CC [M]  /usr/src/vpnclient/linuxcniapi.o
CC [M]  /usr/src/vpnclient/frag.o
CC [M]  /usr/src/vpnclient/IPSecDrvOS_linux.o
CC [M]  /usr/src/vpnclient/interceptor.o
/usr/src/vpnclient/interceptor.c: In function ‘interceptor_init’:
/usr/src/vpnclient/interceptor.c:132: error: ‘struct net_device’ has no member named ‘hard_start_xmit’
/usr/src/vpnclient/interceptor.c:133: error: ‘struct net_device’ has no member named ‘get_stats’
/usr/src/vpnclient/interceptor.c:134: error: ‘struct net_device’ has no member named ‘do_ioctl’
/usr/src/vpnclient/interceptor.c: In function ‘add_netdev’:
/usr/src/vpnclient/interceptor.c:271: error: ‘struct net_device’ has no member named ‘hard_start_xmit’
/usr/src/vpnclient/interceptor.c:272: error: ‘struct net_device’ has no member named ‘hard_start_xmit’
/usr/src/vpnclient/interceptor.c: In function ‘remove_netdev’:
/usr/src/vpnclient/interceptor.c:294: error: ‘struct net_device’ has no member named ‘hard_start_xmit’
make[4]: *** [/usr/src/vpnclient/interceptor.o] Ошибка 1
make[3]: *** [_module_/usr/src/vpnclient] Ошибка 2
make[2]: *** [sub-make] Ошибка 2
make[1]: *** [all] Ошибка 2
make[1]: Leaving directory `/usr/src/linux-headers-2.6.32-5-686'
make: *** [default] Ошибка 2
Failed to make module "cisco_ipsec.ko".
```
Вот такая вот незадача.

Решилось применением патча отсюда: [http://www.lamnk.com/download/fixes.patch]
```bash
wget http://www.lamnk.com/download/fixes.patch
patch < ./fixes.patch
sudo ./vpn_install
```
Результат:
```
# ./vpn_install
Cisco Systems VPN Client Version 4.8.02 (0030) Linux Installer
Copyright (C) 1998-2006 Cisco Systems, Inc. All Rights Reserved.
By installing this product you agree that you have read the
license.txt file (The VPN Client license) and will comply with
its terms. Directory where binaries will be installed [/usr/local/bin]
Automatically start the VPN service at boot time [yes]

In order to build the VPN kernel module, you must have the
kernel headers for the version of the kernel you are running.

Directory containing linux kernel source code [/lib/modules/2.6.32-5-686/build]

* Binaries will be installed in "/usr/local/bin".
* Modules will be installed in "/lib/modules/2.6.32-5-686/CiscoVPN"
* The VPN service will be started AUTOMATICALLY at boot time.
* Kernel source from "/lib/modules/2.6.32-5-686/build" will be used to build the module.
Is the above correct [y]

Making module
make -C /lib/modules/2.6.32-5-686/build SUBDIRS=/usr/src/vpnclient modules
make[1]: Entering directory `/usr/src/linux-headers-2.6.32-5-686'
CC [M]  /usr/src/vpnclient/linuxcniapi.o
CC [M]  /usr/src/vpnclient/frag.o
CC [M]  /usr/src/vpnclient/IPSecDrvOS_linux.o
CC [M]  /usr/src/vpnclient/interceptor.o
CC [M]  /usr/src/vpnclient/linuxkernelapi.o
LD [M]  /usr/src/vpnclient/cisco_ipsec.o
Building modules, stage 2.
MODPOST 1 modules
CC      /usr/src/vpnclient/cisco_ipsec.mod.o
LD [M]  /usr/src/vpnclient/cisco_ipsec.ko
make[1]: Leaving directory `/usr/src/linux-headers-2.6.32-5-686'
Create module directory "/lib/modules/2.6.32-5-686/CiscoVPN"

Copying module to directory "/lib/modules/2.6.32-5-686/CiscoVPN".
Already have group 'bin'
Creating start/stop script "/etc/init.d/vpnclient_init".
/etc/init.d/vpnclient_init
Enabling start/stop script for run level 3,4 and 5.
Creating global config /etc/opt/cisco-vpnclient

Installing license.txt (VPN Client license) in "/opt/cisco-vpnclient/":
/opt/cisco-vpnclient/license.txt

Installing bundled user profiles in "/etc/opt/cisco-vpnclient/Profiles/":
* New Profiles     : Netz_Uni_Hannover_Internet Netz_Uni_Hannover_WLAN sample
Copying binaries to directory "/opt/cisco-vpnclient/bin".
Adding symlinks to "/usr/local/bin".
/opt/cisco-vpnclient/bin/vpnclient
/opt/cisco-vpnclient/bin/cisco_cert_mgr
/opt/cisco-vpnclient/bin/ipseclog
Copying setuid binaries to directory "/opt/cisco-vpnclient/bin".
/opt/cisco-vpnclient/bin/cvpnd
Copying libraries to directory "/opt/cisco-vpnclient/lib".
/opt/cisco-vpnclient/lib/libvpnapi.so
Copying header files to directory "/opt/cisco-vpnclient/include".
/opt/cisco-vpnclient/include/vpnapi.h

Setting permissions.
/opt/cisco-vpnclient/bin/cvpnd (setuid root)
/opt/cisco-vpnclient (group bin readable)
/etc/opt/cisco-vpnclient (group bin readable)
/etc/opt/cisco-vpnclient/Profiles (group bin readable)
/etc/opt/cisco-vpnclient/Certificates (group bin readable)
* You may wish to change these permissions to restrict access to root.
* You must run "/etc/init.d/vpnclient_init start" before using the client.
* This script will be run AUTOMATICALLY every time you reboot your computer.
```