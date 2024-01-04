---
title: Блокировка экрана с помощью i3lock
date: 2015/01/10 06:54:00
tags:
  - linux4all
  - linux
  - i3
---

Подсмотрел интересный способ блокировки экрана монитора в Linux.

Суть блокировки в том, что рабочий стол остаётся видимым, только жутко пикселизированным

{% asset_img block_screen.jpg %}

Script lock.sh:

```shell
#!/bin/sh -e
# Take a screenshot
scrot /tmp/screen_locked.png
# Pixellate it 10x
mogrify -scale 10% -scale 1000% /tmp/screen_locked.png
# Lock screen displaying this image.
i3lock -i /tmp/screen_locked.png
#вместо процентов можно использовать разрешение экрана
```

На экране получается примерно вот такой результат:

{% asset_img screen_locked.png %}

И вроде бы не видно ничего, а вроде и видно, неплохо, в общем.
Для этого в системе должны быть установлены пакеты `i3lock` и `imagemagick`.

Restored from [original](https://web.archive.org/web/20200206174212/http://conformist-mw.blogspot.com/2015/01/i3lock.html)
