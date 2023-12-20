---
title: Ngrep или Network Grep
date: 2015-03-17 21:51:43
tags:
  - linux
  - grep
  - linux4all
  - network
---

Хороший инструмент для исследования пакетов, фильтруя по заданным полям. Установка крайне простая:

```shell
apt-get install ngrep
```

Использование:

```shell
ngrep -d wlan0 '^POST' port 80
```

здесь d — device, можно вписать any (все доступные интерфейсы)
'^POST' — регулярка, по которой парсим.
port — это порт (кэп)

данной командой мы запросто отследим пару логин/пароль при входе на какой-нибудь сайт, где не шифруются данные при аутентификации.

Так же просто отслеживать разные там ftp, все, что угодно. Пример с сайта nnm-club:

```shell
~$ sudo ngrep -d any '^POST' port 80
interface: any
filter: (ip or ip6) and ( port 80 )#############################
T 10.176.94.88:53332 -> 46.246.41.69:80 [AP]
  POST /forum/login.php HTTP/1.1..Host: nnm-club.me..User-Agent: Mozilla/5.0 (X11; Li
  nux i686; rv:36.0) Gecko/20100101 Firefox/36.0 Iceweasel/36.0..Accept: text/html,ap
  plication/xhtml+xml,application/xml;q=0.9,*/*;q=0.8..Accept-Language: ru-RU,ru;q=0.
  8,en-US;q=0.5,en;q=0.3..Accept-Encoding: gzip, deflate..Referer: http://nnm-club.me
  /..Cookie: __utma=202707022.1038152644.1417554679.1420653778.1420878753.9; __utmz=2
  02707022.1419369470.3.2.utmcsr=go.mail.ru|utmccn=(organic)|utmcmd=organic|utmctr=%D
  0%BC%D0%B0%D0%B6%D0%BE%D1%80; phpbb2mysql_4_data=a%3A2%3A%7Bs%3A11%3A%22autologinid
  %22%3Bs%3A0%3A%22%22%3Bs%3A6%3A%22userid%22%3Bs%3A7%3A%227127504%22%3B%7D; phpbb2my
  sql_4_sid=25fcb38ec27ecb7d930f10f53afd7052..Connection: keep-alive..Content-Type: a
  pplication/x-www-form-urlencoded..Content-Length: 66....
redirect=%2F&username=here_login&password=here_pass&login=%C2%F5%EE%E4
```

так же у ngrep есть ключик -t который указывает время определения пакета, -O — писать в файл.
Страница проекта — http://ngrep.sourceforge.net/

Restored from [original](https://web.archive.org/web/20200206164241/http://conformist-mw.blogspot.com/2015/03/ngrep-network-grep.html)
