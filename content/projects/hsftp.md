---
title: ":link: Hsftp"
description: "This is the hsftp homepage."
showSummary: true
summary: "A command-line tool for secure file transfer operations, written in Haskell."
categories: ["Network", "program", "utils"]
tags: ["Haskell"]
cascade:
  showReadingTime: true
---

**Hsftp** is a SFTP client tool for secure file transfer operations.
You can find and fork the source code on [GitHub](https://github.com/iocrafts/hsftp) for personal or collaborative use.


## Installation

You can find and install the package directly from [Hackage](https://hackage.haskell.org/package/hsftp):

```bash
cabal update
cabal install hsftp
```

or

```bash
stack install hsftp
```

## Usage

```bash
Hsftp 1.5.0. Usage: hsftp OPTION

hsftp [OPTIONS] [ITEM]

Common flags:
  -c --conf=FILE          Load conf from file
     --from-date=DATE     Filter files by date (YYYY-MM-DD HH:MM UTC|PST|...)
  -e --extensions=ITEM    Filter files by extensions
  -u --up                 upload
  -d --down               download
     --transfer-from=DIR  Folder to transfer from
     --transfer-to=ITEM   Folder to transfer to
     --archive-to=DIR     Folder to archive to after upload

Miscellaneous:
     --verbose=INT        Verbose level: 1, 2 or 3
  -n --dry-run            Do a dry-run ("No-op") transfer.
  -? --help               Display help message
  -V --version            Print version information
     --numeric-version    Print just the version number
```

### Configuration File

```yaml
# conf.yaml

remote:
        hostname: sftp.domain.com
        port: 22
        username: username
        password: password
        known_hosts: /home/user/.ssh/known_hosts
```

## Examples

### Download - filter by date

```bash
hsftp -c conf.yaml -d \
    --transfer-from /path/to/remote/folder \
    --transfer-to /path/to/local/folder \
    --from-date "2024-06-14 12:15 PDT"
```

### Upload - filter by extension

```bash
hsftp -c conf.yaml -u \
    --transfer-from /path/to/local/folder \
    --transfer-to /path/to/remote/folder \
    -e xml -e Xml
```

### Upload - archive files locally after upload

```bash
hsftp -c conf.yaml -u \
    --transfer-from /path/to/local/folder \
    --transfer-to /path/to/remote/folder \
    --archive-to /path/to/local/archive/folder
```

