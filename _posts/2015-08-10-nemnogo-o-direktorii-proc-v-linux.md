---
title: Немного о директории /proc в Linux
date: 2015-08-10T23:58:21+03:00
author: Maxim Norin
layout: post
permalink: /nemnogo-o-direktorii-proc-v-linux.html
categories:
  - Статьи
tags:
  - /proc
  - linux
  - SYS
---
/proc - это не настоящая файловая система. Она виртуальная. Ее основная задача - получение состояния системы и частично выполнение управляющих действий. К сожалению, не так много людей знают, что находится внутри и как этим пользоваться. Поэтому немного расскажу, чем эта директория может быть вам полезна.
<!--more-->

## Информация о процессах в директории /proc
Информация о процессах хранится в директориях /proc/N, где N - числовой идентификатор процесса. В этой директории содержатся различные псевдо-файлы, которые содержат информацию о самом процессе и связанном с ним окружении.

__/proc/N/cmdline__ - Содержимое командной строки, которой был запущен процесс.

__/proc/N/environ__ - Описание окружения, в котором работает процесс. Оно может быть полезно для просмотра содержимого окружения, если вам надо, например, посмотреть, была ли установлена переменная окружения перед запуском программы.

__/proc/N/exe__ - Символическая ссылка на выполнимый файл запущенной программы.

__/proc/N/limits__ - Лимиты на использование системных ресурсов, актуальные для работающего процесса.

__/proc/N/mounts__ - Список смонтированных ресурсов, которые доступны процессу

__/proc/N/status__ - Статус работающей программы. Он включает в себя такую информацию как идентификатор родительского процесса, статус самого процесса, его название, его идентификатор, идентификатор пользователя и группы, группы, в которые входит владелец процесса, сколько потоков использует процесс, сколько памяти он использует и так далее.

В этой же директории содержится несколько псевдо-директорий:

__/proc/N/cwd__ - Текущая директория для процесса. Представлена символической ссылкой на директорию. Если рабочая директория для процесса изменится, изменится и ссылка.

__/proc/N/fd__ - Файловые дескрипторы, которые используются процессом. Для программы bash, например, там по умолчанию будут дескрипторы 0, 1, 2 и 255, указывающие на виртуальный терминал, в котором запущен процесс, например, /dev/pts/6.

__/proc/N/fdinfo__ - Информация о файловых дескрипторах. Каждый файл в этой директории содержит поля pos (позиция курсора), flags (флаги, с которыми этот дескриптор был открыт) и mnt_id (идентификатор точки монтирования из списка, содержащегося в файле /proc/N/mountinfo)

__/proc/N/root__ - Символическая ссылка на директорию, которая для данного процесса является корневой

__/proc/N/net__ - Сетевые системные ресурсы и их параметры, действующие для конкретного процесса.

## Общесистемные псевдо-файлы и псевдо-директории
__/proc/acpi__ - директория, связанная с управлением питанием и различным устройствами. От компьютера к компьютеру содержимое этой директории отличается. Через эту директорию можно посылать управляющие сигналы устройствам. Например: в моем ноутбуке есть кнопка включения подсветки для клавиатуры. Сочетание клавиш, которые для этого используются, - это Fn плюс кнопка с изображением лампочки на клавиатуре. Но я могу включать и выключать подсветку программно.
Включить:
```
echo "on" > /proc/acpi/ibm/light
```
Выключить:
```
echo "off" > /proc/acpi/ibm/light
```
Таким образом, можно на некоторое событие в системе повесить обработчик (на баше), который будет мигать лампочкой, если пришло письмо или произошло еще что-нибудь. Однако, возможность таким образом работать с устройствами зависит от модели ноутбука и обычно драйверов устройств. Устройства именуются очень часто по-разному, и вам надо будет предварительно посмотреть, что у вас есть и как это можно использовать.

Через директорию /proc/acpi также, например, можно проверить, открыт или закрыт ноутбук. Для этого на моем ноутбуке можно проверить содержимое файла /proc/acpi/button/lid/LID/state. Если в файле содержится "state:   open", значит крышка открыта.

__/proc/asound__ - директория, связанная со звуковыми устройствами. Например, файл /proc/asound/cards содержит звуковые карты, доступные в системе.

__/proc/bus__ - информация о системных шинах и устройствах, которые к ним подключены. Например, файл /proc/bus/input/devices содержит информацию о различных устройствах, список можно получить такой командой:
```
grep "Name" /proc/bus/input/devices
```
__/proc/fs__ - Информация о файловых системах. Например, вам нужно посмотреть опции, с которыми в данный момент работает файловая система ext4, размещенная на разделе sda2. Это можно сделать командой
```
cat /proc/fs/ext4/sda2/options
```
__/proc/sys__ - Псевдо-директория, содержащая массу информации о системе. К примеру, файл /proc/sys/dev/cdrom/info содержит информацию о CD/DVD-ROM, который установлен в системе. Выглядит эта информация так:
```
CD-ROM information, Id: cdrom.c 3.20 2003/12/17

drive name:             sr0
drive speed:            24
drive # of slots:       1
Can close tray:         1
Can open tray:          1
Can lock tray:          1
Can change speed:       1
Can select disk:        0
Can read multisession:  1
Can read MCN:           1
Reports media changed:  1
Can play audio:         1
Can write CD-R:         1
Can write CD-RW:        1
Can read DVD:           1
Can write DVD-R:        1
Can write DVD-RAM:      1
Can read MRW:           1
Can write MRW:          1
Can write RAM:          1
```
В общем-то, всё понятно. Название устройства и различные его характеристики, названные человеческими словами. Как видно выше, мой DVD-RW много чего умеет, но не умеет выбирать диски.

__/proc/sys/fs__ - Как понятно по названию, псевдо-директория, содержащая информацию о подсистеме, связанной с файловыми системами. Опять же, пример: /proc/sys/fs/pipe-max-size. Этот псевдо-файл содержит максимальный размер буфера пайпа. Поэтому если вдруг у вас некорректно работает какой-то скрипт, в котором данные передаются через пайп, есть повод задуматься, какого размера у вас буфер пайпа и заглянуть в этот псевдо-файл. Возможно вы после этого захотите воспользоваться командами "exec" и "read" с опцией "-u".

__/proc/sys/net__ - Общесистемные сетевые параметры. Например:
```
echo "1" > /proc/sys/net/ipv4/ip_forward
```
Включить форвардинг пакетов между двумя сетевыми интерфейсами.

__/proc/sys/vm__ - Параметры виртуальной памяти. Например, вы можете сбросить кэш и освободить немного памяти, используемой под кэш командой
```
echo "3" > /proc/sys/vm/drop_caches
```
В общем, для начала знакомства с этой директорией достаточно, но я рекомендую по возможности более плотно познакомиться с этой директорией и ее содержимым. Более подробную информацию можно найти в странице руководств в 5 разделе
```
man 5 proc
```
Когда вы познакомитесь с (псевдо)директорией /proc, вы сможете получать массу информации о системе без всяких утилит, просто при помощи команды cat.