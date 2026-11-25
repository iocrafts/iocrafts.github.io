---
title: "Hsftp – Haskell SFTP Command-Line Client"
description: "Hsftp is an open-source SFTP command-line client written in Haskell for scripted uploads and downloads, with date and extension filters."
showSummary: true
summary: "A command-line tool for secure file transfer operations, written in Haskell."
categories: ["CLI", "Networking"]
tags: ["Haskell", "SFTP"]
---

**Hsftp** is an SFTP client tool for secure file transfer operations.
You can find and fork the source code on [GitHub](https://github.com/iocrafts/hsftp) for personal or collaborative use.

[![Hackage version](https://img.shields.io/hackage/v/hsftp)](https://hackage.haskell.org/package/hsftp)
[![License: BSD-3-Clause](https://img.shields.io/badge/license-BSD--3--Clause-blue)](https://github.com/iocrafts/hsftp/blob/main/LICENSE)

- **License:** BSD-3-Clause
- **Changelog:** [CHANGELOG.md](https://github.com/iocrafts/hsftp/blob/main/CHANGELOG.md)
- **Report a bug or request a feature:** [GitHub Issues](https://github.com/iocrafts/hsftp/issues)
- **Contribute:** pull requests are welcome on [GitHub](https://github.com/iocrafts/hsftp)

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

Run `hsftp --help` to list all options:

```text
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

### Download – Filter by Date

```bash
hsftp -c conf.yaml -d \
    --transfer-from /path/to/remote/folder \
    --transfer-to /path/to/local/folder \
    --from-date "2024-06-14 12:15 PDT"
```

### Upload – Filter by Extension

```bash
hsftp -c conf.yaml -u \
    --transfer-from /path/to/local/folder \
    --transfer-to /path/to/remote/folder \
    -e xml -e Xml
```

### Upload – Archive Files Locally After Upload

```bash
hsftp -c conf.yaml -u \
    --transfer-from /path/to/local/folder \
    --transfer-to /path/to/remote/folder \
    --archive-to /path/to/local/archive/folder
```

