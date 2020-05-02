---
title: Проверка сертификата сервера из bash
date: 2017-01-14T05:04:26+03:00
author: Maxim Norin
layout: post
permalink: /proverka-sertifikata-servera-iz-bash.html
categories:
  - Статьи
tags:
  - bash
  - linux
  - openssl
  - s_client
  - мониторинг
  - проверка
  - скрипт
---
Проверка сертификата сервера из bash может быть полезна для мониторинга, чтобы не пропустить дату продления сертификата. Такие проблемы как продление доменного имени и продление SSL-сертификата достаточно распространены, даже серьезные организации иногда забывают проплатить домен или купить новый сертификат. Поэтому хорошей практикой является мониторинг даты окончания действия сертификата и доменного имени. С доменным именем разберемся в другой раз, а пока давайте посмотрим, как написать скрипт, который не только покажет поля сертификата на удаленном сервере, но и скажет, сколько дней осталось до окончания сертификата.<!--more-->

## Что нам потребуется
Для проверки будем использовать, естественно, Linux. Именно на нем чаще всего стоят системы мониторинга, такие как Zabbix и Nagios. Кроме сервера с Linux'ом нам еще кое-что потребуется. Прежде всего OpenSSL (чем свежее, тем лучше), и, конечно же, bash, на котором мы и будем писать скрипт. Этот скрипт будет полезен как при ручной проверке, так и при автоматизированных проверках.

## Проверка сертификата сервера из bash
Вот как будет организована проверка. Мы открываем SSL-соединение с сервером, получаем сертификат, передаем его на обработку OpenSSL (openssl x509), получая таким образом информацию по полям, а если нам нужно посчитать, сколько времени осталось до истечения срока действия сертификата, будет использовать отдельный параметр, который нам просто выведет количество дней в формате "N days".

Вот сам скрипт с комментариями:
```bash
#!/bin/bash

SERVERNAME=$1
PORT=$2
PARAMS=""

# Функция, которая выводит помощь по параметрам
print_help(){
cat << EOF
  Script that fetches SSL certificate information from remote server and prints out certificate fields.
  Can be used for certificate expiration date monitoring.

  Usage: certinfo.sh hostname.or.ip.address port [parameters]

  Parameters:

  --issuer      - Certificate issuer
  --subject     - Subject
  --serial      - Serial
  --email       - Email
  --start-date  - Certificate start date
  --end-date    - Certificate end date
  --expires-in  - Expiration in days
  --purpose     - Certificate purposes
  --dates       - Both start and end dates
  --fingerprint - Certificate fingerprint
  --alias       - Certificate aliases
  --modulus     - Modulus
  --pubkey      - Public key
  --all         - Full certificate information (the same as without any parameters)
  --help        - This help
EOF
exit 0
}

# Функция, которая показывает, сколько дней осталось до конца действия сертификата
expires_in(){
    EXP_DATE=$( 
    echo "" | openssl s_client -connect ${SERVERNAME}:${PORT} 2>/dev/null \
    | sed -n '/-----BEGIN CERTIFICATE-----/,/-----END CERTIFICATE-----/p' \
    | openssl x509 -noout -in - -enddate \
    | sed 's/notAfter=//' 
)
    DAYS_LEFT=$(( ($(date -d "$EXP_DATE" +%s) - $(date -d "now" +%s) ) / 86400 ))
    echo $DAYS_LEFT days
    exit
}

# Сдвигаем параметры, пропуская сервер и порт
shift 2
# Анализируем параметры скрипта в цикле
# Это позволяет указывать их в произвольном порядке
while [ "$1" != "" ]
do
    case "$1" in
        --issuer) PARAMS+="-issuer " ;;
        --subject) PARAMS+="-subject " ;;
        --serial) PARAMS+="-serial " ;;
        --email) PARAMS+="-email " ;;
        --start-date) PARAMS+="-startdate " ;;
        --end-date) PARAMS+="-enddate " ;;
        --purpose) PARAMS+="-purpose " ;;
        --dates) PARAMS+="-dates " ;;
        --fingerprint) PARAMS+="-fingerprint " ;;
        --alias) PARAMS+="-alias " ;;
        --modulus) PARAMS+="-modulus " ;;
        --pubkey) PARAMS+="-pubkey " ;;
        --all) PARAMS+="-text " ;;
        --help) print_help ;;
        --expires-in) expires_in ;;
        *) ;;
    esac
    shift
done

# Если параметры не указаны, то просто выводим полную информацию о сертификате
PARAMS=${PARAMS:-"-text "}

# Проверка сертификата сервера из bash
# Получаем сертификат удаленного сервера из вывода SSL клиента
# Выделяем само тело сертификата и передаем на обработку openssl x509
# Подставляем параметры, чтобы вывелась информация по нужным полям
echo "" | openssl s_client -connect ${SERVERNAME}:${PORT} 2>/dev/null \
| sed -n '/-----BEGIN CERTIFICATE-----/,/-----END CERTIFICATE-----/p' \
| openssl x509 -noout -in - $PARAMS
```

## Что можно улучшить
Можно дописать свои функции, которые будут выводить информация в удобном для дальнейшего использования формате, если вы планируете куда-то передавать эти данные для статистики или обработки. Плюс можно добавить проверку параметров (выводить текст о том, что недостаточно параметров), но в данном случае это не так важно, поскольку назначение этого скрипта - мониторинг, что значит, что, скорее всего, настраиваться он будет один раз.