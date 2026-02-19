---
title: "Python Things"
date: 2026-02-17
draft: false
---

Inject params in pytest fixture using parametrize:

```python
import pytest


@pytest.fixture
def base():
    return "/tmp"


@pytest.fixture
def gerenate_path(base, path):
    return f"{base}/{path}"


@pytest.mark.parametrize("path", ["one", "two"])
def test_passing_path(gerenate_path):
    assert gerenate_path in ("/tmp/one", "/tmp/two")
```

Cheapest way to implement cache (with lazy evaluation):

```python
import boto3

_cache = {}

def get_s3_client() -> boto3.session.Session.client:
    if "s3" not in _cache:
        _cache["s3"] = boto3.client(service_name)
    return _cache[service_name]
```
