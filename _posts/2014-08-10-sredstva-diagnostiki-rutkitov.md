---
title: Средства диагностики руткитов
date: 2014-08-10T22:48:16+03:00
author: Maxim Norin
layout: post
permalink: /sredstva-diagnostiki-rutkitov.html
categories:
  - Статьи
tags:
  - chkrootkit
  - rkhunter
  - rootkit
  - руткит
---
Руткит (rootkit) - это программа, позволяющая получить доступ с правами суперпользователя или администратора к системе, при этом скрывающая следы злоумышленника. Когда недоброжелатель получает доступ к системе с повышенными правами, можно сказать, что систему вы потеряли. Однако, если у вас еще есть доступ к системе, то можно попытаться ее спасти. В этом вам помогут средства диагностики руткитов - rkhunter и chkrootkit.
<!--more-->

## chkrootkit
Программа chkrootkit содержится в пакете с одноименным названием, установить ее в Debian/Ubuntu можно командой
```
apt-get install chkrootkit
```
После установки запуск производится командой
```
chkrootkit
```
Если запустить ее без параметров, то результат ее работы будет выглядеть следующим образом:
```
# chkrootkit
ROOTDIR is `/'
Checking `amd'...                                           not found
Checking `basename'...                                      not infected
Checking `biff'...                                          not found
Checking `chfn'...                                          not infected
Checking `chsh'...                                          not infected
Checking `cron'...                                          not infected
Checking `crontab'...                                       not infected
Checking `date'...                                          not infected
Checking `du'...                                            not infected
Checking `dirname'...                                       not infected
Checking `echo'...                                          not infected
Checking `egrep'...                                         not infected
Checking `env'...                                           not infected
Checking `find'...                                          not infected
Checking `fingerd'...                                       not found
Checking `gpm'...                                           not found
Checking `grep'...                                          not infected
Checking `hdparm'...                                        not infected
Checking `su'...                                            not infected
Checking `ifconfig'...                                      not infected
Checking `inetd'...                                         not infected
Checking `inetdconf'...                                     not infected
Checking `identd'...                                        not found
Checking `init'...                                          not infected
Checking `killall'...                                       not infected
Checking `ldsopreload'...                                   not infected
Checking `login'...                                         not infected
Checking `ls'...                                            not infected
Checking `lsof'...                                          not infected
Checking `mail'...                                          not infected
Checking `mingetty'...                                      not found
Checking `netstat'...                                       not infected
Checking `named'...                                         not found
Checking `passwd'...                                        not infected
Checking `pidof'...                                         not infected
Checking `pop2'...                                          not found
Checking `pop3'...                                          not found
Checking `ps'...                                            not infected
Checking `pstree'...                                        not infected
Checking `rpcinfo'...                                       not infected
Checking `rlogind'...                                       not found
Checking `rshd'...                                          not found
Checking `slogin'...                                        not infected
Checking `sendmail'...                                      not infected
Checking `sshd'...                                          not infected
Checking `syslogd'...                                       not tested
Checking `tar'...                                           not infected
Checking `tcpd'...                                          not infected
Checking `tcpdump'...                                       not infected
Checking `top'...                                           not infected
Checking `telnetd'...                                       not infected
Checking `timed'...                                         not found
Checking `traceroute'...                                    not infected
Checking `vdir'...                                          not infected
Checking `w'...                                             not infected
Checking `write'...                                         not infected
Checking `aliens'...                                        no suspect files
Searching for sniffer's logs, it may take a while...        nothing found
Searching for rootkit HiDrootkit's default files...         nothing found
Searching for rootkit t0rn's default files...               nothing found
Searching for t0rn's v8 defaults...                         nothing found
Searching for rootkit Lion's default files...               nothing found
Searching for rootkit RSHA's default files...               nothing found
Searching for rootkit RH-Sharpe's default files...          nothing found
Searching for Ambient's rootkit (ark) default files and dirs... nothing found
Searching for suspicious files and dirs, it may take a while... The following suspicious files and directories were found:  
/usr/lib/python2.6/dist-packages/PyQt4/uic/widget-plugins/.noinit /usr/lib/jvm/java-1.5.0-gcj-4.4/.java-gcj-4.4.jinfo /usr/lib/jvm/.java-gcj-4.7.jinfo /usr/lib/jvm/java-6-sun-1.6.0.26/.systemPrefs /usr/lib/jvm/.java-1.6.0-openjdk-amd64.jinfo /usr/lib/jvm/.java-6-sun.jinfo /usr/lib/pymodules/python2.6/.path /usr/lib/pymodules/python2.7/.path /usr/lib/icedove/.autoreg /usr/lib/python2.7/dist-packages/PyQt4/uic/widget-plugins/.noinit

Searching for LPD Worm files and dirs...                    nothing found
Searching for Ramen Worm files and dirs...                  nothing found
Searching for Maniac files and dirs...                      nothing found
Searching for RK17 files and dirs...                        nothing found
Searching for Ducoci rootkit...                             nothing found
Searching for Adore Worm...                                 nothing found
Searching for ShitC Worm...                                 nothing found
Searching for Omega Worm...                                 nothing found
Searching for Sadmind/IIS Worm...                           nothing found
Searching for MonKit...                                     nothing found
Searching for Showtee...                                    nothing found
Searching for OpticKit...                                   nothing found
Searching for T.R.K...                                      nothing found
Searching for Mithra...                                     nothing found
Searching for LOC rootkit...                                nothing found
Searching for Romanian rootkit...                           nothing found
Searching for Suckit rootkit...                             nothing found
Searching for Volc rootkit...                               nothing found
Searching for Gold2 rootkit...                              nothing found
Searching for TC2 Worm default files and dirs...            nothing found
Searching for Anonoying rootkit default files and dirs...   nothing found
Searching for ZK rootkit default files and dirs...          nothing found
Searching for ShKit rootkit default files and dirs...       nothing found
Searching for AjaKit rootkit default files and dirs...      nothing found
Searching for zaRwT rootkit default files and dirs...       nothing found
Searching for Madalin rootkit default files...              nothing found
Searching for Fu rootkit default files...                   nothing found
Searching for ESRK rootkit default files...                 nothing found
Searching for rootedoor...                                  nothing found
Searching for ENYELKM rootkit default files...              nothing found
Searching for common ssh-scanners default files...          nothing found
Searching for suspect PHP files...                          nothing found
Searching for anomalies in shell history files...           nothing found
Checking `asp'...                                           not infected
Checking `bindshell'...                                     not infected
Checking `lkm'...                                           chkproc: nothing detected
chkdirs: nothing detected
Checking `rexedcs'...                                       not found
Checking `sniffer'...                                       lo: not promisc and no packet sniffer sockets
eth0: PACKET SNIFFER(/sbin/dhclient[3530])
Checking `w55808'...                                        not infected
Checking `wted'...                                          chkwtmp: nothing deleted
Checking `scalper'...                                       not infected
Checking `slapper'...                                       not infected
Checking `z2'...                                            chklastlog: nothing deleted
Checking `chkutmp'...                                        The tty of the following user process(es) were not found
 in /var/run/utmp !
