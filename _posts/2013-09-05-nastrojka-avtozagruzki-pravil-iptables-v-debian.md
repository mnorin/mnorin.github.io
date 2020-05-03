---
title: Настройка автозагрузки правил iptables в Debian
date: 2013-09-05T23:12:55+03:00
author: Maxim Norin
layout: post
permalink: /nastrojka-avtozagruzki-pravil-iptables-v-debian.html
categories:
  - Статьи
tags:
  - bash
  - debian
  - init.d
  - iptables
---
Чем отличается настройка iptables в Debian от других операционных систем? Тем, что достаточно давно сложилось так, что способ загрузки правил iptables оставался на усмотрение администратора, который администрирует сервер. И хотя позже появились такие решения, как ufw, пришедший из Ubuntu, или iptables-persistent, все равно может потребоваться реализовать автоматическую загрузку таблиц самостоятельно. И есть несколько способов это сделать...
<!--more-->

Прежде, чем рассматривать способы загрузки и сохранения таблиц, давайте разберемся, что мы можем использовать. Это три команды:
```
iptables
iptables-save
iptables-restore
```
Команда iptables - это основная команда для изменения текущих таблиц, по ней можно найти много информации на просторах Интернета, поэтому пока не будем подробно рассматривать все ее возможности.

Команда iptables-save выдает на стандартный вывод действующие в данный момент правила iptables, поэтому для сохранения их в файл надо использовать перенаправление потоков:
```
iptables-save > /etc/iptables/iptables.today
```
Команда iptables-restore делает, как вы уже догадались, обратное. То есть восстанавливает правила iptables. Работает она тоже со стандартными потоками, поэтому загрузка правил будет выглядеть так:
```
iptables-restore < /etc/iptables/iptables.today
```
А теперь давайте более подробно посмотрим, как же, собственно, организовать загрузку правил автоматически.

__Способ 1.__ Простой способ.

Самый простой способ загрузки таблиц - это вызывать загрузку после поднятия основного сетевого интерфейса при помощи параметра post-up, размещенного в файле /etc/network/interfaces

Что для этого потребуется? В первую очередь нам потребуется директория, в которой будут храниться файлы с таблицами. В соответствии со стандартом FHS, это будет /etc/iptables/, в ней мы будем хранить файлы с правилами, полученными при помощи iptables-save. Перед настройкой правил сначала сбросим содержимое правил по умолчанию:
```
iptables -F INPUT
iptables -F OUTPUT
iptables -F FORWARD
```
Теперь посмотрим, что у нас получилось:
```
iptables-save
```
У вас должны быть чистые списки правил. Это состояние желательно сохранить на случай необходимости сброса правил.
```
iptables-save > /etc/iptables/iptables.clean
```
Детальную настройку правил мы рассматривать не будем, мы сейчас не об этом. После настройки необходимых правил сохраняем их снова в файл. Я предпочитаю включать в название файла дату и время сохранения правил. Во-первых, так понятнее, что когда сохранялось, во-вторых, сортировка при просмотре в файловых менеджерах о именам выстроит список файлов по времени их создания.
```
iptables-save > /etc/iptables/iptables-`date +%Y%m%d-%H%M%S`
```
Имя получается длинное, но зато при использовании такого шаблона точно можно определить, в какое время этот файл был получен.

Теперь делаем еще одно действие, которое нам позволит не переписывать название файла с правилами для загрузки. Создаем символическую ссылку на файл, в который мы только что сохранили правила. Пусть это будет файл iptables-20130904-223007, тогда делаем так:
```
ln -s /etc/iptables/iptables-20130904-223007 /etc/iptables/iptables.default
```
И последнее, что надо сделать - изменить файл /etc/network/interfaces, дописав одну строчку. Должно получиться примерно следующее:
```
auto eth0
iface eth0 inet dhcp
post-up /sbin/iptables-restore < /etc/iptables/iptables.default
```
И после этого сохраняем.

В общем, вот и всё. Теперь после поднятия интерфейса правила iptables будут загружены автоматически. Какие у этого способа есть минусы? Если кто-нибудь изменит файл /etc/network/interfaces, например, какая-нибудь программа-менеджер сети, то строчка с загрузкой правил может потеряться. Не факт, конечно, но вероятность есть. Второе - если у вас несколько сетевых интерфейсов, то вам надо либо вешать на событие post-up каждого интерфейса, либо точно знать, что выбранный вами интерфейс точно поднимается. Вариация этого способа - размещение скрипта загрузки правил в директории /etc/network/{if-pre-up.d|if-up.d}. В таком случае По соответствующему событию правила будут загружаться автоматически.

__Способ 2.__ Совсем простой способ.

Еще более простой способ, которым многие пользуются - это написать шелл-скрипт загрузки правил и вызывать его из, например, /etc/rc.local или еще из какого-нибудь места при загрузке системы. Минус тут очевиден - для сохранения текущего состояния надо будет править этот самый скрипт, что не есть хорошо и удобно. Предыдущий способ в этом смысле выигрывает.

