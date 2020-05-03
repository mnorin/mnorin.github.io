---
title: Первичный домен-контроллер на Samba 3
date: 2015-02-01T00:53:05+03:00
author: Maxim Norin
layout: post
permalink: /samba-primary-domain-controller.html
categories:
  - Статьи
tags:
  - debian
  - domain
  - linux
  - samba
  - windows 7
  - windows xp
  - домен
---
Первичный домен-контроллер на Samba позволяет создать собственный домен с использованием компьютера под управлением операционной системы на базе ядра Linux. Это нужно в первую очередь для централизованной аутентификации. При входе в домен вы можете войти в систему под своей учетной записью на любом компьютере, который включен в домен. Давайте посмотрим, как же настроить первичный домен-контроллер на Samba.
<!--more-->

Рассматривать будем на примере операционной системы Debian GNU/Linux, но принципиального различия с другими операционными системами нет. Рассматривать будем Samba 3. Установка и настройка производится под пользователем root, либо с использованием sudo. Домен, который у нас в итоге получится, не является аналогом Active Directory, наш домен будет более старой спецификации, домен NT. Но его уже достаточно для решения задачи централизованной аутентификации и выделения прав на определенные действия в операционной системе.

## Установка Samba
В первую очередь установим пакеты, которые нам будут нужны:
```
apt-get install samba
```
По зависимостям установится еще несколько пакетов

## Настройка Samba
### Глобальные настройки
Перед началом настройки сохраните файл /etc/samba/smb.conf
```
mv /etc/samba/smb.conf /etc/samba/smb.conf.bak
```
Теперь создадим пустой файл, в который будем записывать настройки
```
touch /etc/samba/smb.conf
```
Вот что нужно туда записать:
```
[global]
workgroup = mydomain 
netbios name = dc1 
server string = Samba Primary Domain Controller 
domain master = yes 
preferred master = yes 
domain logons = yes 
add machine script = /usr/sbin/useradd -N -g Computers -c Machine -d /var/lib/samba -s /bin/false %u 
security = user 
encrypt passwords = yes 
wins support = yes 
name resolve order = wins lmhosts hosts bcast 

logon path = \\%N\%U\profile 
logon drive = Z: 
logon home = \\%N\%U
writable = yes
```
Параметры "workgroup" и "netbios name" - это название вашего домена и имя домен-контроллера, измените их на свои. А если у вас уже есть WINS-сервер, закомментируйте строчку "wins support = yes", при наличии этой настройки сервер Samba будет работать как сервер Netbios. Настройки "logon path", "logon drive" и "logon home" нужны для хранения пользовательского профиля. При входе на другую машину профиль будет загружаться с домен-контроллера и пользователь сможет работать со своими настройками.

### Создание файла LMHOSTS
В файле /etc/samba/lmhosts содержатся такие же записи, как и в файле /etc/hosts, но это не имена хостов, а Netbios-имена. Когда компьютер под управлением Windows включается в домен MYDOMAIN, Windows пытается найти адрес первичного домен-контроллера, и если ей это не удается, машина в домен включена не будет. Если название вашего домена MYDOMAIN, имя домен-контроллера dc1, а его IP-адрес 192.168.0.2, то файл будет выглядеть следующим образом:
```
192.168.0.2 mydomain
192.168.0.2 dc1
```