! RUID          PID TTY    CMD
! root         3489 tty7   /usr/bin/X -br -nolisten tcp :0 vt7 -auth /var/run/xauth/A:0-narm3b
chkutmp: nothing deleted
Checking `OSX_RSPLUG'...                                    not infected
```
Как видите, программа проверяет определенные части системы на предмет присутствия руткитов. В моем случае она ничего не нашла. Тем не менее, полагаться на одно средство никогда не стоит. Если есть возможность использовать другие средства, то лучше их использовать. Именно поэтому мы будем рассматривать сразу две программы.

У chkrootkit есть параметры командной строки, которые можно использовать при работе, а формат самой команды выглядит так:
```
chkrootkit [параметры] [название теста]
```
Параметры:
<table>
<tbody>
<tr>
<td width="120">-h</td>
<td>Вывести краткую справку и выйти</td>
</tr>
<tr>
<td>-V</td>
<td>Вывести информацию о версии и выйти</td>
</tr>
<tr>
<td>-l</td>
<td>Список возможных тестов. Именно названия из этого списка можно явно указывать при запуске. Если название не указано, будут проведены все тесты</td>
</tr>
<tr>
<td>-d</td>
<td>Режим отладки</td>
</tr>
<tr>
<td>-x</td>
<td>Режим эксперта</td>
</tr>
<tr>
<td>-e</td>
<td>Исключить файлы и директории, для которых известны ложно-позитивные срабатывания. Список должен быть заключен в кавычки, а файлы и директории разделены пробелами</td>
</tr>
<tr>
<td>-q</td>
<td>"Тихий" режим</td>
</tr>
<tr>
<td>-r &lt;директория&gt;</td>
<td>Использовать указанную директория как корневую</td>
</tr>
<tr>
<td>-p &lt;список&gt;</td>
<td>Указать пути для внешних программ, используемых chkrootkit. Список разделяется двоеточиями</td>
</tr>
<tr>
<td>-n</td>
<td>Исключить из проверки директории, смонтированные по NFS</td>
</tr>
</tbody>
</table>
Как видите, параметров немного. Теперь давайте рассмотрим вторую программу, rkhunter

