---
title: internet from phone via bt
date: 2016-02-01 11:04:28
tags:
  - linux
  - linux4all
  - bluetooth
  - android
  - modem
---

Понадобилось мне подключить интернет через телефон nokia 6760. Это довольно таки не сложно, как оказалось. Для начала ищем наш телефон:

```shell
hcitool scan

Scanning ...
    00:BD:3A:DA:8E:5F       Nokia 6760
```

Далее нужно узнать на каком канале у него Dial-up Networking:

```shell
sdptool search DUN | grep Channel

 Channel: 5
```

Так же можно попинговать телефон, если это интересно:

```shell
l2ping 00:BD:3A:DA:8E:5F

Ping: 00:BD:3A:DA:8E:5F from 74:F0:6D:C2:09:9A (data size 44) ...
0 bytes from 00:BD:3A:DA:8E:5F id 0 time 35.70ms
0 bytes from 00:BD:3A:DA:8E:5F id 1 time 36.44ms
0 bytes from 00:BD:3A:DA:8E:5F id 2 time 35.54ms
0 bytes from 00:BD:3A:DA:8E:5F id 3 time 10.72ms
0 bytes from 00:BD:3A:DA:8E:5F id 4 time 11.49ms
Send failed: Connection reset by peer
```

Далее сопрягаем телефон с ноутбуком:

```shell
bluetoothctl

[NEW] Controller 74:F0:6D:C2:09:9A hopeless [default]
[bluetooth]# scan on
Discovery started
[CHG] Controller 74:F0:6D:C2:09:9A Discovering: yes
[NEW] Device 00:BD:3A:DA:8E:5F Nokia 6760
[bluetooth]# agent on
Agent registered
[CHG] Device 00:BD:3A:DA:8E:5F Connected: yes
[CHG] Device 00:BD:3A:DA:8E:5F Connected: no
[bluetooth]# pair 00:BD:3A:DA:8E:5F
Attempting to pair with 00:BD:3A:DA:8E:5F
[CHG] Device 00:BD:3A:DA:8E:5F Connected: yes
Request PIN code
[agent] Enter PIN code: 1
[CHG] Device 00:BD:3A:DA:8E:5F Paired: yes
Pairing successful
[bluetooth]# connect 00:BD:3A:DA:8E:5F
Attempting to connect to 00:BD:3A:DA:8E:5F
[CHG] Device 00:BD:3A:DA:8E:5F Connected: yes
Connection successful
[Nokia 6760]# trust 00:BD:3A:DA:8E:5F
[CHG] Device 00:BD:3A:DA:8E:5F Trusted: yes
Changing 00:BD:3A:DA:8E:5F trust succeeded
[bluetooth]# quit
Agent unregistered
[DEL] Controller 74:F0:6D:C2:09:9A hopeless [default]
```

Биндим телефон (устройство 0 — `/dev/rfcomm0`, канал 5-й):

```shell
rfcomm bind 0 00:BD:3A:DA:8E:5F 5
```

Теперь можно проверить, всё ли работает. Подключаемся через minicom к телефону и звоним, например, себе:

```shell
minicom --device /dev/rfcomm0

 Welcome to minicom 2.7

OPTIONS: I18n
Compiled on Nov 30 2015, 11:52:37.
Port /dev/rfcomm0, 12:09:13

Press CTRL-A Z for help on special keys

atdt+380*********
BUSY
```

Итак, всё работает, остаётся лишь настроить подключение. Через NetworkManager у меня так и не получилось ничего сделать, потому опишу обычный способ через pon/poff. Создаём файл `/etc/ppp/peers/nokia`:

```shell
connect "/usr/sbin/chat -v \
TIMEOUT 35 \
ECHO    ON \
ABORT   '\nBUSY\r' \
ABORT   '\nERROR\r' \
ABORT   '\nNO ANSWER\r' \
ABORT   '\nNO CARRIER\r' \
ABORT   '\nNO DIALTONE\r' \
ABORT   '\nRINGING\r\n\r\nRINGING\r' \
ABORT   '\nUsername/Password Incorrect\r'  \
''      \rAT \
OK      'AT+CGDCONT=1,\"IP\",\"ab.kyivstar.net\"' \
OK      ATD*99# \
CONNECT \c \
"

/dev/rfcomm0
460800
#mtu 1400
crtscts
noipdefault
noipv6
defaultroute
replacedefaultroute
hide-password
noauth
persist
usepeerdns
user internet
```

И теперь достаточно набрать `pon nokia` и мы получим интернет с телефона через bluetooth.

Restored from [original](https://web.archive.org/web/20200206171146/http://conformist-mw.blogspot.com/2016/02/bluetooth.html)
