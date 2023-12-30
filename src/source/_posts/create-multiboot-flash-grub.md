---
title: Создание multiboot флешки с grub2, grub4dos в linux
date: 2023-12-30 15:48:55
tags:
  - grub2
  - grub4dos
  - linux
  - linux4all
  - multiboot
---

Данный сказ будет о том, как создать мультизагрузочную флешку в linux. Подобных статей хватает в гугле, но я попробую изложить всё кратко и понятно не повышая энтропии Вселенной. Для чего нужна такая флешка — думаю говорить не стоит, всем и так понятно, потому сразу к делу.

Так получилось, что существует 2 основных загрузчика для этого мероприятия — `grub2` и `grub4dos` (основанный на GNU GRUB). И если GRUB2 доступен изначально в подавляющем большинстве дистрибутивов linux, то `grub4dos` нужно скачать отдельно.
Итак, для начала берём подопытную флешку, форматируем любым удобным образом её в `fat32`. Устанавливаем эмулятор для тестов (в debian-based будет так):

```shell
apt-get install qemu-system
```

и далее устанавливаем `grub2` на флешку (тут условимся, что флешка у меня — `/dev/sdb`, а монтирую я её в `/mnt/flash`):

```shell
grub-install --force --no-floppy --boot-directory=/mnt/flash/boot /dev/sdb
```

подкаталог `boot` grub создаст сам. В общем на этом этапе всё уже готово, далее нужно создать конфигурационный файл `/mnt/flash/boot/grub/grub.cfg` В этом файле описываем, что будем грузить и откуда. Создадим каталог для наших образов в корне флешки, который в абсолютном виде будет таким — `/mnt/flash/iso`. Начнём с lubuntu (считается легковесной, в принципе удобна для live и умеет использовать casper)

```bash
set timeout=10
set default=0

 menuentry "Lubuntu" {
  loopback loop /iso/lubuntu.iso
  linux (loop)/casper/vmlinuz persistent boot=casper \
  iso-scan/filename="/iso/lubuntu.iso" noeject noprompt splash toram --
  initrd (loop)/casper/initrd.lz
 }
```

Разберём, что здесь написано. Нужно найти образ в каталоге iso с названием `lubuntu.iso`. Далее указываем опции загрузки. Я решил lubuntu сделать с сохранением состояния, чтобы один раз настроить как нужно и потом пользоваться. Для этого в опциях ядра указываем — `persistent` и создаём в корне флешки специальный файл `casper-rw` с созданной в ней файловой системой ext2 или ext3:

```shell
dd if=/dev/zero of=/mnt/flash/casper-rw bs=1M count=250
mkfs.ext2 /mnt/flash/casper-rw
```

