---
title: Systemd долго ждёт сеть при загрузке Debian 8
date: 2015-11-21T23:10:34+03:00
author: Maxim Norin
layout: post
permalink: /systemd-dolgo-zhdyot-set-pri-zagruzke-debian-8.html
categories:
  - Вопросы и ответы
tags:
  - "8"
  - debian
  - jessie
  - linux
  - systemd
  - сеть
---
__Вопрос:__

В процессе загрузки Debian 8 systemd долго ждет сеть (до 1,5 минут), хотя настройки заданы верно, даже если DHCP не используется. Как это исправить?<!--more-->

__Ответ:__

В файле /etc/network/interfaces для всех сетевых интерфейсов нужно указать allow-hotplug. То есть, изменить этот файл, который выглядит так:
```
auto eth0
    iface eth0 inet dhcp

auto lo
    iface lo inet loopback

auto wlan0
    iface wlan0 inet dhcp
        wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf

auto lxcnat0
    iface lxcnat0 inet static
        bridge_ports none
        bridge_fd 0
        bridge_maxwait 0
        address 10.0.3.1
        netmask 255.255.255.0
        up iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE
```
Чтобы он стал выглядеть так:
```
allow-hotplug eth0
    iface eth0 inet dhcp

allow-hotplug lo
    iface lo inet loopback

allow-hotplug wlan0
    iface wlan0 inet dhcp
        wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf

allow-hotplug lxcnat0
    iface lxcnat0 inet static
        bridge_ports none
        bridge_fd 0
        bridge_maxwait 0
        address 10.0.3.1
        netmask 255.255.255.0
        up iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE
```

И после этого загрузка станет быстрее.