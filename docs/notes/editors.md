---
title: Editor helpers
---

### Editorconfig

Common example of [editorconfig](https://editorconfig.org/)

```ini
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true

[*.json]
insert_final_newline = false

[*.py]
indent_style = space
indent_size = 4
max_line_length = 100

[*.{html,js,json,yml,yaml,md}]
indent_style = space
indent_size = 2
max_line_length = 100
```
