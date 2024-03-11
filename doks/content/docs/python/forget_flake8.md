---
title: Forget Flake8
---

There is a good alternative instead called [ruff](https://beta.ruff.rs/docs/)
 which is written in Rust.

It is extremely fast and can replace almost all popular extensions.

So instead of a bunch of flake8 extensions and isort just put something like this
into the `pyproject.toml`:

```toml
[tool.ruff]
target-version = "py311"
line-length = 100
fix = true
ignore-init-module-imports = true
select = [
  "E",    # pycodestyle
  "F",    # pyflakes
  "I",    # isort
  "A",    # flake8-builtins
  "COM",  # flake8-commas
  "T20",  # flake8-print
  "Q",    # flake8-quotes
  "BLE",  # flake8-blind-except
  "N",    # pep8-naming
]

[tool.ruff.flake8-quotes]
inline-quotes = "single"
```
