# osat-fluent-myrepos-tool

Version: 0.1.0
Status: Draft

A user-space manager for [myrepos](https://myrepos.branchable.com/) (`mr`), part of the OS Sovereign Autonomous Tools (OSAT) Fluent collection.

This manager installs versioned, self-contained myrepos artifacts to `~/.local/share/myrepos-tool/<version>/`, activated through a single env file and generated wrapper scripts for `mr` and `webcheckout`. Full documentation lives in [docs/en/README.md](docs/en/README.md).

## Quick start

```bash
python3 install-myrepos.py --install
mr --version
```

## Platform support

Linux and macOS. **Standard Windows is not supported and cannot be**: `mr` requires Perl and executes every configured action through a POSIX shell, neither of which standard Windows provides. On Windows, use myrepos inside WSL, where this manager works as on Linux. This is a property of the tool, not a gap in the manager, so there is no Windows item on the roadmap.

## License

This software, *osat-fluent-myrepos-tool*, by **Christopher Steel**, is licensed under the [GNU General Public License v3.0 or later (GPL-3.0-or-later)](https://www.gnu.org/licenses/gpl-3.0.html).

You may redistribute and/or modify this software under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

See the `LICENSE` file included with this project for the full license text. myrepos itself is by Joey Hess and is licensed under the GNU General Public License version 2 or later; this manager installs it unmodified.

[![License: GPL v3+](https://img.shields.io/badge/License-GPLv3%2B-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