## rkhunter
rkhunter - это более сложная программа. Устанавливается она так же просто:
```
apt-get install rkhunter
```
Установка включается также скрипт для запуска проверки по расписанию, он помещается в директорию /etc/cron.daily и имеет название "chkrootkit". Однако для того, чтобы проверка происходила ежедневно, необходимо в файле /etc/rkhunter.conf изменить опцию
```
RUN_DAILY="false"
```
на
```
RUN_DAILY="true"
```
Запустить проверку можно при помощи опции "--check". Результат проверки должен выглядеть следующим образом:
```
# rkhunter --check
[ Rootkit Hunter version 1.4.0 ]

Checking system commands...

  Performing 'strings' command checks
    Checking 'strings' command                               [ OK ]

  Performing 'shared libraries' checks
    Checking for preloading variables                        [ None found ]
    Checking for preloaded libraries                         [ None found ]
    Checking LD_LIBRARY_PATH variable                        [ Not found ]

  Performing file properties checks
    Checking for prerequisites                               [ OK ]
    /usr/sbin/adduser                                        [ OK ]
    /usr/sbin/chroot                                         [ OK ]
    /usr/sbin/cron                                           [ OK ]
    /usr/sbin/groupadd                                       [ OK ]
    /usr/sbin/groupdel                                       [ OK ]
    /usr/sbin/groupmod                                       [ OK ]
    /usr/sbin/grpck                                          [ OK ]
    /usr/sbin/inetd                                          [ OK ]
    /usr/sbin/nologin                                        [ OK ]
    /usr/sbin/pwck                                           [ OK ]
    /usr/sbin/rsyslogd                                       [ OK ]
    /usr/sbin/tcpd                                           [ OK ]
    /usr/sbin/useradd                                        [ OK ]
    /usr/sbin/userdel                                        [ OK ]
    /usr/sbin/usermod                                        [ OK ]
    /usr/sbin/vipw                                           [ OK ]
    /usr/bin/awk                                             [ OK ]
    /usr/bin/basename                                        [ OK ]
    /usr/bin/chattr                                          [ OK ]
    /usr/bin/curl                                            [ OK ]
    /usr/bin/cut                                             [ OK ]
    /usr/bin/diff                                            [ OK ]
    /usr/bin/dirname                                         [ OK ]
    /usr/bin/dpkg                                            [ OK ]
    /usr/bin/dpkg-query                                      [ OK ]
    /usr/bin/du                                              [ OK ]
    /usr/bin/elinks                                          [ OK ]
    /usr/bin/env                                             [ OK ]
    /usr/bin/file                                            [ OK ]
    /usr/bin/find                                            [ OK ]
    /usr/bin/GET                                             [ OK ]
    /usr/bin/groups                                          [ OK ]
    /usr/bin/head                                            [ OK ]
    /usr/bin/id                                              [ OK ]
    /usr/bin/killall                                         [ OK ]
    /usr/bin/last                                            [ OK ]
    /usr/bin/lastlog                                         [ OK ]
    /usr/bin/ldd                                             [ OK ]
    /usr/bin/less                                            [ OK ]
    /usr/bin/locate                                          [ OK ]
    /usr/bin/logger                                          [ OK ]
    /usr/bin/lsattr                                          [ OK ]
    /usr/bin/lsof                                            [ OK ]
    /usr/bin/mail                                            [ OK ]
    /usr/bin/md5sum                                          [ OK ]
    /usr/bin/mlocate                                         [ OK ]
    /usr/bin/newgrp                                          [ OK ]
    /usr/bin/passwd                                          [ OK ]
    /usr/bin/perl                                            [ OK ]
    /usr/bin/pgrep                                           [ OK ]
    /usr/bin/pkill                                           [ OK ]
    /usr/bin/pstree                                          [ OK ]
    /usr/bin/rkhunter                                        [ OK ]
    /usr/bin/rpm                                             [ OK ]
    /usr/bin/runcon                                          [ OK ]
    /usr/bin/sha1sum                                         [ OK ]
    /usr/bin/sha224sum                                       [ OK ]
    /usr/bin/sha256sum                                       [ OK ]
    /usr/bin/sha384sum                                       [ OK ]
    /usr/bin/sha512sum                                       [ OK ]
    /usr/bin/size                                            [ OK ]
    /usr/bin/sort                                            [ OK ]
    /usr/bin/stat                                            [ OK ]
    /usr/bin/strace                                          [ OK ]
    /usr/bin/strings                                         [ OK ]
    /usr/bin/sudo                                            [ OK ]
    /usr/bin/tail                                            [ OK ]
    /usr/bin/test                                            [ OK ]
    /usr/bin/top                                             [ OK ]
    /usr/bin/touch                                           [ OK ]
    /usr/bin/tr                                              [ OK ]
    /usr/bin/uniq                                            [ OK ]
    /usr/bin/users                                           [ OK ]
    /usr/bin/vmstat                                          [ OK ]
    /usr/bin/w                                               [ OK ]
    /usr/bin/watch                                           [ OK ]
    /usr/bin/wc                                              [ OK ]
    /usr/bin/wget                                            [ OK ]
    /usr/bin/whatis                                          [ OK ]
    /usr/bin/whereis                                         [ OK ]
    /usr/bin/which                                           [ OK ]
    /usr/bin/who                                             [ OK ]
    /usr/bin/whoami                                          [ OK ]
    /usr/bin/unhide.rb                                       [ Warning ]
    /usr/bin/gawk                                            [ OK ]
    /usr/bin/lwp-request                                     [ OK ]
    /usr/bin/bsd-mailx                                       [ OK ]
    /usr/bin/w.procps                                        [ OK ]
    /sbin/depmod                                             [ OK ]
    /sbin/fsck                                               [ OK ]
    /sbin/ifconfig                                           [ OK ]
    /sbin/ifdown                                             [ OK ]
    /sbin/ifup                                               [ OK ]
    /sbin/init                                               [ OK ]
    /sbin/insmod                                             [ OK ]
    /sbin/ip                                                 [ OK ]
    /sbin/lsmod                                              [ OK ]
    /sbin/modinfo                                            [ OK ]
    /sbin/modprobe                                           [ OK ]
    /sbin/rmmod                                              [ OK ]
    /sbin/route                                              [ OK ]
    /sbin/runlevel                                           [ OK ]
    /sbin/sulogin                                            [ OK ]
    /sbin/sysctl                                             [ OK ]
    /bin/bash                                                [ OK ]
    /bin/cat                                                 [ OK ]
    /bin/chmod                                               [ OK ]
    /bin/chown                                               [ OK ]
    /bin/cp                                                  [ OK ]
    /bin/date                                                [ OK ]
    /bin/df                                                  [ OK ]
    /bin/dmesg                                               [ OK ]
    /bin/echo                                                [ OK ]
    /bin/ed                                                  [ OK ]
    /bin/egrep                                               [ OK ]
    /bin/fgrep                                               [ OK ]
    /bin/fuser                                               [ OK ]
    /bin/grep                                                [ OK ]
    /bin/ip                                                  [ OK ]
    /bin/kill                                                [ OK ]
    /bin/less                                                [ OK ]
    /bin/login                                               [ OK ]
    /bin/ls                                                  [ OK ]
    /bin/lsmod                                               [ OK ]
    /bin/mktemp                                              [ OK ]
    /bin/more                                                [ OK ]
    /bin/mount                                               [ OK ]
    /bin/mv                                                  [ OK ]
    /bin/netstat                                             [ OK ]
    /bin/ping                                                [ OK ]
    /bin/ps                                                  [ OK ]
    /bin/pwd                                                 [ OK ]
    /bin/readlink                                            [ OK ]
    /bin/sed                                                 [ OK ]
    /bin/sh                                                  [ OK ]
    /bin/su                                                  [ OK ]
    /bin/touch                                               [ OK ]
    /bin/uname                                               [ OK ]
    /bin/which                                               [ OK ]
    /bin/kmod                                                [ OK ]
    /bin/dash                                                [ OK ]

[Press  to continue]


Checking for rootkits...

  Performing check of known rootkit files and directories
    55808 Trojan - Variant A                                 [ Not found ]
    ADM Worm                                                 [ Not found ]
    AjaKit Rootkit                                           [ Not found ]
    Adore Rootkit                                            [ Not found ]
    aPa Kit                                                  [ Not found ]
    Apache Worm                                              [ Not found ]
    Ambient (ark) Rootkit                                    [ Not found ]
    Balaur Rootkit                                           [ Not found ]
    BeastKit Rootkit                                         [ Not found ]
    beX2 Rootkit                                             [ Not found ]
    BOBKit Rootkit                                           [ Not found ]
    cb Rootkit                                               [ Not found ]
    CiNIK Worm (Slapper.B variant)                           [ Not found ]
    Danny-Boy's Abuse Kit                                    [ Not found ]
    Devil RootKit                                            [ Not found ]
    Dica-Kit Rootkit                                         [ Not found ]
    Dreams Rootkit                                           [ Not found ]
    Duarawkz Rootkit                                         [ Not found ]
    Enye LKM                                                 [ Not found ]
    Flea Linux Rootkit                                       [ Not found ]
    Fu Rootkit                                               [ Not found ]
    Fuck`it Rootkit                                          [ Not found ]
    GasKit Rootkit                                           [ Not found ]
    Heroin LKM                                               [ Not found ]
    HjC Kit                                                  [ Not found ]
    ignoKit Rootkit                                          [ Not found ]
    IntoXonia-NG Rootkit                                     [ Not found ]
    Irix Rootkit                                             [ Not found ]
    Jynx Rootkit                                             [ Not found ]
    KBeast Rootkit                                           [ Not found ]
    Kitko Rootkit                                            [ Not found ]
    Knark Rootkit                                            [ Not found ]
    ld-linuxv.so Rootkit                                     [ Not found ]
    Li0n Worm                                                [ Not found ]
    Lockit / LJK2 Rootkit                                    [ Not found ]
    Mood-NT Rootkit                                          [ Not found ]
    MRK Rootkit                                              [ Not found ]
    Ni0 Rootkit                                              [ Not found ]
    Ohhara Rootkit                                           [ Not found ]
    Optic Kit (Tux) Worm                                     [ Not found ]
    Oz Rootkit                                               [ Not found ]
    Phalanx Rootkit                                          [ Not found ]
    Phalanx2 Rootkit                                         [ Not found ]
    Phalanx2 Rootkit (extended tests)                        [ Not found ]
    Portacelo Rootkit                                        [ Not found ]
    R3dstorm Toolkit                                         [ Not found ]
    RH-Sharpe's Rootkit                                      [ Not found ]
    RSHA's Rootkit                                           [ Not found ]
    Scalper Worm                                             [ Not found ]
    Sebek LKM                                                [ Not found ]
    Shutdown Rootkit                                         [ Not found ]
    SHV4 Rootkit                                             [ Not found ]
    SHV5 Rootkit                                             [ Not found ]
    Sin Rootkit                                              [ Not found ]
    Slapper Worm                                             [ Not found ]
    Sneakin Rootkit                                          [ Not found ]
    'Spanish' Rootkit                                        [ Not found ]
    Suckit Rootkit                                           [ Not found ]
    Superkit Rootkit                                         [ Not found ]
    TBD (Telnet BackDoor)                                    [ Not found ]
    TeLeKiT Rootkit                                          [ Not found ]
    T0rn Rootkit                                             [ Not found ]
    trNkit Rootkit                                           [ Not found ]
    Trojanit Kit                                             [ Not found ]
    Tuxtendo Rootkit                                         [ Not found ]
    URK Rootkit                                              [ Not found ]
    Vampire Rootkit                                          [ Not found ]
    VcKit Rootkit                                            [ Not found ]
    Volc Rootkit                                             [ Not found ]
    Xzibit Rootkit                                           [ Not found ]
    zaRwT.KiT Rootkit                                        [ Not found ]
    ZK Rootkit                                               [ Not found ]