__Способ 3.__ Самый сложный, но и самый интересный с моей точки зрения.

Третий способ состоит в написании собственного скрипта, делающего вид, что он сервис, и этот сервис будет запускаться при старте системы. Кроме того, мы сможем очищать текущие правила и изменять файл с правилами, который будет загружаться по умолчанию. И для этого всего мы будем использовать только один скрипт. Ну и, конечно же, этот скрипт должен самостоятельно проверять, есть ли у нас необходимые файл и директории и создавать их, если их нет. Но всё по порядку.

Начнем с того, что создадим наш скрипт и поместим его в директорию /etc/init.d, где у нас хранятся скрипты для других сервисов.
```bash
# touch /etc/init.d/ip-firewall
# chmod +x /etc/init.d/ip-firewall
```
Далее в любом удобном вам редакторе редактируем этот файл, вписываем в него следующее:
```bash
#!/bin/bash

### BEGIN INIT INFO
# Provides:          ip-firewall
# Required-Start:    $networking
# Required-Stop:     $networking
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: iptables frontend script
# Description:       iptables-based firewall
### END INIT INFO

test -f /lib/lsb/init-functions || exit 1
. /lib/lsb/init-functions

DESC="Iptables based firewall"
NAME=ip-firewall
SCRIPTNAME=/etc/init.d/ip-firewall

do_start()
{

}

do_stop()
{

}

do_list()
{

}

do_makedefault()
{

}

do_savecurrent()
{

}

case "$1" in
    start)
        do_start
    ;;
    stop)
        do_stop
    ;;
    list)
        do_list
    ;;
    makedefault)
        do_makedefault
    ;;
    savecurrent)
        do_savecurrent
    ;;
    *)
        echo Usage: "ip-firewall start|stop|list|makedefault|savecurrent"
    ;;
esac
```
Это наш шаблон скрипта сервиса, который мы с вами сейчас будем доводить до ума.

У нас уже есть обработка параметров и тела всех функций, осталось только написать внутренности этих функций. Начнем со старта, напишем содержимое функции do_start:
```bash
do_start()
{
  echo Starting ip-firewall...
  if [ -e /etc/iptables/iptables.default ]
  then
    /sbin/iptables-restore < /etc/iptables/iptables.default
    echo Done.
  else
    echo Unable to load rules. Make sure file /etc/iptables/iptables.default exists or make default some rules set.
}
```
Единственная задача этой функции, по идее,- загрузка правил фаервола, но надо также проверить, а существует ли файл, который мы хотим загрузить, и если его не существует, то выдать соответствующее предупреждение. Следующая функция - это отключение фаервола. Смысл ее будет в загрузке пустых правил. Тут мы даже не будем загружать никакого файла, а просто сбросим содержимое цепочек правил и включим разрешительные политики.
```bash
do_stop()
{
  echo Turning off ip-firewall...
  /sbin/iptables -F INPUT
  /sbin/iptables -F OUTPUT
  /sbin/iptables -F FORWARD
  /sbin/iptables -P INPUT ACCEPT
  /sbin/iptables -P OUTPUT ACCEPT
  /sbin/iptables -P FORWARD ACCEPT
  echo Done.
}
```
После этих действий цепочки iptables будут, но никаких запретов не будет. Следующая функция, которую мы напишем - do_list(). Ее задача - вывести список файлов с правилами. И можно сразу указать, какой из них считается файлом по умолчанию.
```bash
do_list()
{
  echo Rules sets:
  ls /etc/iptables | grep iptables | grep -v default
  echo Default rules set is:
  ls -l /etc/iptables | grep default | awk '{print $11}'
}
```
Теперь мы можем просмотреть список файлов с правилами и сказать, какой из этих файлов является файлом по умолчанию. Это нам необходимо для того, чтобы в дальнейшем можно было указывать, какой файл с правилами должен быть по умолчанию. Следующая функция - do_makedefault(). Ее задача - сделать некоторый файл с правилами файлом по умолчанию, чтобы он загружался автоматически при старте нашего скрипта. Поскольку файл, который у нас загружается, всегда будет иметь одно и то же название, он всегда будет просто символической ссылкой, а указывать эта ссылка будет на разные файлы. В этой функции у нас будет использоваться еще один параметр скрипта, поэтому добавим значение $2 в вызов функции в структуре case. Должно получиться так:
```bash
makedefault)
  do_makedefault $2
;;
```
Итак
```bash
do_makedefault(){
  echo Setting rules set \"$1\" as default...
  rm -f /etc/iptables/iptables.default
  ln -s /etc/iptables/$1 /etc/iptables/iptables.default
  echo Done.
}
```
В самой функции этот параметр превратится в $1, потому что для скрипта он второй, а для функции - первый. Как вы видите ,мы просто удаляем символическую ссылку с названием iptables.default и делаем новую на тот файл, название которого указано. Указывать надо только имя файла, путь указывать не нужно. А флаг -f мы используем для подавления сообщения об ошибке, если такого файла не существует.

