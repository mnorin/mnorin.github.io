---
title: Работа с контейнерами LXC в Debian
date: 2015-03-02T16:27:44+03:00
author: Maxim Norin
layout: post
permalink: /rabota-s-kontejnerami-lxc-v-debian.html
categories:
  - Статьи
tags:
  - debian
  - linux
  - lxc
---
Технология LXC (Linux Containers) - это технология, позволяющая создать так называемые контейнеры, являющиеся по сути виртуальными машинами, использующими одно ядро. При этом в контейнерах могут работать разные операционные системы. Вы можете одновременно на одном и том же железе запустить, например, Debian, Ubuntu, CentOS, Gentoo и другие, которые будут использовать одно и то же ядро. В этом плане LXC работает схоже с OpenVZ. Давайте посмотрим, как их использовать.
<!--more-->
## Установка LXC
Самое первое, что надо сделать - поставить пакеты
```
apt-get install lxc bridge-utils libvirt-bin debootstrap
```
Затем проверить, смонтировались ли cgroups.
```
$ sudo mount | grep cgroup
tmpfs on /sys/fs/cgroup type tmpfs (ro,nosuid,nodev,noexec,mode=755)
cgroup on /sys/fs/cgroup/systemd type cgroup (rw,nosuid,nodev,noexec,relatime,xattr,release_agent=/lib/systemd/systemd-cgroups-agent,name=systemd)
cgroup on /sys/fs/cgroup/cpuset type cgroup (rw,nosuid,nodev,noexec,relatime,cpuset,clone_children)
cgroup on /sys/fs/cgroup/cpu,cpuacct type cgroup (rw,nosuid,nodev,noexec,relatime,cpu,cpuacct)
cgroup on /sys/fs/cgroup/memory type cgroup (rw,nosuid,nodev,noexec,relatime,memory)
cgroup on /sys/fs/cgroup/devices type cgroup (rw,nosuid,nodev,noexec,relatime,devices)
cgroup on /sys/fs/cgroup/freezer type cgroup (rw,nosuid,nodev,noexec,relatime,freezer)
cgroup on /sys/fs/cgroup/net_cls,net_prio type cgroup (rw,nosuid,nodev,noexec,relatime,net_cls,net_prio)
cgroup on /sys/fs/cgroup/blkio type cgroup (rw,nosuid,nodev,noexec,relatime,blkio)
cgroup on /sys/fs/cgroup/perf_event type cgroup (rw,nosuid,nodev,noexec,relatime,perf_event)
```
Если вы видите такой список, значит всё хорошо. Если вы не видите memory, то нужно сделать кое-что еще, в противном случае мы не сможем выставлять ограничения по использованию памяти. Проверить можно также следующей командой:
```
> cat /proc/cgroups 
#subsys_name  	hierarchy	  num_cgroups  	enabled
cpuset       	1      	4      	1
cpu	          1	      4      	1
cpuacct  	    1      	4      	1
memory       	0      	1      	0
devices 	     1      	4      	1
freezer 	     1      	4      	1
net_cls 	     1	      4      	1
blkio   	     1      	4      	1
perf_event	   1      	4      	1
```
Если строка "memory" у вас выглядит похожим образом, надо включить поддержку управления памятью в ядре. Это можно сделать следующим образом:

1) Добавить в файле /etc/default/grub в строчку
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet"
```
опции "cgroup_enable=memory" и "swapaccount=1". Строчка должна выглядеть так:
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet cgroup_enable=memory swapaccount=1"
```
2) Дать команду "update-grub"

3) Перезагрузить компьютер

4) Посмотреть еще раз содержимое /proc/cgroups. Теперь оно должно выглядеть примерно так:
```
> cat /proc/cgroups
#subsys_name    hierarchy    num_cgroups    enabled
cpuset          	1            6              1
cpu             1            6              1
cpuacct         1            6              1
memory          1            6              1
devices         1            6              1
freezer         1            6              1
net_cls         1            6              1
blkio           1            6              1
perf_event      1            6              1
```