[Press  to continue]


  Performing additional rootkit checks
    Suckit Rookit additional checks                          [ OK ]
    Checking for possible rootkit files and directories      [ None found ]
    Checking for possible rootkit strings                    [ None found ]

  Performing malware checks
    Checking running processes for suspicious files          [ None found ]
    Checking for login backdoors                             [ None found ]
    Checking for suspicious directories                      [ None found ]
    Checking for sniffer log files                           [ None found ]
  Performing trojan specific checks
    Checking for enabled inetd services                      [ Warning ]
    Checking for Apache backdoor                             [ Not found ]

  Performing Linux specific checks
    Checking loaded kernel modules                           [ OK ]
    Checking kernel module names                             [ OK ]

[Press  to continue]


Checking the network...

  Performing checks on the network ports
    Checking for backdoor ports                              [ None found ]
    Checking for hidden ports                                [ Skipped ]

  Performing checks on the network interfaces
    Checking for promiscuous interfaces                      [ None found ]

Checking the local host...

  Performing system boot checks
    Checking for local host name                             [ Found ]
    Checking for system startup files                        [ Found ]
    Checking system startup files for malware                [ None found ]

  Performing group and account checks
    Checking for passwd file                                 [ Found ]
    Checking for root equivalent (UID 0) accounts            [ None found ]
    Checking for passwordless accounts                       [ None found ]
    Checking for passwd file changes                         [ None found ]
    Checking for group file changes                          [ None found ]
    Checking root account shell history files                [ OK ]

  Performing system configuration file checks
    Checking for SSH configuration file                      [ Found ]
    Checking if SSH root access is allowed                   [ Warning ]
    Checking if SSH protocol v1 is allowed                   [ Not allowed ]
    Checking for running syslog daemon                       [ Found ]
    Checking for syslog configuration file                   [ Found ]
    Checking if syslog remote logging is allowed             [ Not allowed ]

  Performing filesystem checks
    Checking /dev for suspicious file types                  [ None found ]
    Checking for hidden files and directories                [ Warning ]

