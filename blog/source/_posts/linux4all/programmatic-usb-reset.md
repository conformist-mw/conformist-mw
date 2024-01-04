---
title: Сброс usb устройства без физического вмешательства (usbreset)
date: 2015-10-17 19:40:51
tags:
  - linux
  - linux4all
  - usb
---

Иногда бывает необходимость сбросить usb устройство не вытаскивая его из порта (например, удалённо). Для этого может понадобиться такая штука, как usbreset. Берём код ниже и сохраняем его как `usbreset.c`

```C
/* usbreset -- send a USB port reset to a USB device
 *
 * Compile using: gcc -o usbreset usbreset.c
 *
 * */

#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>
#include <errno.h>
#include <sys ioctl.h="">

#include <linux usbdevice_fs.h="">


int main(int argc, char **argv)
{
 const char *filename;
 int fd;
 int rc;

 if (argc != 2) {
  fprintf(stderr, "Usage: usbreset device-filename\n");
  return 1;
 }
 filename = argv[1];

 fd = open(filename, O_WRONLY);
 if (fd < 0) {
  perror("Error opening output file");
  return 1;
 }

 printf("Resetting USB device %s\n", filename);
 rc = ioctl(fd, USBDEVFS_RESET, 0);
 if (rc < 0) {
  perror("Error in ioctl");
  return 1;
 }
 printf("Reset successful\n");

 close(fd);
 return 0;
}
</linux></sys></errno.h></fcntl.h></unistd.h></stdio.h>
```

Компилируем его:

```shell
cc usbreset.c -o usbreset
```

Узнаём, какое устройство нужно сбросить:

```shell
lsusb
Bus 002 Device 003: ID 0fe9:9010 DVICO
```

Делаем наш файл исполняемым:

```shell
chmod +x usbreset
```

И выполняем:

```shell
sudo ./usbreset /dev/bus/usb/002/003
```

Вот и всё.

Restored from [original](https://web.archive.org/web/20200206175018/http://conformist-mw.blogspot.com/2015/10/usb-usbreset.html)
