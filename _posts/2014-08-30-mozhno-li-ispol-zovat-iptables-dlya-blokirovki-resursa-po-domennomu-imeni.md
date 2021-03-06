---
title: Можно ли использовать iptables для блокировки ресурса по доменному имени?
date: 2014-08-30T12:42:16+03:00
author: Maxim Norin
layout: post
permalink: /mozhno-li-ispol-zovat-iptables-dlya-blokirovki-resursa-po-domennomu-imeni.html
categories:
  - Вопросы и ответы
tags:
  - iptables
  - squid
  - squidguard
  - баннеры
  - фильтрация трафика
---
__Вопрос:__ Можно ли использовать iptables, чтобы блокировать ресурсы по доменному имени вместо ip-адреса, например, баннерные сети?

<!--more-->

__Ответ:__ Можно, но при этом будет блокироваться тот адрес, который получается при добавлении записи, и он потом будет использоваться при блокировке. Если у ресурса несколько IP-адресов, либо адрес меняется, то такая блокировка работать не будет. Поэтому лучшим вариантом будет использование фильтрующего прокси-сервера, например, [squid в связке со squidguard](/squid-squidguard-sarg-v-debiane.html).
