---
title: 'Toshiba AC100: Установка яркости матрицы/экрана ноутбука в linux'
date: 2013-02-03T22:51:24+03:00
author: Maxim Norin
layout: post
permalink: /toshiba-ac100-ustanovka-yarkosti-matritsy-e-krana-n.html
categories:
  - Статьи
tags:
  - bash
  - brightness
  - class
  - GPL
  - linux
  - SYS
  - toshiba ac100
  - ubuntu
  - ноутбук
  - яркость
---
Установив на Toshiba AC100 Ubuntu и свой любимый IceWM я обнаружил, что яркость матрицы всегда одна и та же, причем максимальная. А это не только потребляет больше энергии и сокращает таким образом время работы от батареи, но и напрягает глаза при длительной работе.
В результате был написан небольшой скрипт, который позволяет изменять яркость монитора из командной строки.
<!--more-->
brightness:
```bash
#!/bin/bash

#
# Notebook/Laptop/Netbook/other devices with LCD screens probably
# LCD brightness script
# License: GNU GPLv2
# Checked on kernel 3.0
#

DEVICES_BASE="/sys/class/backlight"
DEVICES_LIST=$(ls /sys/class/backlight/)

for DEVICE in $DEVICES_LIST
do
  MAX_BRIGHTNESS=$(cat $DEVICES_BASE/$DEVICE/max_brightness)
  case $1 in
  restore)
    sudo chmod a+w $DEVICES_BASE/$DEVICE/brightness
    cat /etc/brightness.conf > $DEVICES_BASE/$DEVICE/brightness
    echo
    echo Brightness is restored to $(cat $DEVICES_BASE/$DEVICE/brightness)
    echo
  ;;
  show)
    echo
    echo Device: $DEVICE
    echo Maximum brightness: $MAX_BRIGHTNESS
    echo Brightness is set to $(cat $DEVICES_BASE/$DEVICE/brightness)
    echo
  ;;
  *)
    CORRECT_VALUE="0"
    for (( j=1; j<$MAX_BRIGHTNESS; j++ ))
      do
        if [ "$1" == "$j" ];
          then
            CORRECT_VALUE="1"
        fi
      done
      if [ "$CORRECT_VALUE" == "1"  ];
        then
          sudo chmod a+w $DEVICES_BASE/$DEVICE/brightness
          echo $1 > $DEVICES_BASE/$DEVICE/brightness
          echo $1 > /etc/brightness.conf
          echo
          echo Brightness is set to $1
          echo
        else
          echo
          echo Usage: brightness \[INTEGER\|show\|restore\]
          echo INTEGER is integer value acceptable as device brightness. It must be between 1 and maximum for your device
          echo To get maximum values use \"brightness show\"
          echo
      fi
  ;;
  esac
done
```
