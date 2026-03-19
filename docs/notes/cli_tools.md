---
title: CLI Tools
description: Interesting CLI tools and utilities
---

## [witr](https://github.com/pranshuparmar/witr)

**"Why is this running?"** — a CLI tool (with interactive TUI mode) that explains
where a running process came from, how it was started, and what chain of systems
is responsible for it. Instead of manually correlating output from `ps`, `top`,
`lsof`, `ss`, `systemctl`, and `docker ps`, witr makes that causality explicit
in a single, human-readable output.

Written in Go. Available on Linux, macOS, FreeBSD, and Windows.

```bash
# quick install
curl -fsSL https://raw.githubusercontent.com/pranshuparmar/witr/main/install.sh | bash
```
