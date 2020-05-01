---
title: Обновление AIX 5.3 до 6.1
date: 2011-07-01T11:23:46+03:00
author: Maxim Norin
layout: post
permalink: /obnovlenie-aix-5-3-do-6-1.html
categories:
  - Статьи
tags:
  - "5.3"
  - "6.1"
  - aix
  - ibm
  - upgrade
  - апгрейд
  - обновление
---
Данный документ описывает обновление операционной системы IBM AIX версии 5.3 до версии 6.1 на серверах IBM pSeries.

## Подготовка

1) Внимательно прочитайте release notes к той версии, до которой будете обновляться. Почитать можно здесь
2) Сделайте резервные копии всей необходимой информации.
3) Убедитесь, что всё оборудование, включая внешние устройства, подключено к серверу и находится во включенном состоянии.
<!--more-->

4) Проверьте наличие ошибок в работе командой
```
errpt -a
```
и исправьте их, если это необходимо.

5) Убедитесь, что на сервере достаточно свободного дискового пространства.
6) Убедитесь, что на сервере существует группа разделов rootvg. Это можно сделать командой
```
lsvg -p rootvg
```
7) Убедитесь, что CDROM в списке загрузочных устройств стоит перед жестким диском:
```
bootlist -m normal -o
```
Если CDROM находится не перед жестким диском, измените порядок устройств загрузки:
```
bootlist -m normal cd0 hdisk0
```
8) Рекомендуется предварительно перезагрузить сервер, чтобы убедиться, что, если перезагрузка происходит с ошибками, ошибки перезагрузки не связаны с обновлением системы.
9) Для перезагрузки сервера остановите все приложения и перезагрузитесь командой
```
shutdown -Fr
```
10) Также предварительно проверьте, что для всех пакетов установлены требуемые зависимости, и что все пакеты до конца установлены:
```
lppchk -v
```

## Обновление

Итак, вставьте диск с операционной системой IBM AIX 6.1 в CD/DVD-дисковод сервера.
Перезагрузите сервер командой
```
reboot -Fr
```
Сервер загрузится с CD/DVD-диска и вы увидите экран с выбором опций.
```
-------------------------------------------------------------

1 = SMS menu 5 = Default bootlist
8 = Open Firmware 6 = Stored bootlist
-------------------------------------------------------------
```
Ничего нажимать не нужно. Необходимо подождать.
Появится следующий экран:
```
-------------------------------------------------------------------------

Starting Software
Please wait
-------------------------------------------------------------------------
```
Ждём. Затем появится следующий экран:
```
--------------------------------------------------------------------------

Please define the System Console
Type a 1 and press Enter to use this terminal as the system console
--------------------------------------------------------------------------
```
Нажимаем 1 и затем Enter.
```
--------------------------------------------------------------------------

1. Type 1 and press Enter to have English during install
>>>choice[1]:
--------------------------------------------------------------------------
```
Еще раз 1 и затем Enter.
```
--------------------------------------------------------------------------

Welcome to Base Operating System Installation and Maintenance
Type the number of your choice and press Enter. Choice is indicated by >>>
>>>1. Start Install now with Default Settings
2. Change/show Installation Settings and Install
3. Start Maintenance Mode for System Recovery
4. Configure Network Disks (iSCSI)
88 Help?
99 Previous Menu
Choice[1]: 2
--------------------------------------------------------------------------
```
Нажмите 2 и затем Enter для изменения параметров установки.
```
---------------------------------------------------------------------------------

Installation and Settings
Either type 0 and press Enter to install with current settings
or type the number of the setting you want to change and press Enter
1. System Settings:
Method of Installation ......................Migration
Disk where you want to Install ..............hdisk0
2. Primary Language Environment settings (AFTER Install):
Cultural Conventions ........................English (United States)
Language ....................................English (United States)
Keyboard ....................................English (United States)
Keyboard Type ...............................Default
3. Security Model...............................Default
4. More Options (Software install options)
>>> 0 Install with the current settings listed above
88 Help?
99 Previous Menu
>>> Choice[0]:
---------------------------------------------------------------------------------
```
Если пункт "Method of Installation" отличается от "Migration", измените его на "Migration".

Убедитесь, что диск в пункте "Disk where you want to Install" выбран правильно.

Параметры "Cultural Conventions", "Language", "Keyboard" можете выставить в те, которые предпочитаете для работы.

После выбора настроек нажмите 4 и Enter для перехода к дополнительным настройкам установки программного обеспечения.
```
----------------------------------------------------------------------------------

Install Options
1. Enable System Backup to install any system....Yes
2. Import User Volume Groups.....................Yes
3. Remove Java 1.1.8 Software....................No
>>> 0 Install with the current settings listed above
88 Help?
99 Previous Menu
>>> Choice[0]:
----------------------------------------------------------------------------------
```
Проверьте, что значения настроек установлены так, как указано выше и нажмите Enter для выбора выделенного пункта "0 Install with the current settings listed above".
```
----------------------------------------------------------------------------------

Migration Installation Summary
Disks: hdisk0
Cultural Convention: en_GB
Language: en_US
Keyboard: en_GB
Import User Volume Groups: Yes
Enable System Backup to install any system: Yes
Remove Java 1.1.8 Software: No
>>> 1 Continue with Install
88 Help?
99 Previous Menu
>>> Choice[1]:
----------------------------------------------------------------------------------
```
Еще раз проверьте значения, удостоверьтесь, что всё указано правильно, а затем нажмите Enter для выбора пункта "1 Continue with Install"
```
----------------------------------------------------------------------------------

Migration menu preparation
Please wait
Approximate Elapsed time
% task complete (in minutes)
----------------------------------------------------------------------------------
```
Подождите. Появится следующий экран.
```
------------------------------------------------------------------------------------------

Migration Confirmation
Either type 0 and press Enter to continue the installation
or type the number of your choice and press Enter
1. List the saved Base System Configuration files which will not be merged into the system
These files are saved in /tmp/bos
2. List the filesets which will be removed and not replaced
3. List directories which will have all current contents removed
4. Reboot without migrating
Acceptance of license agreements is required before using system.
You will be prompted to accept after the system reboots
>>> 0 Continue with the migration
WARNING: Selected files, directories and filesets (installable options)
from the Base System will be removed.
Choose 2 or 3 for more information
88 Help?
99 Previous Menu
>>> Choice[0]:
------------------------------------------------------------------------------------------
```
При желании можете посмотреть пункты 1,2,3, а можете сразу ввести 0 или просто нажать Enter, поскольку пункт "0 Continue with the migration" выбран по умолчанию.
```
----------------------------------------------------------------------------

Saving system configuration files in /tmp/bos
Removing obsolete filesets, directories and files
Installing Base Operating System
Please wait
Approximate Elapsed time
% task complete (in minutes)
----------------------------------------------------------------------------
```
Процесс инсталляции продолжится без вашего участия и завершится.
По окончании вы увидите приглашения входа в систему.
Войдите в систему и проверьте версию операционной системы командой
```
oslevel -s
```
Вот и всё. Теперь можно устанавливать последние обновления.

Оригинал статьи здесь: http://www.computers-it.com/aix/aix_upgrade_AIX_5.3_to_6.1.php