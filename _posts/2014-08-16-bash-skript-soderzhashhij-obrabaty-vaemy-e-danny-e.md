---
title: Bash-скрипт, содержащий обрабатываемые данные
date: 2014-08-16T19:17:19+03:00
author: Maxim Norin
layout: post
permalink: /bash-skript-soderzhashhij-obrabaty-vaemy-e-danny-e.html
categories:
  - Статьи
tags:
  - bash
  - данные
  - самомодификация
---
Давайте на примере простого скрипта рассмотрим, как написать скрипт, хранящий данные внутри самого себя. Это будет список задач, хранимый в том же самом скрипте, который добавляет задачи, удаляет их и выводит список задач в его текущем состоянии. Итоговый скрипт будет за вычетом самого списка задач занимать менее 750 байт. Его удобно использовать, например, с Dropbox'ом, Яндекс.Диском или Copy для синхронизации задач между несколькими компьютерами

<!--more-->

Самое первое, что нужно сделать - это определить команды, используемые в скрипте и написать обработку параметров командной строки.
```bash
case $1 in
    add|del|show)
        ACTION=$1
        shift
        TASK=$@
        ${ACTION}_task
    ;;
    *)
        show_help
    ;;
esac
```
У нас есть три команды: add, del, show. Самое первое, что сделаем - запомним действие, которое указано в командной строке, в переменную ACTION. Затем сдвинем позиционные параметры скрипта, чтобы удалить из этого списка само действие. Переменной TASK приравняем содержимое командной строки, которое в итоге получится. После этого запустим функцию с именем ДЕЙСТВИЕ_task, которая выполняет соответствующее действие. Если же в качестве первого параметра указано что-то иное или ничего не указано, будет вызвана функция show_help, отображающая краткую помощь. Вот она:
```bash
show_help(){
  cat << EOF

Description: script which keeps tasks list (or any other list) inside itself
Usage: $0 [add|show|del]'

Commands:
  add [task description]
  del [task description part (will delete any tasks that include entered string)]
  show

EOF
exit
}
```
Теперь создадим функции, необходимые для выполнения указанных действий. Начнем с простого, с вывода списка задач.
```bash
show_task(){
echo "----------TASK LIST--------"
cat << TASKLISTEND
TASKLISTEND
}
```
Эта функция просто выводит всё, что находится между строкой
```bash
cat << TASKLISTEND
```
и строкой
```bash
TASKLISTEND
```
Именно между этими строками мы и будем хранить список задач. Теперь осталось написать еще две функции: добавление и удаление задачи. Добавлять задачу будем следующим образом:
```bash
get_task(){
if [ "$TASK" == "" ]
then
    echo -n "Enter task description: "
    read TASK
fi
}

add_task(){
get_task
sed -i $0 -re "s/(^TASKLISTEND$)/$TASK\n\1/"
$0 show
}
```
Непосредственно добавлением задачи занимается функция add_task, а функция get_task просто запрашивает с клавиатуры задачу, если она не была указана в командной строке. Добавление осуществляется потоковым редактором sed. Он выполняет следующее действие: находит в файле $0 (сам файл скрипта) строку, в которой содержится слово "TASKLISTEND" и вставляет перед ним еще одну строку, содержащую описание задачи, и перевод строки.

Удаление реализуется похожим образом:
```bash
del_task(){
get_task
sed -i $0 -e "/ TASKLISTEND$/,/^TASKLISTEND$/ {/$TASK/d}"
$0 show
}
```
Как видите, здесь мы тоже получаем описание задачи при помощи функции get_task, а затем выполняем удаление строки из списка, в которой находится часть описания задачи, попадающей в интервал между двумя строками. Строка начала интервала строк содержит пробел и слово TASKLISTEND в конце строки, а строка конца интервала содержит одно единственное слово TASKLISTEND. Между этими двумя строками мы и удаляем найденную строку, если такая есть.

Вот, собственно, и всё. Полный текст скрипта tsklst.sh:
```bash
#!/bin/bash

get_task(){
if [ "$TASK" == "" ]
then
    echo -n "Enter task description: "
    read TASK
fi
}

add_task(){
get_task
sed -i $0 -re "s/(^TASKLISTEND$)/$TASK\n\1/"
$0 show
}

del_task(){
get_task
sed -i $0 -e "/ TASKLISTEND$/,/^TASKLISTEND$/ {/$TASK/d}"
$0 show
}

show_task(){
echo "----------TASK LIST--------"
cat << TASKLISTEND
TASKLISTEND
}

show_help(){
  cat << EOF

Description: script which keeps tasks list (or any other list) inside itself
Usage: $0 [add|show|del]'

Commands:
  add [task description]
  del [task description part (will delete any tasks that include entered string)]
  show

EOF
exit
}

case $1 in
    add|del|show)
        ACTION=$1
        shift
        TASK=$@
        ${ACTION}_task
    ;;
    *)
        show_help
    ;;
esac
```
