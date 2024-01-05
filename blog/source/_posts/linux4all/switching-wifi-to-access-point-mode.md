---
title: Переключение wifi в режим точки доступа
date: 2015-01-20 06:03:00
tags:
  - linux4all
  - wifi
  - debian
  - dnsmasq
  - hostapd
---

Раздать wi-fi с 3g модема (и с проводного интернета), когда ничего другого нет под рукой — дело благородное, потому мы пройдем по быстрому пути получения профита.
Инструкция предназначена для debian-based дистрибутивов. Нам понадобится `hostapd` — собственно для раздачи wi-fi, `dnsmasq` — для раздачи ip-адресов и `notify-send` (не обязательно) — для оповещений. `iptables` на данный момент доступен из коробки. Ставим `hostapd` и останавливаем его:

```shell
aptitude install hostapd
service hostapd stop
```

В файле `/etc/default/hostapd` раскомментируем и исправляем строку:

```txt
DAEMON_CONF="/etc/hostapd/hostapd.conf"
```

Создаем и редактируем файл `/etc/hostapd/hostapd.conf`

```ini
interface=wlan0
driver=nl80211
ssid=wifi_4_friends
hw_mode=g
channel=6
wpa=2
wpa_passphrase=12345678
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP
auth_algs=1
macaddr_acl=0
```

Тут все просто — имя точки доступа, пароль, канал, на котором будет работать и драйвер.
Ставим `dnsmasq` и останавливаем его:

```shell
aptitude install dnsmasq
service dnsmasq stop
```

`dnsmasq` хорош тем, что в нем все есть и он прост для настройки. Открываем файл конфигурации `/etc/dnsmasq.conf`:

```ini
interface=wlan0
dhcp-range=192.168.2.2,192.168.2.100,12h
```

Тут все крайне просто, но если нужно, можно добавить альтернативный dns сервер, а также можно хосты принудительно направлять на 127.0.0.1, тем самым блокируя их. Подробности в справке `man dnsmasq`. Еще один момент, обязательно адреса dhcp-range должны быть в одной сети с wlan0. если Вы по каким-либо соображениям в скрипте запуска не будете принудительно менять ip адрес для wlan0, то укажите тут пул такой же, как в wlan0. Например дома есть роутер с адресом 192.168.1.1 и сеть 192.168.1.0/24, то dhcp-range нужно указать в пределах этого пространства, а также, чтобы он не пересекался с пулом адресов, выдаваемых dhcp-сервером роутера. Мы пойдем путем по-проще и сами укажем другую подсеть.

Теперь отключим автозагрузку демонов:

```shell
update-rc.d hostapd disable
update-rc.d dnsmasq disable
```

Ко всему этому осталось только включать/отключать роутинг и добавлять/удалять правило из iptables

```shell
sysctl net.ipv4.ip_forward=1
iptables -t nat -A POSTROUTING -o ppp0 -j MASQUERADE
```

Это будем производить автоматически, с помощью скрипта. Все готово, а вот и сам скрипт wifi-ap:

```bash
#!/bin/bash
#script to start/stop hostapd, dnsmasq, add/remove iptables rule

set -e
exec 3>&1
exec 2>&1 >> /tmp/wifi-ap

function print_help(){
    echo "Start/Stop Software Access Point"
    echo
    echo "Usage `basename $0` options..."
    echo "wifi-ap on to start Software AP"
    echo "wifi-ap off to stop Software AP"
    echo
    echo "log-file - /tmp/wifi-ap"
    echo
}
if [ $# = 0 ]; then
    print_help >&3
        exit 0
fi

if [ $1 = on ]; then
        ifconfig wlan0 192.168.2.1
        service dnsmasq start
        sysctl net.ipv4.ip_forward=1
        iptables -t nat -A POSTROUTING -o ppp0 -j MASQUERADE
        service hostapd start
        notify-send --expire-time=4000 "Software Access Point" "<b>start</b>"
    exit 0
fi

if [ $1 = off ]; then
        service dnsmasq stop
        service hostapd stop
        ifconfig wlan0 192.168.1.4
        sysctl net.ipv4.ip_forward=0
        iptables -D POSTROUTING -t nat -o ppp0 -j MASQUERADE
        notify-send --expire-time=4000 "Software Access Point" "<b>stop</b>"
    exit 0
fi
```

Он принимает 2 параметра, on и off. Вы легко можете подкорректировать его под себя и, если нужно, заменить интерфейс ppp0 на eth0 (или другой, на Ваше усмотрение).
Также с переходом на systemd нужно заменить service и update.rc на systemctl, хотя это и не обязательно, ведь пока systemd обратно совместим с sysvinit скриптами

Restored from [original](https://web.archive.org/web/20200206174212/http://conformist-mw.blogspot.com/2015/01/wifi_20.html)
