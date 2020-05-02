---
title: Создание локального репозитория в RedHat, CentOS и прочих системах с пакетным менеджером yum
date: 2013-02-02T11:16:35+03:00
author: Maxim Norin
layout: post
permalink: /sozdanie-lokal-nogo-repozitoriya-v-redhat-centos.html
categories:
  - Статьи
tags:
  - centos
  - red hat
  - repo
  - yum
  - локальный репозиторий
---

Вот как это сделать:
<!--more-->

1) Монтируем диск с дистрибутивом
```
mkdir /mnt/cdrom
mount -t iso9660 /dev/cdrom /mnt/cdrom
```
2) Копируем все файлы с диска (или дисков) в одну папку. Например, /var/repo/redhat-5.5
```
mkdir /var/repo
cp -r /mnt/cdrom /var/repo/redhat-5.5
```
3) В дистрибутиве есть пакет с названием createrepo. Необходимо его установить.
```
rpm -i createrepo-<версия>.rpm
```
4) Создаем репозиторий.
```
createrepo /var/repo/redhat-5.5
```
5) Создаем файл описания репозитория и открываем его на редактирование.
```
touch /etc/yum.repos.d/localrepo.repo
nano /etc/yum.repos.d/localrepo.repo
```
6) Вписываем в него следующее:
```
[localrepo]
name=LocalRepository
baseurl=file:///var/repo/redhat-5.5
gpgcheck=0
enabled=1
```
7) Пользуемся
```
yum install mc
```
