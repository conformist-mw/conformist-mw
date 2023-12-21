---
title: Экспорт паролей из chrome/chromium в .csv
date: 2015-03-20 21:14:45
tags:
---

Уже давно google chrome(chromium) не хранят пароли в открытом виде в файле Local Data, как это было раньше. И это создает некоторые трудности при желании экспортировать все пароли в удобочитаемый вид, чтобы хранить их или еще для чего-то. Но получить пароли все так же не сложно, для этого и существует данная заметка.

Нам нужен linux с установленным хромом, открываем терминал и пишем следующее:

```shell
#google chrome:
google-chrome --user-data-dir=/tmp/chrome-tmp --password-store=basic

#chromium:
chromium --user-data-dir=/tmp/chrome-tmp --password-store=basic
```

После этого запустится браузер и предложит войти в аккаунт google, входим, заходим в параметры синхронизации и убираем все галки, кроме паролей (зачем синхронизировать лишнее?). Таким образом мы получим локальную копию вашего аккаунта, но основные настройки не будут затронуты, ничего не изменится.

Далее идем в каталог
```shell
cd /tmp/chrome-tmp/Default
```

и находим файл Login Data. На этом шаге очень хорошо, если установлен sqlite3, если нет, установите.
Далее:

```shell
sqlite3 'Login Data'
```

Откроется консоль sqlite, в которой пишем:

```sqlite
.mode csv
.headers on
.separator ","
.output chrome_pass.csv
select * from logins;
.exit
```

Так мы получили файл `chrome_pass.csv`, который можно открыть в `libreoffice`, например:

```shell
localc chrome_pass.csv
```

Вот и все, каталог `/tmp` очистится после перезагрузки

```xml
<paranoic mode> rm -r /tmp/chrome-tmp </paranoic mode>
```

В firefox/thunderbird для хранения паролей используется 3 файла:

- `signons.sqlite` — собственно файл, хранящий пары логин/пароль в plaintext, но зашифрован ключом;
- `key3.db` и `cert8.db` — ключ и сертификат для расшифровки файла `signons.sqlite`

Т.е. просто так не посмотришь, но мир не без добрых людей, которые уже написали множество реализаций для получения паролей в чистом виде. Например здесь — скрипт, написанный на python, который достаточно просто запустить, он сам найдет путь хранения паролей и выдаст полный список в формате:

```
--Site(https://example.com):
----Username здесь будет логин
----Password здесь будет пароль
```

С точки зрения *nix подхода этот способ даже лучше:

```shell
./ffpassdecrypt | grep -A2 site
```

получаем все совпадения к конкретному сайту, а не всю кучу, как в случае с chrome

Restored from [original](https://web.archive.org/web/20200206164241/http://conformist-mw.blogspot.com/2015/03/chromechromium-csv.html)
