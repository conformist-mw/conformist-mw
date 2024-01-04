---
title: Приручаем tmux
date: 2016-01-24 22:08:29
tags:
  - linux
  - linux4all
  - tmux
  - debian
---

Как пишет арчевики (и все остальные), `tmux` — терминальный мультиплексор. Самое удобное в нём то, что можно отключиться от сессии и продолжить работу, например, по ssh. Так же удобно в нём держать консольные приложения, если таковыми пользуешься. Главной целью настройки для меня было обеспечение незаметности этого мультиплексора, чтобы его поведение было подобно обычному терминалу + удобства tmux. Далее я опишу строки из своего конфига с объяснением, что и зачем.

```bash
# снимаем бинд с дефолтного сочетания клавиш
unbind C-b
# указываем свой бинд — обратный апостроф
set -g prefix `
# т.к. сам обратный апостроф тоже иногда нужен, добавим ещё одну команду,
# которая позволяет использовать его при втором нажатии на бинд клавишу
bind ` send-prefix
# начало нумерации окон с 1, а не с 0
set -g base-index 1
# начало нумерации панелей в окне с 1
setw -g pane-base-index 1
# запретить изменять имя окна
set -g allow-rename off
# отключить визуальную активность
set-option -g monitor-activity off
# быстрая перезагрузка конфига
bind r source-file ~/.tmux.conf \; display "Reloaded!"
# вертикальное и горизонтальное разделение окна
bind | split-window -h
bind - split-window -v
# движение между панелями в стиле vi
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R
# поддержка мыши (режим копирования включается скроллом)
set -g mouse-utf8 on
set -g mouse on
# utf-8 в статус строке (для добавления необычных символов)
set -g status-utf8 on
# частота обновления статус-строки
set -g status-interval 0
# поддержка 256 цветов
set -g default-terminal "screen-256color"
# цвета для статус строки. Здесь допускается цифровое выражение.
# Чтобы увидеть все цвета, можно воспользоваться скриптом:
# wget http://www.vim.org/scripts/download_script.php?src_id=4568 -O colortest
# perl colortest -w
set -g status-fg colour245
set -g status-bg colour238
setw -g window-status-fg white
setw -g window-status-bg default
setw -g window-status-attr dim
setw -g window-status-current-fg white
setw -g window-status-current-bg white
setw -g window-status-current-attr bright # выделить текущее окно
setw -g window-status-separator "|" # разделитель окон
# в статус панели можно указывать цвета так: #[fg=colour###]
setw -g window-status-current-format " #[fg=black]#I:#[fg=blue]#W "
# status-left — это то место, где указывается имя сессии.
# у меня всегда одна сессия, потому я просто скрыл
set -g status-left ""

# правая часть статус панели. Можно использовать команды #(cmd),
# но почему-то у меня не заработала #(pwd), чтобы как-то видоизменить вывод
# потому использую встроенную pane_current_path — в фигурных скобках.
set -g status-right "#{pane_current_path} %R %d/%m/%g"
# окна, которые нужно запустить. после команды указано bash — для того,
# чтобы когда выйдешь из программы окно не закрывалось. В этом случае
# remain-on-exit не помогает, потому так
new -n rss 'newsbeuter;bash'
neww -n mutt 'mutt;bash'
neww -n mocp 'mocp;bash'
neww -n bash
# выбрать последнее окно
selectw -t 4
```

Очень удобно для работы вместе с `Tilda` — dropdown терминал.

Restored from [original](https://web.archive.org/web/20200207004404/http://conformist-mw.blogspot.com/2016/01/tmux.html)
