---
title: Запуск Victoria с жёсткого диска с помощью grub2
date: 2016-02-09 13:00:10
tags:
  - linux
  - linux4all
  - grub2
  - victoria
---

Короткая заметка о том, как добавить `Victoria` в меню загрузки grub для проверки своего жёсткого диска без мультизагрузочной флешки.

Добавляем в ls `/etc/grub.d/40_custom` следующее:

```bash
#!/bin/sh
exec tail -n +3 $0
# This file provides an easy way to add custom menu entries.  Simply type the
# menu entries you want to add after this comment.  Be careful not to change
# the 'exec tail' line above.
menuentry "victoria" {
  linux16 /boot/memdisk iso raw
  initrd16 /boot/vcr35r.iso
}
```

Устанавливаем syslinux, скачиваем iso образ Victoria и закидываем в нужный каталог (boot):

```shell
cp /usr/lib/syslinux/memdisk /boot
cp $HOME/Downloads/vcr35r.iso /boot
```

Далее делаем `update-grub` и всё готово, можно перезагружаться и запускать victoria и жёсткого диска для проверки.

Restored from [original](https://web.archive.org/web/20200206162837/http://conformist-mw.blogspot.com/2016/02/victoria-grub2.html)