[Press  to continue]



System checks summary
=====================

File properties checks...
    Files checked: 141
    Suspect files: 1

Rootkit checks...
    Rootkits checked : 310
    Possible rootkits: 0

Applications checks...
    All checks skipped

The system checks took: 2 minutes and 20 seconds

All results have been written to the log file (/var/log/rkhunter.log)

One or more warnings have been found while checking the system.
Please check the log file (/var/log/rkhunter.log)
```
chkrootkit запоминает состояние файлов и проверяет, изменилось ли их состояние. Если вы обновили какие-то пакеты, то при очередной проверке rkhunter отобразит изменения, и, если все хорошо, то вам надо будет обновить статус состояния файлов командой
```
chkrootkit --propupd
```
Кроме этого, у chkrootkit есть еще достаточно много команд и опций командной строки, которые вы можете использовать.

Команды:
<table>
<tbody>
<tr>
<td width="160">-c, --check</td>
<td>Проверка, эту опцию мы уже рассмотрели выше</td>
</tr>
<tr>
<td>--unlock</td>
<td>Удалить lock-файл</td>
</tr>
<tr>
<td>--update</td>
<td>Обновить базу программы. Для обновления в системе должна присутствовать программа wget или lynx</td>
</tr>
<tr>
<td>--propupd [уточнение]</td>
<td>Обновление состояния. В качестве уточнения может быть указан один из параметров:
- имя файла
- имя директории
- имя пакета</td>
</tr>
<tr>
<td>--versioncheck</td>
<td>Проверить, есть ли более свежая версия программы</td>
</tr>
<tr>
<td>--list [уточнение]</td>
<td>Вывести информацию. В качестве уточнения можно использовать одно из следующих слов:
tests - вывести список доступных тестов
lang или languages - список поддерживаемых языков
rootkits - список известных руткитов
perl - вывести информацию об установленном в системе трансляторе perl
propfiles - список файлов, из которых формируется информация о состоянии</td>
</tr>
<tr>
<td>-C, --config-check</td>
<td>rkhunter проверяет корректность своего конфигурационного файла и завершает работу</td>
</tr>
<tr>
<td>-V, --version</td>
<td>Вывод информации о версии программы</td>
</tr>
<tr>
<td>-h, --help</td>
<td>Вывод краткой справки</td>
</tr>
</tbody>
</table>
Опции:
<table>
<tbody>
<tr>
<td width="160">--appendlog</td>
<td>По умолчанию rkhunter при новом запуске перезаписывает лог-файл. Эта опция позволяет добавлять записи в конец не затирая существующий файл лога</td>
</tr>
<tr>
<td>--bindir &lt;директория&gt;</td>
<td>Добавление директории в список директорий, в которых rkhunter будет искать необходимые программы. По умолчанию это содержимое переменной PATH пользователя root и некоторые внутренние директории, которые использует rkhunter. Указанная директория будет добавлена в конец списка. Однако, если указать перед именем директории "+", то она будет добавлена в начало списка</td>
</tr>
<tr>
<td>-cs2, --color-set2</td>
<td>Цветовая схема по умолчанию предполагает, что текст будет выводиться на темном фоне. Но если вы используете белый фон в эмуляторе терминала, эта опция позволит использовать цветовую схему, которая лучше выглядит на светлом фоне</td>
</tr>
<tr>
<td>--configfile &lt;файл&gt;</td>
<td>Использовать указанный файл настроек</td>
</tr>
<tr>
<td>--cronjob</td>
<td>Аналогично --check, но отключает средства интерактивного взаимодействия, например, ожидание нажатия клавиши Enter</td>
</tr>
<tr>
<td>--dbdir &lt;директория&gt;</td>
<td>Указать директорию, в которой находится база данных программы</td>
</tr>
<tr>
<td>--debug</td>
<td>Включить режим отладки. При включении отладки вывод и содержимое стандартного лога не изменяются. Для отладочной информации создается отдельный файл, начинающийся с "/tmp/rkhunter-debug"</td>
</tr>
<tr>
<td>--disable &lt;список&gt;</td>
<td>Не выполнять указанные в списке тесты. Названия тестов разделяются запятыми без пробелов</td>
</tr>
<tr>
<td>--display-logfile</td>
<td>После окончания работы на экран будет выведен файл лога</td>
</tr>
<tr>
<td>--enable &lt;список&gt;</td>
<td>Выполнять только указанные тесты. Список тестов разделяется запятыми без пробелов</td>
</tr>
<tr>
<td>--hash &lt;алгоритм&gt;</td>
<td>команды проверки и --propupd будут использовать указанный алгоритм для подсчета контрольной суммы. Возможные значения: MD5, SHA1, SHA224, SHA256, SHA384, SHA512, NONE, а также название команды, которая будет использоваться для вычисления контрольной суммы</td>
</tr>
<tr>
<td>--lang &lt;язык&gt;
--language &lt;язык&gt;</td>
<td>Указать язык, на котором будут выводиться сообщения во время работы</td>
</tr>
<tr>
<td>-l, --logfile &lt;файл&gt;</td>
<td>Указать название файла лога</td>
</tr>
<tr>
<td>--noappend-log</td>
<td>Вернуть обычное поведение, rkhunter будет перезаписывать лог при каждом запуске</td>
</tr>
<tr>
<td>--nocf</td>
<td>Используется совместно с опцией --disable. Обычно если эта опция не указана, то не выполняются тесты, которые были указаны в командной строке, а также тесты, которые записаны в конфигурационном файле. Опция --nocf позволяет игнорировать тесты из конфигурационного файла</td>
</tr>
<tr>
<td>--no-colors</td>
<td>Не использовать цвета при отображении результатов работы</td>
</tr>
<tr>
<td>--nolog</td>
<td>Не записывать файл лога</td>
</tr>
<tr>
<td>--nomow, --no-mail-on-warning</td>
<td>Не посылать электронное письмо при появлении предупреждений</td>
</tr>
<tr>
<td>--ns, --no-summary</td>
<td>При использовании --check в конце проверки отображается суммарная информация. Данная опция ее отключает</td>
</tr>
<tr>
<td>--pkgmgr</td>
<td>Указать, какой пакетный менеджер использовать для получения информации о файле. Возможные варианты: RPM, DPKG, BSD, SOLARIS, NONE. По умолчанию NONE</td>
</tr>
<tr>
<td>-q, --quiet</td>
<td>"Тихая" работа. Информация на экран не выводится. Полезно, когда необходимо только проверить код возврата</td>
</tr>
<tr>
<td>--rwo, --report-warnings-only</td>
<td>Отображать только предупреждения</td>
</tr>
<tr>
<td>--sk, --skip-keypress</td>
<td>При проверке не ждать от пользователя нажатия клавиши для продолжения</td>
</tr>
<tr>
<td>--summary</td>
<td>Отображать суммарную информацию результатов теста. По умолчанию включено</td>
</tr>
<tr>
<td>--syslog [уровень]</td>
<td>Если используется эта опция, то начало и конец работы логгируются при помощи syslog. Если указан уровень логгирования, то они логгируются на соответствующем уровне. По умолчанию это authpriv.notice</td>
</tr>
<tr>
<td>--tmpdir &lt;директория&gt;</td>
<td>Указать директорию для создания временных файлов</td>
</tr>
<tr>
<td>--vl, --verbose-logging</td>
<td>При тестировании выводить в лог как можно больше информации</td>
</tr>
<tr>
<td>-x, --autox</td>
<td>Автоматически определять, запущена ли X Window System. Если да, то автоматически используется второй набор цветов (опция --color-set2)</td>
</tr>
<tr>
<td>-X, --no-autox</td>
<td>Не определять автоматически, запущена ли X Window System</td>
</tr>
</tbody>
</table>

Ну, вот и все. Удачи, и здоровья вашим серверам.