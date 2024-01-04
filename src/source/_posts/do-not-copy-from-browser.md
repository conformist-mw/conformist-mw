---
title: Не копируйте из браузера сразу в терминал!
date: 2016-02-13 13:03:10
tags:
  - linux4all
  - security
  - terminal
---

Короткая заметка о том, что не нужно бездумно копировать из браузера прямо в терминал на сайтах, которым вы недоверяете.

[Вот тут](https://thejh.net/misc/website-terminal-copy-paste) пример такого приёма. Внешне кажется, что кусочки кода снизу и сверху одинаковы, но это не так. Скопировав верхнюю строку в текстовый редактор мы увидим следующее:

```shell
git clone /dev/null; clear; echo -n "Hello ";whoami|tr -d '\n';echo -e '!\nThat was a bad idea.
Don'"'"'t copy code from websites you don'"'"'t trust!
Here'"'"'s the first line of your /etc/passwd: ';head -n1 /etc/passwd
git clone git://git.kernel.org/pub/scm/utils/kup/kup.git
```

Разумеется, для демонстрации тут приведен безвредный список команд, но как нибудь можно попасть и на более вредный.

Restored from [original](https://web.archive.org/web/20200206164739/http://conformist-mw.blogspot.com/2016/02/blog-post.html)
