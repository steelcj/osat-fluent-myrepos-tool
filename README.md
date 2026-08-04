# osat-fluent-myrepos-tool

Version: 0.1.0
Status: Draft

## Description

### Why use an osat-fluent installer?

osat-fluent installers are designed to be as useful as possible without being invasive.

Many of the cross platform agnostic as possible. In addition they install the target package 

Installs and maintains 

A user-space manager for [myrepos](https://myrepos.branchable.com/) (`mr`), part of the OS Sovereign Autonomous Tools (OSAT) Fluent collection.



This manager installs versioned, self-contained myrepos artifacts to `~/.local/share/myrepos-tool/<version>/`, activated through a single env file and generated wrapper scripts for `mr` and `webcheckout`. Full documentation lives in [docs/en/README.md](docs/en/README.md).

## Installation

Create the directory you want to keep your installers in:

```bash
mkdir ~/areas/development
cd ~/areas/development
```

### Clone the installer

For usage only

```bash
git clone https://github.com/steelcj/osat-fluent-myrepos-tool.git
```

If you want to develop

```bash
git clone git@github.com:steelcj/osat-fluent-myrepos-tool.git
```

Install

```bash
python3 install-myrepos.py --install
mr --version
```

## Platform support

Linux and macOS. **Standard Windows is not supported and cannot be**: `mr` requires Perl and executes every configured action through a POSIX shell, neither of which standard Windows provides. On Windows, use myrepos inside WSL, where this manager works as on Linux. This is a property of the tool, not a gap in the manager, so there is no Windows item on the roadmap.

## Vulnerabilities

### Current versions, none

A vulnerability affected the **`webcheckout` feature** of `myrepos` through version **1.20171231**. We are currently installing version 1.20180727

Is it fixed?

Yes. Later versions of `myrepos` changed how URLs are handled to prevent these attacks.

### Old versions of myrepos

In previous versions the issue was that `mr` would take a repository URL from an untrusted web page and pass it directly to `git clone` without validating or sanitizing it. An attacker could craft a malicious URL that Git interpreted as something other than a normal repository location, potentially leading to arbitrary command execution.

This did **not** affect ordinary uses of `mr` with trusted `.mrconfig` files.

* [Debian 13 — myrepos — vulnerability — patch and remediation guide](https://www.progressiverobot.com/2026/05/25/debian-13-myrepos-vulnerability-patch-remediation/)

## License

This software, *osat-fluent-myrepos-tool*, by **Christopher Steel**, is licensed under the [GNU General Public License v3.0 or later (GPL-3.0-or-later)](https://www.gnu.org/licenses/gpl-3.0.html).

You may redistribute and/or modify this software under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

See the `LICENSE` file included with this project for the full license text. myrepos itself is by Joey Hess and is licensed under the GNU General Public License version 2 or later; this manager installs it unmodified.

[![License: GPL v3+](https://img.shields.io/badge/License-GPLv3%2B-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
