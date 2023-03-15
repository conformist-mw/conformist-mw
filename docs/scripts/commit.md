---
title: Commit whenever you want
---

### Description

Sometimes you need to commit something in the specific date (respecting
 `committer` and `author` dates), so here is a ad-hoc script to do this in the
  random time within a chosen day.

 Save it and add executable flag:

 ```shell
touch ~/.local/bin/git-commit
chmod +x ~/.local/bin/git-commit
 ```

### Usage example

 ```shell
 git-commit 2023-01-01 "git commit message"
 ```

 ### Script

 ```python
 #!/usr/bin/env python

import argparse
import os
import subprocess
from datetime import datetime, time
from random import randint


def get_parser():
    parser = argparse.ArgumentParser(
        description="Example: git-commit 2020-12-29 'git commit message'",
    )
    parser.add_argument(
        'date',
        type=lambda s: datetime.strptime(s, '%Y-%m-%d'),
        help='target date in format: 2020-12-29',
    )
    parser.add_argument('message', help='git commit message')
    return parser.parse_args()


if __name__ == '__main__':
    args = get_parser()
    target_date = datetime.combine(
        args.date,
        time(randint(7, 23), randint(0, 59)),
    ).isoformat()
    env = os.environ
    env.update({
        'GIT_AUTHOR_DATE': target_date,
        'GIT_COMMITTER_DATE': target_date,
    })

    subprocess.Popen(['git', 'commit', '-m', args.message], env=env)
```
