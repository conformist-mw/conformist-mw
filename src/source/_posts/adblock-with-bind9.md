---
title: adblock with bind9
date: 2015-04-28 15:02:50
tags:
  - linux
  - linux4all
  - adblock
  - bind
  - debian
  - dns
---

Adblock, всеми любимый, можно организовать и без расширения браузера, правда это будет не такой качественный `adblock` (`ublock`), но все же. Если машина используется как dns сервер в локальной сети, то уже сможет существенно облегчить жизнь и снизить нагрузку на сеть бесполезными запросами. Для этого нам понадобится web-server, dns-server и список ненужных хостов.

Опишем логику действий, для чего нужен веб-сервер? А для того, что если браузер наткнется на рекламу, путь к которой будет localhost, то на странице появятся жалобы, что контент не найден. Для этого мы будем использовать прозрачную картинку, которую будет отдавать веб-браузер.
Итак, начнем.

```shell
aptitude install apache2
```

Использовать будем уже имеющийся файл, потому скачаем его и положим туда, откуда потом и будем брать:

```shell
wget http://probablyprogramming.com/wp-content/uploads/2009/03/tinytrans.gif
mv tinytrans.gif a.gif
mkdir /var/www/adblock
mv a.gif /var/www/adblock
```

Настраиваем дефолтный хост apache2

```shell
vim /etc/apache2/sites-available/000-default.conf
```

в нем укажем:

```xml
<Virtualhost *:80>
  ServerAdmin webmaster@localhost
  ServerName localhost
  ServerAlias www.localhost
  DocumentRoot /var/www/adblock

  <Directory "/var/www/adblock">
    <Filesmatch "a.gif$">
      Header set Cache-Control "max-age=230204000, public"
    </Filesmatch>
    RewriteEngine On
    RewriteBase /
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-l
    RewriteRule ^(.*)$ http://localhost/a.gif
  </Directory>
</Virtualhost>
```

Если попробовать перезапустить apache сейчас, то получим ошибку, скорее всего, потому включим модуль headers:

```shell
a2enmod headers
apachectl graceful
```

Итак, замена блоков готова, по curl localhost мы должны получить страничку со ссылкой на файл `a.gif`. Переходим к bind9

```shell
aptitude install bind9
```

Открываем файл `/etc/bind/named.conf.options`, указываем в нем днс, которыми мы будем пользоваться:

```
forwarders {
  8.8.8.8; //google
  77.88.8.8; //yandex
};
```

Получаем файл, в котором указаны рекламные хосты:

```shell
sudo wget -O /etc/bind/blacklist 'http://pgl.yoyo.org/adservers/serverlist.php?hostformat=bindconfig&showintro;=0&mimetype;=plaintext'
```

Или сами идем по адресу http://pgl.yoyo.org/adservers/, там выбираем bind9 формат файла и сохраняем. В этом файле по умолчанию адрес файла блокировки `null.zone.file`, исправим это:

```shell
sed -i -e 's%null%/etc/bind/null%g' blacklist
```

Подключим этот файл в конфиге bind `/etc/bind/named.conf`

```
include /etc/bind/blacklist;
```

Создадим файл зоны для этого списка:

```
$TTL    86400

@   SOA blocked.site. root.blocked.site.    2015042600  1h  10m 1d  86400
        NS  blocked.site.
        A   127.0.0.1

@   IN  A   127.0.0.1
*   IN  A   127.0.0.1
```

Далее указываем нашему хосту использовать внутренний dns для запросов в файле `/etc/resolv.conf`:

```
nameserver 127.0.0.1
```

Рестартуем bind и проверяем:

```shell
rndc reload
dig +short zintext.com
127.0.0.1
```

Так же, если нужно добавить много хостов из личного файла (например black_hosts), можно сделать следующее (предполагается, что в файле хосты по одному на строчку):

```shell
cat black_hosts | while read line; do
> echo "zone \"$line\" { type master; notify no; file \"/etc/bind/null.zone.file\"; };"
> done >> /etc/bind/blacklist
```

Restored from [original](https://web.archive.org/web/20200207033657/http://conformist-mw.blogspot.com/2015/04/adblock-apache2-bind9.html)
