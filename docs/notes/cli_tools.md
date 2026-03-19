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

## [dive](https://github.com/wagoodman/dive)

A tool for exploring Docker image layers and discovering ways to shrink image
size. Shows image contents broken down by layer, indicates what changed in each
layer (added/modified/removed files), and estimates image efficiency (wasted
space from duplicated or leftover files). Supports `docker`, `podman`, and
`docker-archive` sources. Can also run in CI mode (`CI=true`) to enforce
efficiency thresholds.

Written in Go. Available on Linux, macOS, and Windows.

```bash
# macOS
brew install dive

# usage
dive <your-image-tag>
dive build -t <some-tag> .
```
