---
title: Keenetic notes
---

# Static DNS

Add a record
```shell
ip host my.domain.net 192.168.1.2

# all subdomains
ip host *.domain.net 192.168.1.2
```

Save config
```shell
system configuration save
```

Show all records
```shell
show dns-proxy
```

Delete record:
```shell
no ip host my.domain.net 192.168.1.2
```
