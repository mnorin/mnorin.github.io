---
title: Приоритеты репозиториев apt и deb-multimedia.org
date: 2017-01-07T03:12:20+03:00
author: Maxim Norin
layout: post
permalink: /prioritety-repozitoriev-apt.html
categories:
  - Статьи
tags:
  - apt
  - apt-get
  - deb
  - deb-multimedia.org
  - debian
  - DMO
  - preferences
  - приоритет
---
Приоритеты репозиториев apt позволяют исправить проблемы, похожие на ту, что описана [здесь](/curl-35-gnutls_handshake-failed.html). В общих чертах суть проблемы выглядит так: из неофициального репозитория устанавливаются пакеты, версия которых считается более высокой, чем версия пакета из официального репозитория. Один из репозиториев, с которыми возникают такие проблемы, - deb-multimedia.org. Вот как можно решить проблемы с пакетами DMO, используя приоритеты репозиториев менеджера пакетов APT.
<!--more-->

Проверяем, что у нас установлено из репозитория deb-multimedia.org:
```
$ dpkg -l | grep dmo
ii  avidemux                  1:2.6.8-dmo8           amd64        Free video editor
ii  avidemux-common           1:2.6.8-dmo8           all          Free video editor (Internationalization files)
ii  avidemux-plugins          1:2.6.8-dmo8           amd64        Free video editor (plugins)
ii  avidemux-qt               1:2.6.8-dmo8           amd64        Free video editor (QT version)
ii  libaacplus2:amd64         2.0.2-dmo2             amd64        AAC+ encoding library - runtime files
ii  libaacplus2:i386          2.0.2-dmo2             i386         AAC+ encoding library - runtime files
ii  libaacs0:amd64            0.8.1-dmo1             amd64        free-and-libre implementation of AACS
ii  libaften0:amd64           0.0.8svn20100103-dmo1  amd64        audio AC3 encoder - runtime files
ii  libavcodec56:amd64        10:2.6.9-dmo1          amd64        Library to encode decode multimedia streams - runtime files
ii  libavresample2:amd64      10:2.6.9-dmo1          amd64        FFmpeg audio conversion library
ii  libavresample2:i386       10:2.6.9-dmo1          i386         FFmpeg audio conversion library
ii  libavutil54:amd64         10:2.6.9-dmo1          amd64        FFmpeg avutil library - runtime files
ii  libavutil54:i386          10:2.6.9-dmo1          i386         FFmpeg avutil library - runtime files
ii  libbluray1:amd64          2:0.7.0-dmo1           amd64        Blu-ray disc playback support library (shared library)
ii  libdca0:amd64             0.0.5-dmo2             amd64        decoding library for DTS Coherent Acoustics streams
ii  libdvdcss2:amd64          1.3.0-dmo1             amd64        Simple foundation for reading DVDs - runtime libraries
ii  libfaac0:amd64            1:1.28-dmo3            amd64        AAC audio encoder - library files.
ii  libfaac0:i386             1:1.28-dmo3            i386         AAC audio encoder - library files.
ii  libfdk-aac1:amd64         1:0.1.4-dmo1           amd64        Fraunhofer FDK AAC codec library.
ii  libfdk-aac1:i386          1:0.1.4-dmo1           i386         Fraunhofer FDK AAC codec library.
ii  libguess1:amd64           1.2-dmo2               amd64        High-speed character set detection library.
ii  liblavfile-2.1-0:amd64    2:2.1.0-dmo6           amd64        MJPEG capture/editing/replay and MPEG encoding toolset (library)
ii  liblavjpeg-2.1-0:amd64    2:2.1.0-dmo6           amd64        MJPEG capture/editing/replay and MPEG encoding toolset (library)
ii  liblavplay-2.1-0:amd64    2:2.1.0-dmo6           amd64        MJPEG capture/editing/replay and MPEG encoding toolset (library)
ii  liblsmash2                2.3.0-dmo1             amd64        Loyal to Spec of Mpeg4 and Ad-hoc Simple Hackwork
ii  libmjpegutils-2.1-0:amd64 2:2.1.0-dmo6           amd64        MJPEG capture/editing/replay and MPEG encoding toolset (library)
ii  libmp3lame-dev:amd64      1:3.99.5-dmo4          amd64        Development files for LAME
ii  libmp3lame0:amd64         1:3.99.5-dmo4          amd64        Shared libraries for MP3 encoding
ii  libmp3lame0:i386          1:3.99.5-dmo4          i386         Shared libraries for MP3 encoding
ii  libmpeg2encpp-2.1-0:amd64 2:2.1.0-dmo6           amd64        MJPEG capture/editing/replay and MPEG encoding toolset (library)
ii  libmplex2-2.1-0:amd64     2:2.1.0-dmo6           amd64        MJPEG capture/editing/replay and MPEG encoding toolset (library)
ii  libpostproc53:amd64       10:2.6.9-dmo1          amd64        postproc shared libraries
ii  libquicktime2:amd64       3:1.2.4-dmo11          amd64        Library for reading and writing Quicktime files
ii  libswresample1:amd64      10:2.6.9-dmo1          amd64        FFmpeg audio rescaling library
ii  libswresample1:i386       10:2.6.9-dmo1          i386         FFmpeg audio rescaling library
ii  libswscale3:amd64         10:2.6.9-dmo1          amd64        FFmpeg video scaling library
ii  libutvideo15:amd64        15.1.0-dmo2            amd64        Ut Video Codec Suite library
ii  libvidstab1.0             2:0.98b-dmo1           amd64        Video stabilization library.
ii  libx264-146:amd64         3:0.146.2538+git121396c-dmo1 amd64        x264 video coding library
ii  libx264-146:i386          3:0.146.2538+git121396c-dmo1 i386         x264 video coding library
ii  libx265-51:amd64          1.6-dmo1               amd64        x265 video coding library
ii  libxvidcore4:amd64        3:1.3.3-dmo1           amd64        High quality ISO MPEG4 codec library
ii  libxvidcore4:i386         3:1.3.3-dmo1           i386         High quality ISO MPEG4 codec library
ii  mjpegtools                2:2.1.0-dmo6           amd64        MJPEG video capture/editting/playback MPEG encoding
ii  vdpau-va-driver:amd64     0.7.4-dmo5             amd64        VDPAU-based backend for VA API
ii  w64codecs:amd64           1:20071007-dmo2        amd64        win64 binary codecs
ii  x11proto-xf86vidmode-dev  2.3.1-2                all          X11 Video Mode extension wire protocol
```
Достаточно много. Некоторые пакеты не обновлялись годами, поэтому есть вероятность проблем совместимости с новыми версиями программ из официального репозитория. Создаем файл /etc/apt/preferences. В нем указываем соответствующие приоритеты. Я использую ветку stretch, поэтому указываю, что должны использоваться пакеты из stretch и пакеты из репозитория debian.org, даже если они более старой версии. Для репозитория deb-multimedia.org ставим минимальный приоритет, пакеты оттуда будут устанавливаться только в том случае, если они отсутствуют в официальном репозитории.