### Настройка секции [netlogon]
В файл /etc/samba/smb.conf добавляем секцию [netlogon]
```
[netlogon]
path = /var/lib/samba/netlogon 
browseable = no 
read only = no 
create mask = 0700 
directory mask = 0700 
valid users = %S
```
Директория /var/lib/samba/netlogon - это стартовая директория при входе в домен. При входе пользователя в домен будет выполнен скрипт netlogon.bat, размещенный в этой директории. Если такой директории нет, ее нужно создать и выставить права 755:
```
mkdir /var/lib/samba/netlogon
chmod 755 /var/lib/samba/netlogon
```
В файле netlogon.bat можно настраивать окружение, или, например, подключать сетевой диск при входе в домен:
```
net use s: \\dc1\share
```
### Настройка секции [homes]
Эта секция файла /etc/samba/smb.conf используется для настройки домашних директорий пользователей
```
[homes]
valid users = %S
guest ok = yes
read only = no
create mask = 0700
directory mask = 0700
browsable = no
```
Сохраняем и проверяем конфигурацию командой
```
testparm
```
Если при ее выполнении возникают ошибки, исправьте их и проверьте еще раз. Если ошибок нет, то можно рестартовать сервис
```
service samba restart
```
### Первичный домен-контроллер на Samba настроен
Теперь необходимо разобраться с добавлением пользователей, выделением им прав и включением компьютеров с Windows в домен.

## Создание доменных пользователей
### Добавление администратора домена
Учетная запись администратора называется root, надо добавить ее следующей командой:
```
smbpasswd -a root
```
Не используйте такой же пароль, как у пользователя root в системе, это разные учетные записи, и у них должны быть разные пароли.

### Добавление группы для компьютеров
Создадим группу с названием "Computers"
```
groupadd Computers
```
### Создание пользовательской учетной записи
Создадим учетную запись, которая будет использоваться только совместно с Samba
```
useradd iivanov
smbpasswd -a iivanov
```
Введите пароль два раза.

Теперь нужно выдать этому пользователю права:
```
net rpc rights grant "MYDOMAIN\iivanov" SeMachineAccountPrivilege \
SePrintOperatorPrivilege SeAddUsersPrivilege SeDiskOperatorPrivilege \
SeRemoteShutdownPrivilege
```
Вот права, которые можно выдавать пользователю:
<table>
<tbody>
<tr>
<td width="120">SeMachineAccountPrivilege</td>
<td>Разрешение на добавление машин в домен</td>
</tr>
<tr>
<td>SeTakeOwnershipPrivilege</td>
<td>Разрешение на перехват прав на файлы и другие объекты</td>
</tr>
<tr>
<td>SeBackupPrivilege</td>
<td>Разрешение на создание резервных копий файлов и директорий</td>
</tr>
<tr>
<td>SeRestorePrivilege</td>
<td>Разрешение на восстановление файлов и директорий из резервных копий</td>
</tr>
<tr>
<td>SeRemoteShutdownPrivilege</td>
<td>Разрешение на форсированное выключение с удаленной системы</td>
</tr>
<tr>
<td>SePrintOperatorPrivilege</td>
<td>Разрешение на управление принтерами</td>
</tr>
<tr>
<td>SeAddUsersPrivilege</td>
<td>Разрешение на добавление пользователей и групп в домен</td>
</tr>
<tr>
<td>SeDiskOperatorPrivilege</td>
<td>Разрешение на управление папками общего доступа</td>
</tr>
<tr>
<td>SeSecurityPrivilege</td>
<td>Разрешение на управление системной безопасностью</td>
</tr>
</tbody>
</table>

При выдаче прав будет запрошен пароль администратора Samba root (тот пароль, который мы установили чуть раньше). И теперь нужно сделать маппинг NT-группы пользователей на unix-группу:
```
groupadd admins
net groupmap add ntgroup="Administrators" unixgroup=admins rid=512 type=d
```
Для того, чтобы учетная запись созданного пользователя имела права администратора домена, ее необходимо добавить в ту группу, на которую сделан маппинг доменной группы "Administrators".
```
adduser iivanov admins
```
Теперь можно включить машину под управлением Windows в домен

## Включение машины в домен
Для машин под управлением Windows XP машину можно сразу включать в домен и потом входить под своей доменной учетной записью. Для Windows 7 нужно сделать еще кое-что. Нужно внести в реестр следующие изменения:
```
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\LanmanWorkstation\Parameters]
"DomainCompatibilityMode"=dword:00000001
"DNSNameResolutionRequired"=dword:00000000
```
После этого можно включать машину в домен.