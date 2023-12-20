---
title: remove rc packages
date: 2015-03-07 06:06:02
tags:
  - linux
  - debian
  - linux4all
---

При просмотре списка установленных пакетов `dpkg -l` мы можем увидеть помимо обычного `( ii )`, еще и `( rc )`, что значит:
- r: —пакет отмечен, как удаленный, но —
- c: — остались конфигурационные файлы

```shell
~$ dpkg -l | grep ^rc
rc  awesome;     3.5.2-1  highly configurable X window manager
```

Это может происходить даже если удалять с purge. Удалить их можно очень просто, нужно вывести весь список пакетов, отсечь лишние поля и передать dpkg:

```shell
apt-get purge $(dpkg -l | awk '/^rc/ { print $2 }')
```

Так же можно с помощью aptitude:
`$ aptitude search '~c'`
`$ aptitude purge '~c'`

Вот таким нехитрым способом можно почистить систему от мусора не нужных файлов
