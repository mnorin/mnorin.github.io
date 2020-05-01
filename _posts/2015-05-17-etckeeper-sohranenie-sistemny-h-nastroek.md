---
id: 2098
title: 'etckeeper: сохранение системных настроек'
date: 2015-05-17T14:37:18+03:00
author: Maxim Norin
layout: post
permalink: /etckeeper-sohranenie-sistemny-h-nastroek.html
categories:
  - Статьи
tags:
  - debian
  - etckeeper
  - linux
  - настройка
---
etckeeper - это программа для создания резервных копий настроек системы с использованием версионного контроля. Любые изменения можно сохранить или откатить до нужной версии. Кроме того, эту программу можно использовать при развертывании копий серверов, чтобы избежать ручной настройки с самого начала, а ограничиться только внесением необходимых изменений. Давайте посмотрим, как она работает и как ей пользоваться.
<!--more-->

## Установка etckeeper

Для установки etckeeper в Debian достаточно дать команду
```
apt-get install etckeeper
```

Зависимости будут установлены автоматически

## Настройка

Настройки etckeeper находятся в директории /etc/etckeeper. Главный файл настроек называется etckeeper.conf

Главной настройкой является VCS - какую систему контроля версий использовать. Доступные варианты:
- hg (mercurial)
- git (по умолчанию)
- bzr (Bazaar)
- darcs

В конфигурационном файле эта настройка выглядит так:
```
VCS=git
```
Затем указываются опции для соответствующих систем версионного контроля:
```
GIT_COMMIT_OPTIONS=""
HG_COMMIT_OPTIONS=""
BZR_COMMIT_OPTIONS=""
DARCS_COMMIT_OPTIONS="-a"
```

По умолчанию коммиты выполняются каждый день. Чтобы этого избежать, нужно раскомментировать строчку
```
#AVOID_DAILY_AUTOCOMMITS=1
```
Для того, чтобы не выводилось предупреждение при обработке специальных файлов, нужно раскомментировать опцию
```
#AVOID_SPECIAL_FILE_WARNING=1
```
Следующая опция отключает коммит изменений до инсталляции, чтобы этого не происходило, нужно раскомментировать ее
```
#AVOID_COMMIT_BEFORE_INSTALL=1
```
Следующие две опции устанавливают высокоуровневый пакетный менеджер (apt, yum, pacman-g2) и низкоуровневый пакетный менеджер (dpkg, rpm, и т.д.):
```
HIGHLEVEL_PACKAGE_MANAGER=apt
LOWLEVEL_PACKAGE_MANAGER=dpkg
```

## Использование etckeeper

Пользоваться etckeeper'ом очень просто. Формат команды выглядит так:
```
etckeeper команда [-d директория]
```
Вот список команд:

| Команда | Описание |
|---|---|
| init | Установить и инициализировать репозиторий (зависит от значения опции VCS в конфигурационном файле) |
| commit [сообщение] | Закоммитить изменения конфигурационных файлов в репозиторий. При этом распознается, если вы используете sudo или su для получения прав root, и записывает оригинальное имя пользователя |
| pre-commit | Эта команда вызывается как пре-коммит хук. Она сохраняет метаданные и делает первичную проверку |
| pre-install | Эта команда вызывается через хук DPkg::Pre-Install-Pkgs менеджера пакетов apt и аналогичные хуки других пакетных менеджеров. Она позволяет закоммитить любые незакоммиченные изменения перед инсталляций, обновлением пакетов и так далее. Это позволяет иметь в репозитории все версии конфигурационных файлов, включая те, которые устанавливались с предыдущей версией пакета. |
| post-install | Эта команда вызывается через хук DPkg::Post-Invoke менеджера пакетов apt и аналогичные хуки других пакетных менеджеров. Она коммитит изменения, сделанные пакетами при установке |
| unclean | Эта команда возвращает значение true, если есть незакомиченные изменения |
| update-ignore [-a] | Обновить файл, в котором перечислены файлы, которые требуется игнорировать. Данные вне блока "managed by etckeeper" не трогаются. Опция -a добавит блок "managed by etckeeper", если его не существует. |
| vcs команда [опции] | Выполнить команду системы контроля версий, которая задана в настройках. Например, "etckeeper vcs diff" |
| uninit [-f] | Уничтожение данных. Удаляется созданный командой init репозиторий. Если указан ключ -f, то данные будут удалены без вопросов. Обычно используется при изменении системы контроля версий, которую вы используете. Сначала вызывается uninit для включенной в данный момент системы контроля версий, а затем init для той, на которую вы переходите. |

Таким образом, при использовании etckeeper, изменяя конфигурационные файлы, вы можете в любое время откатить на то состояние, которое сохранено в репозитории.

Если у вас есть желание поучаствовать в разработке, вы можете найти проект [на GitHub'е](https://github.com/joeyh/etckeeper).