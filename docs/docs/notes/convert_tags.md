To convert tags (so they are displayed correctly at Jellyfin, for example):

```shell
sudo apt install mediainfo python3-mutagen
```

To ensure that file has incorrect encoding in tags:

```shell
mediainfo file.mp3
```

convert (usually from win-1251):

```shell
mid3iconv -p -e CP1251 file.mp3  # `-p` for dry-run
```

convert all files:

```shell
find /path/to/music -type f -name '*.mp3' -exec mid3iconv -e CP1251 {} \;
```
