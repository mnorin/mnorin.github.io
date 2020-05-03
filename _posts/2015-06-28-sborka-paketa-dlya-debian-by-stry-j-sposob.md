---
title: Сборка пакета для Debian (быстрый способ)
date: 2015-06-28T14:35:49+03:00
author: Maxim Norin
layout: post
permalink: /sborka-paketa-dlya-debian-by-stry-j-sposob.html
categories:
  - Статьи
tags:
  - bash
  - checkinstall
  - deb
  - debian
  - dpkg
  - gcc
  - linux
  - пакет
---
Сборка пакета для Debian может быть необходима при разработке собственного программного обеспечения, либо при установке программного обеспечения, отсутствующего в репозиториях. В операционных системах на базе ядра Linux из исходных кодов не рекомендуется устанавливать программы при помощи команды "make install". Причины очевидны - если вы удалите Makefile, то уже не сможете удалить всё, что устанавливается при выполнении этой команды. Кроме того, обновлять установленное программное обеспечение таким образом тоже очень неудобно. Рекомендуемый способ установки программ - сборка пакета с последующей установкой этого пакета при помощи пакетного менеджера. Давайте посмотрим, как быстро создать свой собственный deb-пакет с программным обеспечением.
<!--more-->
## Написание программы
Напишем программу, которую будем устанавливать в систему. Пусть это будет простая программа, в общем, это не самая главная часть.

helloworld.c
```c
#include <stdio.h>

void main(){
  printf("Hello, world!\n");
}
```
Теперь можно писать Makefile для компиляции и установки программы

## Написанием Makefile'а
Теперь создадим Makefile для компиляции и установки нашей программы.

Makefile
```makefile
build:
 gcc helloworld.c -o helloworld
 strip helloworld

clean:
 rm helloworld

install: build
 cp helloworld /usr/bin

uninstall:
 rm -f /usr/bin/helloworld
```
Здесь мы описываем 4 цели:
<table>
<tbody>
<tr>
<td>build</td>
<td>Непосредственно компиляция программы из исходного кода и удаление отладочной информации</td>
</tr>
<tr>
<td>clean</td>
<td>Удаление бинарного файла</td>
</tr>
<tr>
<td>install</td>
<td>Установка программы</td>
</tr>
<tr>
<td>uninstall</td>
<td>Деинсталляция программы</td>
</tr>
</tbody>
</table>
Для установки достаточно дать команду "make install", по зависимостям будет вызвана цель build, которая скомпилирует программу и удалит отладочную информацию. Если дать команду "make" без аргументов, будет вызвана цель "build".

## Сборка пакета для Debian
Для сборки пакета воспользуемся программой checkinstall. Сначала ее нужно установить:
```
apt-get install checkinstall
```
После установки можно приступать к сборке пакета. Для сборки нам потребуются некоторые параметры, которые есть у этой программы.
<table>
<tbody>
<tr>
<td width="120">--pkgname</td>
<td>Название пакета</td>
</tr>
<tr>
<td>--pkgversion</td>
<td>Версия пакета</td>
</tr>
<tr>
<td>--pkglicense</td>
<td>Лицензия, под которой распространяется пакет</td>
</tr>
<tr>
<td>--pkggroup</td>
<td>Группа, в которую входит пакет</td>
</tr>
<tr>
<td>--maintainer</td>
<td></td>
</tr>
<tr>
<td>--nodoc</td>
<td>Не включать в пакет документацию. Вы можете включить документацию, если она у вас есть</td>
</tr>
<tr>
<td>--strip</td>
<td>Удалить отладочную информацию для всех бинарных файлов в пакете. Эта опция нужна, если удаление отладочной информации не включается в Makefile</td>
</tr>
</tbody>
</table>
Создадим файл с описанием пакета с названием "description-pak", чтобы каждый раз не вводить руками описание при сборке. Внутри должно быть описание и пустая строка в конце:
```
Simple package that shows how to create debian package fast

```
Теперь создадим небольшой shell-скрипт, в который включим вызов checkinstall со всеми необходимыми параметрами
```bash
#!/bin/bash

sudo checkinstall \
--pkgname helloworld \
--pkgversion 1.0.0 \
--pkglicense GPLv2 \
--pkggroup utils \
--maintainer "Maxim Norin \<mnorin@mnorin.com>" \
--nodoc \
--install \
-y
```
Теперь запускаем этот файл на выполнение. Вы должны увидеть такой результат:
```
$ ./makepkg.sh

checkinstall 1.6.2, Copyright 2009 Felipe Eduardo Sanchez Diaz Duran
         Эта программа распространяется на условиях GNU GPL



*****************************************
**** Debian package creation selected ***
*****************************************

Этот пакет был создан с использованием данных значений: 

0 -  Maintainer: [ Maxim Norin <mnorin@mnorin.com> ]
1 -  Summary: [ Simple package that shows how to create debian package fast ]
2 -  Name:    [ helloworld ]
3 -  Version: [ 1.0.0 ]
4 -  Release: [ 1 ]
5 -  License: [ GPLv2 ]
6 -  Group:   [ utils ]
7 -  Architecture: [ amd64 ]
8 -  Source location: [ make_package ]
9 -  Alternate source location: [  ]
10 - Requires: [  ]
11 - Provides: [ helloworld ]
12 - Conflicts: [  ]
13 - Replaces: [  ]

Введите номер для изменения параметра или нажмите ВВОД для продолжения: 

Installing with make install...

========================= Результаты установки ===========================
gcc helloworld.c -o helloworld
strip helloworld
cp helloworld /usr/bin

======================== Установка успешно завершена ======================

Файлы копируются во временный каталог...OK

Stripping ELF binaries and libraries...OK

Сжимаются страницы руководства...OK

Построение списка файлов...OK

Собирается Debian-пакет...OK

Устанавливается Debian-пакет...OK

Удаляются временные файлы...OK

Записывается пакет с резервной копией...OK
OK

Удаляется временный каталог...OK


**********************************************************************

 Done. The new package has been installed and saved to

 /home/mnorin/Temp/make_package/helloworld_1.0.0-1_amd64.deb

 You can remove it from your system anytime using: 

      dpkg -r helloworld

**********************************************************************
```
В результат пакет собран и он находится в текущей директории. Кроме того, он установлен в системе. Это можно проверить так:
```
$ dpkg -l | grep helloworld
ii  helloworld    1.0.0-1    amd64     Simple package that shows how to create debian package fast
```
И осталось только проверить, работает ли сама программа:
```
$ helloworld
Hello, world!
```
Работает. Теперь пакет из текущей директории можно размещать на сервере, чтобы его могли скачивать другие.