Приоритеты репозиториев apt имеют следующие значения:
- P >= 1000 - Устанавливать пакеты, даже если это приводит к установке более старой версии, чем текущая установленная
- 990 <= P < 1000 - Устанавливать версию пакетов, даже если она не входит в текущий релиз, за исключением случая, если установленная версия новее.
- 500 <= P < 990 - Устанавливать пакеты, если они отсутствуют в текущем релизе, и устанавливаемая версия новее.
- 100 <= P < 500 - Устанавливать пакеты, если нет доступной версии в других репозиториях или установленная версия старее.
- 0 < P < 100 - Устанавливать пакет только в том случае, если нет другой установленной версии.
- P < 0 - Предотвратить установку указанной версии
- P = 0 - Неопределенное поведение, не рекомендуется использовать

Более подробно почитать о приоритетах и параметрах приоритезации можно в man-страничке (man apt_preferences) или [здесь](https://wiki.debian.org/AptPreferences).

Сам файл /etc/apt/preferences:
```
Package: *
Pin: origin *.debian.org
Pin-Priority: 1000

Package: *
Pin: release a=stretch
Pin-Priority: 1000

Package: *
Pin: origin *.deb-multimedia.org
Pin-Priority: 1
```
Обновляемся
```
$ sudo apt-get upgrade
Чтение списков пакетов… Готово
Построение дерева зависимостей       
Чтение информации о состоянии… Готово
Расчёт обновлений… Готово
Пакеты, которые будут оставлены в неизменном виде:
  shutter
Пакеты, будут заменены на более СТАРЫЕ версии:
  base-files libart-2.0-2 libbluray1 libdca0 libfaac0 libfaac0:i386 libguess1 liblavfile-2.1-0 liblavjpeg-2.1-0 liblavplay-2.1-0 libmjpegutils-2.1-0 libmpeg2encpp-2.1-0 libmplex2-2.1-0 libquicktime2 libxvidcore4 libxvidcore4:i386 nikto vdpau-va-driver
обновлено 0, установлено 0 новых пакетов, 18 пакетов заменены на старые версии, для удаления отмечено 0 пакетов, и 8 пакетов не обновлено.
Необходимо скачать 1 763 kБ архивов.
После данной операции, объём занятого дискового пространства уменьшится на 489 kB.
Хотите продолжить? [Д/н] 
Пол:1 http://ftp.nz.debian.org/debian stretch/main amd64 base-files amd64 9.7 [57,6 kB]
Пол:2 http://ftp.nz.debian.org/debian stretch/main amd64 libart-2.0-2 amd64 2.3.21-2 [72,7 kB]
Пол:3 http://ftp.nz.debian.org/debian stretch/main amd64 libbluray1 amd64 1:0.9.3-3 [137 kB]
Пол:4 http://ftp.nz.debian.org/debian stretch/main amd64 libdca0 amd64 0.0.5-10 [105 kB]
Пол:5 http://ftp.nz.debian.org/debian stretch/non-free amd64 libfaac0 amd64 1.28+cvs20151130-1 [51,4 kB]
Пол:6 http://ftp.nz.debian.org/debian stretch/non-free i386 libfaac0 i386 1.28+cvs20151130-1 [52,2 kB]
Пол:7 http://ftp.nz.debian.org/debian stretch/main amd64 libguess1 amd64 1.2-1.1 [10,1 kB]
Пол:8 http://ftp.nz.debian.org/debian stretch/main amd64 libmjpegutils-2.1-0 amd64 1:2.1.0+debian-5 [27,8 kB]
Пол:9 http://ftp.nz.debian.org/debian stretch/main amd64 libquicktime2 amd64 2:1.2.4-9 [265 kB]
Пол:10 http://ftp.nz.debian.org/debian stretch/main amd64 liblavfile-2.1-0 amd64 1:2.1.0+debian-5 [40,4 kB]
Пол:11 http://ftp.nz.debian.org/debian stretch/main amd64 liblavjpeg-2.1-0 amd64 1:2.1.0+debian-5 [16,0 kB]
Пол:12 http://ftp.nz.debian.org/debian stretch/main amd64 liblavplay-2.1-0 amd64 1:2.1.0+debian-5 [25,4 kB]
Пол:13 http://ftp.nz.debian.org/debian stretch/main amd64 libmpeg2encpp-2.1-0 amd64 1:2.1.0+debian-5 [74,2 kB]
Пол:14 http://ftp.nz.debian.org/debian stretch/main amd64 libmplex2-2.1-0 amd64 1:2.1.0+debian-5 [49,6 kB]
Пол:15 http://ftp.nz.debian.org/debian stretch/main amd64 libxvidcore4 amd64 2:1.3.4-1 [246 kB]
Пол:16 http://ftp.nz.debian.org/debian stretch/main i386 libxvidcore4 i386 2:1.3.4-1 [239 kB]
Пол:17 http://ftp.nz.debian.org/debian stretch/main amd64 vdpau-va-driver amd64 0.7.4-6 [43,5 kB]
Пол:18 http://ftp.nz.debian.org/debian stretch/non-free amd64 nikto all 1:2.1.5-1 [251 kB]
Получено 1 763 kБ за 3с (571 kБ/c)
dpkg: предупреждение: снижение версии base-files с 1:1.1.0 до 9.7
(Чтение базы данных … на данный момент установлено 344724 файла и каталога.)
Подготовка к распаковке …/base-files_9.7_amd64.deb …
Распаковывается base-files (9.7) на замену (1:1.1.0) …
dpkg: предупреждение: не удалось удалить старый каталог «/mnt»: Каталог не пуст
Настраивается пакет base-files (9.7) …

Файл настройки «/etc/debian_version»
 ==> Изменён с момента установки (вами или сценарием).
 ==> Автор пакета предоставил обновлённую версию.
  Что нужно сделать? Есть следующие варианты:
   Y или I : установить версию, предлагаемую сопровождающим пакета
   N или O : оставить установленную на данный момент версию
      D    : показать различия между версиями
      Z    : запустить оболочку командной строки для проверки ситуации
 По умолчанию сохраняется текущая версия файла настройки.
*** debian_version (Y/I/N/O/D/Z) [по умолчанию N] ? Y
Устанавливается новая версия файла настройки /etc/debian_version …

Файл настройки «/etc/issue»
 ==> Изменён с момента установки (вами или сценарием).
 ==> Автор пакета предоставил обновлённую версию.
  Что нужно сделать? Есть следующие варианты:
   Y или I : установить версию, предлагаемую сопровождающим пакета
   N или O : оставить установленную на данный момент версию
      D    : показать различия между версиями
      Z    : запустить оболочку командной строки для проверки ситуации
 По умолчанию сохраняется текущая версия файла настройки.
*** issue (Y/I/N/O/D/Z) [по умолчанию N] ? Y
Устанавливается новая версия файла настройки /etc/issue …

Файл настройки «/etc/issue.net»
 ==> Изменён с момента установки (вами или сценарием).
 ==> Автор пакета предоставил обновлённую версию.
  Что нужно сделать? Есть следующие варианты:
   Y или I : установить версию, предлагаемую сопровождающим пакета
   N или O : оставить установленную на данный момент версию
      D    : показать различия между версиями
      Z    : запустить оболочку командной строки для проверки ситуации
 По умолчанию сохраняется текущая версия файла настройки.
*** issue.net (Y/I/N/O/D/Z) [по умолчанию N] ? Y
Устанавливается новая версия файла настройки /etc/issue.net …
Updating /etc/profile to current default.
Updating /root/.profile to current default.
dpkg: предупреждение: снижение версии libart-2.0-2:amd64 с 4:14.0.0-r5-0debian7.0.0+pr9 до 2.3.21-2
(Чтение базы данных … на данный момент установлено 344723 файла и каталога.)
Подготовка к распаковке …/00-libart-2.0-2_2.3.21-2_amd64.deb …
Распаковывается libart-2.0-2:amd64 (2.3.21-2) на замену (4:14.0.0-r5-0debian7.0.0+pr9) …
dpkg: предупреждение: снижение версии libbluray1:amd64 с 2:0.7.0-dmo1 до 1:0.9.3-3
Подготовка к распаковке …/01-libbluray1_1%3a0.9.3-3_amd64.deb …
Распаковывается libbluray1:amd64 (1:0.9.3-3) на замену (2:0.7.0-dmo1) …
dpkg: предупреждение: снижение версии libdca0:amd64 с 0.0.5-dmo2 до 0.0.5-10
Подготовка к распаковке …/02-libdca0_0.0.5-10_amd64.deb …
Распаковывается libdca0:amd64 (0.0.5-10) на замену (0.0.5-dmo2) …
dpkg: предупреждение: снижение версии libfaac0:amd64 с 1:1.28-dmo3 до 1.28+cvs20151130-1
Подготовка к распаковке …/03-libfaac0_1.28+cvs20151130-1_amd64.deb …
Деконфигурируется libfaac0:i386 (1:1.28-dmo3) …
Распаковывается libfaac0:amd64 (1.28+cvs20151130-1) на замену (1:1.28-dmo3) …
dpkg: предупреждение: снижение версии libfaac0:i386 с 1:1.28-dmo3 до 1.28+cvs20151130-1
Подготовка к распаковке …/04-libfaac0_1.28+cvs20151130-1_i386.deb …
Распаковывается libfaac0:i386 (1.28+cvs20151130-1) на замену (1:1.28-dmo3) …
dpkg: предупреждение: снижение версии libguess1:amd64 с 1.2-dmo2 до 1.2-1.1
Подготовка к распаковке …/05-libguess1_1.2-1.1_amd64.deb …
Распаковывается libguess1:amd64 (1.2-1.1) на замену (1.2-dmo2) …
dpkg: предупреждение: снижение версии libmjpegutils-2.1-0:amd64 с 2:2.1.0-dmo6 до 1:2.1.0+debian-5
Подготовка к распаковке …/06-libmjpegutils-2.1-0_1%3a2.1.0+debian-5_amd64.deb …
Распаковывается libmjpegutils-2.1-0 (1:2.1.0+debian-5) на замену (2:2.1.0-dmo6) …
dpkg: предупреждение: снижение версии libquicktime2:amd64 с 3:1.2.4-dmo11 до 2:1.2.4-9
Подготовка к распаковке …/07-libquicktime2_2%3a1.2.4-9_amd64.deb …
Распаковывается libquicktime2:amd64 (2:1.2.4-9) на замену (3:1.2.4-dmo11) …
dpkg: предупреждение: снижение версии liblavfile-2.1-0:amd64 с 2:2.1.0-dmo6 до 1:2.1.0+debian-5
Подготовка к распаковке …/08-liblavfile-2.1-0_1%3a2.1.0+debian-5_amd64.deb …
Распаковывается liblavfile-2.1-0 (1:2.1.0+debian-5) на замену (2:2.1.0-dmo6) …
dpkg: предупреждение: снижение версии liblavjpeg-2.1-0:amd64 с 2:2.1.0-dmo6 до 1:2.1.0+debian-5
Подготовка к распаковке …/09-liblavjpeg-2.1-0_1%3a2.1.0+debian-5_amd64.deb …
Распаковывается liblavjpeg-2.1-0 (1:2.1.0+debian-5) на замену (2:2.1.0-dmo6) …
dpkg: предупреждение: снижение версии liblavplay-2.1-0:amd64 с 2:2.1.0-dmo6 до 1:2.1.0+debian-5
Подготовка к распаковке …/10-liblavplay-2.1-0_1%3a2.1.0+debian-5_amd64.deb …
Распаковывается liblavplay-2.1-0 (1:2.1.0+debian-5) на замену (2:2.1.0-dmo6) …
dpkg: предупреждение: снижение версии libmpeg2encpp-2.1-0:amd64 с 2:2.1.0-dmo6 до 1:2.1.0+debian-5
Подготовка к распаковке …/11-libmpeg2encpp-2.1-0_1%3a2.1.0+debian-5_amd64.deb …
Распаковывается libmpeg2encpp-2.1-0 (1:2.1.0+debian-5) на замену (2:2.1.0-dmo6) …
dpkg: предупреждение: снижение версии libmplex2-2.1-0:amd64 с 2:2.1.0-dmo6 до 1:2.1.0+debian-5
Подготовка к распаковке …/12-libmplex2-2.1-0_1%3a2.1.0+debian-5_amd64.deb …
Распаковывается libmplex2-2.1-0 (1:2.1.0+debian-5) на замену (2:2.1.0-dmo6) …
dpkg: предупреждение: снижение версии libxvidcore4:amd64 с 3:1.3.3-dmo1 до 2:1.3.4-1
Подготовка к распаковке …/13-libxvidcore4_2%3a1.3.4-1_amd64.deb …
Деконфигурируется libxvidcore4:i386 (3:1.3.3-dmo1) …
Распаковывается libxvidcore4:amd64 (2:1.3.4-1) на замену (3:1.3.3-dmo1) …
dpkg: предупреждение: снижение версии libxvidcore4:i386 с 3:1.3.3-dmo1 до 2:1.3.4-1
Подготовка к распаковке …/14-libxvidcore4_2%3a1.3.4-1_i386.deb …
Распаковывается libxvidcore4:i386 (2:1.3.4-1) на замену (3:1.3.3-dmo1) …
dpkg: предупреждение: снижение версии vdpau-va-driver:amd64 с 0.7.4-dmo5 до 0.7.4-6
Подготовка к распаковке …/15-vdpau-va-driver_0.7.4-6_amd64.deb …
Распаковывается vdpau-va-driver:amd64 (0.7.4-6) на замену (0.7.4-dmo5) …
dpkg: предупреждение: снижение версии nikto с 1:2.1.6-0kali1 до 1:2.1.5-1
Подготовка к распаковке …/16-nikto_1%3a2.1.5-1_all.deb …
Распаковывается nikto (1:2.1.5-1) на замену (1:2.1.6-0kali1) …
Настраивается пакет libart-2.0-2:amd64 (2.3.21-2) …
Настраивается пакет libfaac0:i386 (1.28+cvs20151130-1) …
Настраивается пакет libfaac0:amd64 (1.28+cvs20151130-1) …
Настраивается пакет nikto (1:2.1.5-1) …
Обрабатываются триггеры для install-info (6.3.0.dfsg.1-1+b1) …
Обрабатываются триггеры для cracklib-runtime (2.9.2-3) …
Настраивается пакет libxvidcore4:amd64 (2:1.3.4-1) …
Настраивается пакет libxvidcore4:i386 (2:1.3.4-1) …
Настраивается пакет libguess1:amd64 (1.2-1.1) …
Настраивается пакет vdpau-va-driver:amd64 (0.7.4-6) …
Настраивается пакет libquicktime2:amd64 (2:1.2.4-9) …
Настраивается пакет libmjpegutils-2.1-0 (1:2.1.0+debian-5) …
Обрабатываются триггеры для libc-bin (2.24-8) …
Настраивается пакет libbluray1:amd64 (1:0.9.3-3) …
Обрабатываются триггеры для man-db (2.7.6.1-2) …
Настраивается пакет libdca0:amd64 (0.0.5-10) …
Настраивается пакет libmplex2-2.1-0 (1:2.1.0+debian-5) …
Настраивается пакет libmpeg2encpp-2.1-0 (1:2.1.0+debian-5) …
Настраивается пакет liblavfile-2.1-0 (1:2.1.0+debian-5) …
Настраивается пакет liblavjpeg-2.1-0 (1:2.1.0+debian-5) …
Настраивается пакет liblavplay-2.1-0 (1:2.1.0+debian-5) …
Обрабатываются триггеры для libc-bin (2.24-8) …
$
```
И таким образом пакеты из DMO и даже один из Kali (nikto), про который я давно забыл, заменены на пакеты из официального репозитория.

Вот что делаем дальше:
```
$ dpkg -l | grep libswscale
ii  libswscale3:amd64                         10:2.6.9-dmo1                        amd64        FFmpeg video scaling library
ii  libswscale4:amd64                         7:3.2.2-1                            amd64        FFmpeg library for image scaling and various conversions - runtime files
$ 
$ sudo apt-get remove libswscale3:amd64 --purge
Чтение списков пакетов… Готово
Построение дерева зависимостей       
Чтение информации о состоянии… Готово
Пакеты, которые будут УДАЛЕНЫ:
  libswscale3*
обновлено 0, установлено 0 новых пакетов, для удаления отмечено 1 пакетов, и 5 пакетов не обновлено.
После данной операции, объём занятого дискового пространства уменьшится на 547 kB.
Хотите продолжить? [Д/н]
```
У нас уже есть более новые версии библиотек, поэтому держать старые, если, конечно, они не нужны для определенных версий программ, нет никакого смысла.

В идеале должно выглядеть так:
```
$ dpkg -l | grep dmo
ii  x11proto-xf86vidmode-dev   2.3.1-2   all   X11 Video Mode extension wire protocol
```
НО! Если у вас есть приложения, установленные из репозитория deb-multimedia.org, например, avidemux, и вы их используете, будьте внимательнее при удалении.