Добавляем остальные linux дистрибутивы по желанию, всё в принципе аналогично. Так же просто можно добавить `memtest` (берём [отсюда](https://memtest.org/) последнюю (или не последнюю) версию в виде `.bin`) и добавляем в наш `grub.cfg`:

```bash
menuentry "Memtest 86+" {
 linux16 /iso/memtest86+.bin
 }
 ```

Далее чуть интереснее. Чтобы запустить DOS программки, необходим файл `memdisk` из пакета `syslinux`. В linux его найти достаточно просто:

```shell
locate memdisk
```

в debian он лежит по пути `/usr/lib/syslinux/memdisk` Копируем его в `/mnt/flash/boot` и можно использовать для того, например, чтобы запустить victoria, вот так:

```shell
menuentry "Victoria" {
 linux16 /boot/memdisk iso raw
 initrd16 /iso/vcr35r.iso
}
```

На этом можно было бы и остановиться, но часто флешка нужна не только для запуска linux дистрибутивов. Для разных утилит Windows `grub2` или не подходит или с ходу не разобраться как правильно подключить тот или иной образ. Поэтому, чтобы упростить себе жизнь, можно воспользоваться grub4dos. Плюсы очевидны, для него много чего описано и нам не нужно придумывать свой велосипед.

Итак, идём [сюда](https://sourceforge.net/projects/grub4dos/) и качаем нужную версию. Распаковываем архив в каталог `/mnt/flash/boot/grub4dos` и создаём в нём файл `menu.lst`. Далее указываем в `grub.cfg` новый загручик:

```bash
menuentry "grub4dos" {
 linux16 /boot/grub4dos/grub.exe --config-file=/boot/grub4dos/menu.lst
}
```

а в `menu.lst` переход обратно в grub2:

```bash
title grub2
find --set-root /boot/grub/i386-pc/core.img
kernel /boot/grub/i386-pc/core.img
```

Теперь у нас доступны оба загрузчика и добавлять новые образы можно туда, откуда грузить их удобнее. Например для загрузки образа `Passcape Reset Windows Password` (нетрудно найти на просторах интернета) в `menu.lst` пишем следующее:

```bash
title Passcape Reset Windows Password
fallback 7
map /iso/PASSCAPE.iso (0xFF) ||map --mem /iso/PASSCAPE.iso (0xFF)
map --hook
chainloader (0xFF)
```

Есть вариант попроще — использовать `NT Password & Registry Editor`. Официальный сайт здесь. Вот тут можно взять все исходные файлы для CD, usb или floppy. Я использую образ флоппи и подключаю его так в grub4dos (menu.lst):

```bash
title NT Password & Registry Editor
find --set-root /iso/NtPasRec.IMA
map --mem /iso/NtPasRec.IMA (fd0)
map --hook
rootnoverify (fd0)
chainloader (fd0)+1
```

Или проще в grub.cfg:

```bash
menuentry "windows pass" {
 linux16 /boot/memdisk
 initrd16 /iso/NtPasRec.IMA
}
```

Всё остальное добавляеся по вкусу, тестируется это с помощью `qemu` так:

```shell
sudo qemu-system-x86_64 -hda /dev/sdb
```

На данный момент можно уже и закончить, но если хочется немного украшательств, то, пожалуй, расскажу как это сделать. GRUB2. Добавляем в `grub.cfg` следующие строки:

```bash
# указываем графический режим, согласно размеру картинки для фона
set gfxmode=800x600x32
# загружаем шрифт (полный путь наподобии /boot/grub/fonts/font.pf2 указывать нельзя)
loadfont $prefix/fonts/unicode.pf2
# загружаем модуль поддержки графического режима
insmod vbe
# загружаем модуль графического терминала
insmod gfxterm
# ну и сам графический терминал
terminal_output gfxterm
# загружаем модуль поддержки изображений (jpeg, png или tga)
insmod png
background_image /boot/back.png
# раскрашиваем терминал (black = прозрачный)
set color_normal=white/black
set color_highlight=black/white
set menu_color_normal=white/black
set menu_color_highlight=yellow/black
```

Теперь всё будет красиво, разрешение можно и побольше указать.
grub4dos: Тут всё немного интересней. `splashimage` уже не поддерживается, нужно использовать `gfxmenu`. Можно взять готовое, но если хочется своего, нужно скачать отсюда архив с примером gfxmenu. Далее:

```shell
 # распаковываем
 $ unzip gfxboot-3.3-custom.zip
 # создаём рабочий каталог и переходим в него
 $ mkdir files && cd $_
 # распаковываем пример gfxmenu
 $ cpio -i < /path/to/dir/3.3/message_en
 # удаляем все графические файлы и кладём свою картинку back.jpg
 # редактируем gfxboot.cfg по желанию и собираем архив
 $ ls | cpio -o > /mnt/flash/boot/grub4dos/message
```

Подключаем этот файл в `menu.lst`:

```bash
gfxmenu /boot/grub4dos/message
```

вот и готово графическое меню. Стоит заметить, что картинка должна быть сохранена в jpeg 1 формате. Проще всего её открыть в вендовом Paint и просто сохранить. Если формат отличный от 800x600 — это нужно указать в `gfxboot.cfg`
На этом вроде бы всё, флешка готова.

Restored from [original](https://web.archive.org/web/20200206160842/http://conformist-mw.blogspot.com/2015/12/multiboot-grub2-grub4dos-linux.html)
