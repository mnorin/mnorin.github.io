---
title: Создание сети для начинающих. Часть 2.
date: 2015-05-10T20:35:51+03:00
author: Maxim Norin
layout: post
permalink: /sozdanie-seti-dlya-nachinayushhih-chast-2.html
categories:
  - Статьи
tags:
  - APIPA
  - dhcp
  - dns
  - gateway
  - NAT
  - локальная сеть
  - настройка
  - создание сети
  - шлюз
---
В <a href="/sozdanie-seti-dlya-nachinayushhih-chast-1.html">первой части</a> мы уже разобрались с физической организацией сети для дома или малого офиса и ручной настройкой сетевых адресов. В этой части мы продолжим рассматривать создание сети для начинающих. Прежде всего, рассмотрим минимальный набор сервисов, которые необходимы для такой работы сети, при которой сетевые настройки выдаются автоматически. Прежде всего рассмотрим, какие сервисы нам необходимы и зачем они нужны, а потом рассмотрим, что такое сетевой шлюз и что может им являться.
<!--more-->

## Необходимый минимальный набор сервисов для работы сети
Самый минимальный набор сервисов для локальной сети с выходом в Интернет включается обычно следующее:
- DHCP - сервис динамической конфигурации хостов, обеспечивающий автоматическое получение адресов и других сетевых параметров
- DNS - сервис доменных имен, обеспечивающий работу с символьными именами, который позволяет открывать сайты по именам, например
- Сетевой шлюз, обеспечивающий выход в Интернет

В небольших сетях весь этот минимальный набор сервисов обеспечивается обычно либо аппаратным роутером, либо компьютером, выступающим в роли сервера. Настройка роутера обычно проще, но при очень активной работе с сетью ресурсов его может не хватить, если, скажем, у вас локальная сеть из 50 машин, а для выхода в Интернет вы используете роутер домашнего класса. Поэтому при росте сети вам, возможно, в дальнейшем нужно будет переходить на сервер-шлюз, либо на более производительное сетевое оборудование. Кроме этого, в небольшой сети сервер-шлюз может использоваться как файловый сервер и принт-сервер, к которому подключается принтер или принтеры.

## Как работает DHCP
При подключении к сети компьютеры посылают специальный запрос на получение сетевого адреса. Этот запрос широковещательный, то есть, отправляется он всем машинам в сети без указания конкретного адресата, но с указанием источника. В качестве источника указывается аппаратный адрес сетевого интерфейса (под сетевым интерфейсом часто понимают сетевую карту, но на самом деле это не совсем точно, на одной сетевой карте может быть несколько сетевых интерфейсов). DHCP-сервер получает такой запрос и отвечает на него. В ответ может включаться достаточно большое количество параметров, однако минимальный набор обычно включает следующие значения:
- Сетевой адрес (IP-адрес)
- Маска сети (Network mask, netmask
- Адрес сетевого шлюза (Gateway)
- Адрес DNS-сервера

Этих параметров достаточно, чтобы минимально организовать работу сети. Если вы используете устройства для IP-телефонии, например, то вам может потребоваться добавить какие-то опции.

Компьютер получает ответ на свой запрос и настраивает сетевой интерфейс с использованием полученных параметров.

Если DHCP-сервера в сети нет, то они все равно могут быть настроены автоматически. Для этого используется APIPA (Automatic Private IP Adressing)

## Что такое APIPA
APIPA - это сервис операционных систем Windows, позволяющий автоматически сконфигурировать сетевой адрес, если в сети нет DHCP-сервера. В операционных системах на базе ядра Linux подобный сервис тоже есть, это avahi-daemon.

Этот сервис работает следующим образом: компьютер при включении и инициализации сети пытается получить адрес от DHCP-сервера. Если ему это не удается, то он присваивает себе адрес из интервала, который был зарезервирован специально для Microsoft, 169.254.0.1-169.254.255.254. Маска подсети при этом настраивается как 255.255.0.0 (Подсеть класса B). После этого клиент использует самостоятельно сконфигурированный адрес до тех пор, пока в сети не появится DHCP-сервер. Проверка на наличие DHCP-сервера производится один раз в пять минут.

## Что такое маска сети
Маска сети, или маска подсети, - это битовая маска, которая определяет, какая часть IP-адреса относится к сети, а какая часть - к хосту. Сам IP-адрес записывается в десятичном виде, но на самом деле он фактически используется в битовом. Часть битов выделяется под номер подсети и часть битов под адрес хоста. 8 бит в адресе называют октетом.

## Что такое шлюз
Шлюз - это компьютер или сетевое устройство, основная задача которого - пересылка пакетов между подсетями. Дело в том, что внутри одного сегмента сети компьютеры могут обмениваться пакетами без его участия, а для отсылки пакетов за пределы сегмента сети необходим шлюз. Он обычно включен в две или более подсетей и его основная задача - пропускать пакеты между этими двумя подсетями. Пересылка пакетов может обеспечиваться двумя способами: первый - это пропуск пакетов между двумя сетевыми интерфейсами шлюза, так называемый форвардинг, а второй - это NAT (Network Address Translation).

В качестве шлюза может выступать либо компьютер с двумя сетевыми картами, либо аппаратный роутер, либо сетевое оборудование (в настоящее время распространено большое количество сетевого оборудования различных производителей). Для небольшой сети достаточно будет аппаратного роутера домашнего класса, если нет необходимости в широких каналах связи (от 50-100 мбит).

## Что такое NAT
NAT (Network Address Translation) - это механизм, который позволяет преобразовывать адреса транзитных пакетов. Таким образом, при использовании NAT источником пакетов будет шлюз, за которым находится локальная сеть, из которой, собственно, пакеты и посылаются. А когда приходит ответ, шлюз перенаправляет пакеты на тот хост, для которого они предназначены. Трансляция сетевых адресов может производиться как один-в-один, так и много-в-один, и даже отдельные порты других хостов могут транслироваться на порты одной машины или одного устройства.

Таким образом, простейшая схема сети с выходом в Интернет выглядит так:

<img src="https://mnorin.com/wp-content/uploads/2015/05/network-gateway.png" alt="Создание сети для начинающих. Часть 2" width="685" height="370" />

Может быть это и не очень подробное описание всех аспектов работы сети, но надеюсь, что понятное, если что-то все-таки непонятно, пишите вопросы в комментарии. Буду рад, если эти статьи позволят вам разобраться с тем, как организовать несложную локальную сеть с выходом в Интернет.