И осталась последняя функция - do_savecurrent(). Она будет тоже выполнять всего одно несложное действие - сохранять текущие правила iptables в файл с включенным в название временем сохранения этих самых правил.
```bash
do_savecurrent(){
  NEWFILENAME="/etc/iptables/iptables-`date +%Y%m%d-%H%M%S`"
  echo Saving current rules set...
  /sbin/iptables-save > $NEWFILENAME
  echo Done.
}
```
Вот, собственно, сам скрипт почти закончен. Осталось сделать еще одну малость - автоматическое создание директорий, в которых будут храниться правила, если их не существует, и, если файл /etc/iptables/iptables.default не существует, то сбросим правила и политики и создадим файл iptables.clean, после чего создадим символическую ссылку на него с названием iptables.default. Для этого напишем отдельную функцию do_init():
```bash
do_init(){
  echo Initiating...
  if [ ! -e /etc/iptables ]
    then
      echo Directory /etc/iptables not found. Creating...
      mkdir /etc/iptables
      echo Done.
  fi
  if [ ! -e /etc/iptables/iptables.default ]
    then
      echo Default rules set not found. Creating...
      /sbin/iptables -F INPUT
      /sbin/iptables -F OUTPUT
      /sbin/iptables -F FORWARD
      /sbin/iptables -P INPUT ACCEPT
      /sbin/iptables -P OUTPUT ACCEPT
      /sbin/iptables -P FORWARD ACCEPT
      /sbin/iptables-save > /etc/iptables/iptables.clean
      ln -s /etc/iptables/iptables.clean /etc/iptables/iptables.default
      echo Done.
  fi
}
```
Эту функцию будем вызывать в самом начале скрипта, еще до выбора выполняемой команды. В итоге у нас получится следующий скрипт:
```bash
#!/bin/bash

### BEGIN INIT INFO
# Provides:          ip-firewall
# Required-Start:    $networking
# Required-Stop:     $networking
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: iptables frontend script
# Description:       iptables-based firewall
### END INIT INFO

test -f /lib/lsb/init-functions || exit 1
. /lib/lsb/init-functions

DESC="Iptables based firewall"
NAME=ip-firewall
SCRIPTNAME=/etc/init.d/ip-firewall

do_start(){
  echo Starting ip-firewall...
  if [ -e /etc/iptables/iptables.default ]
  then
    /sbin/iptables-restore < /etc/iptables/iptables.default
    echo Done.
  else
    echo Unable to load rules. Make sure file /etc/iptables/iptables.default exists or make default some rules set.
  fi
}

do_stop()
{
  echo Turning off ip-firewall...
  /sbin/iptables -F INPUT
  /sbin/iptables -F OUTPUT
  /sbin/iptables -F FORWARD
  /sbin/iptables -P INPUT ACCEPT
  /sbin/iptables -P OUTPUT ACCEPT
  /sbin/iptables -P FORWARD ACCEPT
  echo Done.
}

do_list(){
  echo Rules sets:
  ls /etc/iptables | grep iptables | grep -v default
  echo
  echo Default rules set is:
  ls -l /etc/iptables | grep default | awk '{print $11}'
}

do_makedefault(){
  echo Setting rules set \"$1\" as default...
  rm -f /etc/iptables/iptables.default
  ln -s /etc/iptables/$1 /etc/iptables/iptables.default
  echo Done.
}

do_savecurrent()
{
  NEWFILENAME="/etc/iptables/iptables-`date +%Y%m%d-%H%M%S`"
  echo Saving current rules set...
  /sbin/iptables-save > $NEWFILENAME
  echo Done.
}

do_init(){
  echo Initiating...
  if [ ! -e /etc/iptables ]
    then
      echo Directory /etc/iptables not found. Creating...
      mkdir /etc/iptables
      echo Done.
  fi
  if [ ! -e /etc/iptables/iptables.default ]
    then
      echo Default rules set not found. Creating...
      /sbin/iptables -F INPUT
      /sbin/iptables -F OUTPUT
      /sbin/iptables -F FORWARD
      /sbin/iptables -P INPUT ACCEPT
      /sbin/iptables -P OUTPUT ACCEPT
      /sbin/iptables -P FORWARD ACCEPT
      /sbin/iptables-save > /etc/iptables/iptables.clean
      ln -s /etc/iptables/iptables.clean /etc/iptables/iptables.default
      echo Done.
  fi
}

do_init

case "$1" in
    start)
        do_start
    ;;
    stop)
        do_stop
    ;;
    list)
        do_list
    ;;
    makedefault)
        do_makedefault $2
    ;;
    savecurrent)
        do_savecurrent
    ;;
    *)
        echo Usage: "ip-firewall start|stop|list|makedefault|savecurrent"
    ;;
esac
```
Осталось теперь включить получившийся скрипт в автозапуск:
```
update-rc.d ip-firewall defaults
```
Всё. Теперь правила будут загружаться каждый раз после загрузки, после выполнения скрипта networking. Если сеть стартовать при использовании LSBInit не будет, то правила фаервола загружаться тоже не будут. Надеюсь, этот скрипт будет вам полезен. Любые замечания в комментариях приветствуются.