Теперь нужно проверить конфигурацию LXC:
```
# lxc-checkconfig
Kernel configuration not found at /proc/config.gz; searching...
Kernel configuration found at /boot/config-3.16.0-4-amd64
--- Namespaces ---
Namespaces: enabled
Utsname namespace: enabled
Ipc namespace: enabled
Pid namespace: enabled
User namespace: enabled
Network namespace: enabled
Multiple /dev/pts instances: enabled

--- Control groups ---
Cgroup: enabled
Cgroup clone_children flag: enabled
Cgroup device: enabled
Cgroup sched: enabled
Cgroup cpu account: enabled
Cgroup memory controller: enabled
Cgroup cpuset: enabled

--- Misc ---
Veth pair device: enabled
Macvlan: enabled
Vlan: enabled
File capabilities: enabled

Note : Before booting a new kernel, you can check its configuration
usage : CONFIG=/path/to/config /usr/bin/lxc-checkconfig
```
Все настройки должны быть enabled. Теперь можно приступать к созданию контейнера.

## Создание контейнера
Для создания контейнера используется команда "lxc-create". Вот пара опций, которые можно использовать:

-n <имя> - Название контейнера

-t <тип> - Тип контейнера. По сути операционная система, которая будет работать в контейнере. Посмотреть список доступных вариантов можно командой "ls /usr/share/lxc/templates/". В этой директории лежат шаблоны для контейнеров.

Создадим новый контейнер (виртуальную машину) с операционной системой debian:
```
# lxc-create -n debian01 -t debian

debootstrap is /usr/sbin/debootstrap
Checking cache download in /var/cache/lxc/debian/rootfs-wheezy-amd64 ... 
Downloading debian minimal ...
I: Retrieving Release 
I: Retrieving Release.gpg 
I: Checking Release signature
I: Valid Release signature (key id ED6D65271AACF0FF15D123036FB2A1C265FFB764)
I: Retrieving Packages 
I: Validating Packages 
I: Resolving dependencies of required packages...
I: Resolving dependencies of base packages...
I: Found additional required dependencies: insserv libbz2-1.0 libdb5.1 libsemanage-common libsemanage1 libslang2 libustr-1.0-1 
I: Found additional base dependencies: adduser debian-archive-keyring gnupg gpgv isc-dhcp-common libapt-pkg4.12 libbsd0 libclass-isa-perl libedit2 libgdbm3 libgssapi-krb5-2 libk5crypto3 libkeyutils1 libkrb5-3 libkrb5support0 libncursesw5 libprocps0 libreadline6 libssl1.0.0 libstdc++6 libswitch-perl libusb-0.1-4 libwrap0 openssh-client perl perl-modules procps readline-common 
I: Checking component main on http://http.debian.net/debian...
I: Retrieving libacl1 2.2.51-8
I: Validating libacl1 2.2.51-8
I: Retrieving adduser 3.113+nmu3
I: Validating adduser 3.113+nmu3
I: Retrieving apt 0.9.7.9+deb7u7
I: Validating apt 0.9.7.9+deb7u7
I: Retrieving libapt-pkg4.12 0.9.7.9+deb7u7
I: Validating libapt-pkg4.12 0.9.7.9+deb7u7
........
........
........
I: Configuring apt...
I: Configuring openssh-client...
I: Configuring openssh-server...
I: Configuring perl-modules...
I: Configuring libswitch-perl...
I: Configuring perl...
I: Configuring libui-dialog-perl...
I: Base system installed successfully.
Download complete.
Copying rootfs to /var/lib/lxc/myvm/rootfs...Generating locales (this might take a while)...
  ru_RU.UTF-8... done
Generation complete.
update-rc.d: using dependency based boot sequencing
insserv: warning: current start runlevel(s) (empty) of script `checkroot.sh' overrides LSB defaults (S).
insserv: warning: current stop runlevel(s) (S) of script `checkroot.sh' overrides LSB defaults (empty).
update-rc.d: using dependency based boot sequencing
update-rc.d: error: umountfs Default-Start contains no runlevels, aborting.
update-rc.d: using dependency based boot sequencing
insserv: warning: current start runlevel(s) (empty) of script `hwclock.sh' overrides LSB defaults (S).
insserv: warning: current stop runlevel(s) (0 6 S) of script `hwclock.sh' overrides LSB defaults (0 6).
update-rc.d: using dependency based boot sequencing
update-rc.d: error: cannot find a LSB script for hwclockfirst.sh
Creating SSH2 RSA key; this may take some time ...
Creating SSH2 DSA key; this may take some time ...
Creating SSH2 ECDSA key; this may take some time ...
invoke-rc.d: policy-rc.d denied execution of restart.

Current default time zone: 'Europe/Moscow'
Local time is now:      Sun Mar  1 20:56:01 MSK 2015.
Universal Time is now:  Sun Mar  1 17:56:01 UTC 2015.

Root password is 'nBy6AbFT', please change !</pre>
Контейнер создан. Запускаем его командой
<pre class="">lxc-start -n debian01
```

