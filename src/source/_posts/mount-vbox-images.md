---
title: Монтирование образов дисков VirtualBox (*.vdi, *vhd)
date: 2016-10-05 13:06:18
tags:
  - linux
  - linux4all
  - debian
  - virtualbox
  - qemu
---

Для того, чтобы смонтировать образ vdi (фиксированный или динамический, не важно) нужны модуль ядра nbd и qemu-nbd. Первый обычно есть всегда и его достаточно загрузить (желательно с опицией max_part=16 или больше), а вот qemu возможно придётся поставить.

```shell
modprobe nbd max_part=16
apt install qemu
```

Теперь нужно смонтировать наш образ в новое устройство /dev/nbd. Тут есть 2 варианта:
1) мы точно знаем какие разделы на vdi, нам нужен какой-то один и мы монтируем именно его. Тогда поступаем просто:

```shell
# 1 — first partition
qemu-nbd -P 1 -c /dev/nbd0 storage.vdi
```

2) мы хотим смонтировать всё, что есть. Тогда нам нужно смонтировать весь образ и определить все разделы на нём. Для этого делаем следующее:

```shell
qemu-nbd -c /dev/nbd0 storage.vdi
kpartx -a /dev/nbd0
```

После этих действий все разделы появятся в /dev/mapper/. Оттуда их можно монтировать обычным способом. После завершения работы нужно всё размонтировать и отключить модуль ядра в обратном порядке:

```shell
# umount all partitions
kpartx -d /dev/nbd0
qemu-nbd -d /dev/nbd0
rmmod nbd
```

На этом всё.

Restored from [original](https://web.archive.org/web/20200206164739/http://conformist-mw.blogspot.com/2016/10/virtualbox-vdi-vhd.html)
