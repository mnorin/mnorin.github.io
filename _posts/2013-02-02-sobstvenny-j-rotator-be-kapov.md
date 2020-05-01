---
id: 88
title: Собственный ротатор бэкапов
date: 2013-02-02T00:46:24+03:00
author: Maxim Norin
layout: post
permalink: /sobstvenny-j-rotator-be-kapov.html
categories:
  - Статьи
tags:
  - backup
  - bash
  - linux
  - ротация бэкапов
---
Часто ставится задача создания резервных копий (бэкапов) с сохранением последних, например, пяти копий.
Резервную копию директории можно создать при помощи архиватора. Чаще всего в *nix-системах используют сочетание tar+bzip2.
С ротацией дело обстоит несколько сложнее, но не так сложно, как может показаться.
<!--more-->
Вот пример (файл etc_backup.sh):
```bash
#!/bin/bash

# Переменная для сохранения строки с датой
BACKUP_DATE=$(date +%Y-%m-%d__%H:%M:%S)

# Префикс, или, говоря иначе, начало имени файла
BACKUP_PREFIX="etc"

# Количество копий, которое нужно сохранять
MAX_COPIES_COUNTER=5

# Директория, которая будет архивироваться
DIRECTORY_TO_BACKUP="/etc"

# Директория, в которой будут храниться бэкапы
DIRECTORY_FOR_BACKUPS="/backup/data"

# Собственно архивация
# При архивации сообщения об ошибках будут погашены
tar -cjf $DIRECTORY_FOR_BACKUPS/$BACKUP_PREFIX__$BACKUP_DATE.tar.bz2 $DIRECTORY_TO_BACKUP 2>/dev/null

# Подсчитываем количество копий, которое сохранено в папке для хранения резервных копий
COPIES_COUNTER=$(ls /backup/data | grep $BACKUP_PREFIX | wc -l)

# Если файлов сохранено больше, чем нам необходимо
if [ $COPIES_COUNTER -gt $MAX_COPIES_COUNTER ]
then
  # Вычисляем, сколько файлов нужно удалить из папки
  NEED_TO_DELETE_COUNTER=$(($(ls /backup/data | wc -l)-$MAX_COPIES_COUNTER))
  # Организуем цикл, количество циклов равно количеству файлов, которое нужно удалить
  for (( i==1; i<$NEED_TO_DELETE_COUNTER; i++ ))
    do
      # Сортируем список файлов, берем из него первый и удаляем
      # Файлы будут отсортированы по возрастанию, соответственно
      # удалится самый ранний файл
      rm $DIRECTORY_FOR_BACKUPS/$(ls $DIRECTORY_FOR_BACKUPS | sort | head -n 1)
done
fi
```