Виртуальная машина загрузится, входим как root, можно сразу поменять пароль root'а. Сетевой карты в контейнере еще нет, ее надо добавить, если нам нужна поддержка сети. Для начала выходим из консоли виртуальной машины нажав Ctrl+a и затем q. Останавливаем контейнер
```
lxc-stop -n debian01 -k
```
Редактируем файл /var/lib/lxc/myvm/config. Меняем строчку
```
lxc.network.type = empty
```
на
```
lxc.network.type = veth
```
И добавляем следующее:
```
lxc.network.flags = up
lxc.network.link = br0
lxc.network.ipv4 = 0.0.0.0/24
lxc.network.hwaddr = 00:1E:62:09:08:07
```
В файл /etc/network/interfaces добавим бридж:
```
auto br0
iface br0 inet dhcp
        bridge_ports eth0
        bridge_fd 0
        bridge_maxwait 0
```
Затем поднимаем его
```
ifup br0
```
Настройки сети готовы, можно снова запускать контейнер
```
# lxc-start -n debian01
```
После загрузки проверяем, есть ли сеть:
```
# ifconfig
eth0      Link encap:Ethernet  HWaddr 00:1e:62:09:08:07  
          inet addr:192.168.0.11  Bcast:192.168.0.255  Mask:255.255.255.0
          inet6 addr: fe80::21e:62ff:fe09:807/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:55 errors:0 dropped:0 overruns:0 frame:0
          TX packets:13 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:12946 (12.6 KiB)  TX bytes:1478 (1.4 KiB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```
Как видим, сеть есть, можно продолжать работать, устанавливать приложения, работающие с сетью.

При запуске контейнера можно добавить опцию "-d":
```
lxc-start -n debian01 -d
```
И потом подключиться после загрузки контейнера командой
```
lxc-console -n debian01
```
Отцепиться от консоли можно нажав Ctrl+a q

В общем, вот и всё, можно приступать к настройке машины.

## Автостарт контейнеров
Для того, чтобы контейнер стартовал автоматически после старта системы необходимо сделать символическую ссылку на конфигурационный файл в директории /etc/lxc/auto:
```
ln -s /var/lib/lxc/debian01/config /etc/lxc/auto/debian01
```
В последних версиях LXC это не требуется, автостарт обеспечивается опциями самого контейнера. Вот они:
```
lxc.start.auto = 1
lxc.start.delay = 5
```

## Примечания
В конце еще несколько замечаний.

1) Для создания контейнера с Ubuntu может потребоваться установить пакет "ubuntu-archive-keyring".

2) Для создания контейнера с CentOS необходимо поставить пакет yum (да-да, он есть в Debian'е).

3) В комплекте с LXC идет некоторое количество программ, названия которых начинаются с "lxc-test-". Очень рекомендую посмотреть.

И, как обычно, жду замечания в комментариях.