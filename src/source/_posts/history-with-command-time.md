---
title: history with command time
date: 2015-03-10 21:47:41
tags:
  - linux
  - debian
  - linux4all
---

Захотелось мне как-то узнать когда была выполнена определенная команда, но history по-умолчанию не имеет такой функции, потому делаем так:

```shell
~# export HISTTIMEFORMAT='%F %T '
```

Разумеется, все команды теперь выполнены со временем экспорта переменной, но дальше время уже будет фиксироваться правильно, потому сделаем:

```shell
echo "export HISTTIMEFORMAT='%F %T '" >> ~/.bashrc
```

А если охота сделать это для всех новых пользователей, то добавить нужно в `/etc/profile`.

Еще несколько полезных переменных у history:

```bash
export HISTTIMEFORMAT="%h %d %H:%M:%S> " # удобочитаемый формат (man date)
export HISTCONTROL=ignoreboth # удаляет дубликаты команд (man bash, секция history)
export HISTIGNORE="df*:free*" # игнорируем некоторые команды
export HISTSIZE=10000 # количество команд для запоминания (500 by default)
```
