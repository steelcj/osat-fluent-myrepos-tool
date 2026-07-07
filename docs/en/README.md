# osat-fluent-myrepos-tool

Version: 0.1.0
Status: Draft
Style Guide: style-guide--technical-documentation-for-technologists v0.2.0

## Abstract

This document describes `osat-fluent-myrepos-tool`, the OSAT Fluent manager for [myrepos](https://myrepos.branchable.com/), Joey Hess's `mr` command for managing many version control repositories at once. The tool itself is close to Archetype 5, self-contained scripts with no build step, but its upstream publishes no release artifacts, which forces a git-based acquisition recorded here as a deviation with rationale.

## What this manager does

`install-myrepos.py` owns the full installation lifecycle: acquire, place, activate, switch, inspect, remove. It is a manager, not an installer. The artifact is the myrepos source tree, containing the `mr` and `webcheckout` Perl scripts, the `lib/` include files (git-annex, vcsh, stow, unison, and others), man pages generated at install time, and a `PROVENANCE` file recording exactly where the artifact came from.

## Filesystem layout

```text
~/.local/share/myrepos-tool/
  1.20180726/               installed artifact: mr, webcheckout, lib/, man/, PROVENANCE
  1.20241001/               versions sit side by side

~/.config/myrepos-tool/
  myrepos-tool.env          MYREPOS_TOOL_ROOT="$HOME/.local/share/myrepos-tool/1.20180726"

~/.local/bin/
  mr                        generated wrapper
  webcheckout               generated wrapper
```

All manager-owned paths carry the `myrepos-tool` management identifier and are owner-only, per the collection's ownership convention and least-privilege rule. The tool's own configuration, `~/.mrconfig` and any chained `.mrconfig` files in repositories, is never touched by this manager; it is owned by `mr register` and the operator.

## Acquisition is git, a recorded deviation

The collection prefers acquiring release tarballs at pinned tags. myrepos makes that impossible: upstream left GitHub in 2017, the canonical repository is served from branchable, no release tarballs exist, and versioning is Debian-native date style (`1.20180726`) rather than semver tags. Acquisition is therefore a shallow `git clone` of the upstream at an optional ref, with the version identity read from the first line of `debian/changelog`.

Three mitigations keep the artifact honest. The `.git` directory is stripped after cloning, so the installed artifact carries no git dependency. A `PROVENANCE` file records the upstream URL, the requested ref, and the exact commit hash, restoring the pinning a tarball would have given. And requiring git on the installing machine is a null cost for this particular tool: `mr` exists to manage version control checkouts, so a machine installing it has git by definition.

The default upstream is the canonical `git://myrepos.branchable.com/`, overridable with `--upstream` for mirrors. Note that the `git://` protocol is unauthenticated and unencrypted; whether an https mirror should become the default is an open ROADMAP item.

## Runtime is system Perl, unmanaged

`mr` is deliberately self-contained, depending on nothing beyond basic Perl, so unlike managers whose tools need a managed environment, this one manages no runtime. It warns if `perl` is absent at install time and otherwise stays out of the way. `webcheckout` additionally requires the `LWP::Simple` and `HTML::Parser` Perl modules from CPAN or the system package manager; the manager installs the script and its wrapper regardless and leaves those modules to the operator, since managing CPAN would exceed this manager's ownership.

## The exported root variable

Generated wrappers source the env file and additionally export `MYREPOS_TOOL_ROOT` before executing the tool. Because `mr` runs every configured action in a child shell, the exported variable is visible inside `.mrconfig` actions and includes. This gives operator configuration a version-stable path to the shipped libraries:

```ini
include = cat "$MYREPOS_TOOL_ROOT/lib/git-annex"
```

The include survives version switches without editing, which a hardcoded path would not.

## Platform support

Linux and macOS. Standard Windows is not supported and cannot be: `mr` requires Perl and executes every configured action through a POSIX shell, neither of which standard Windows provides. Under WSL this manager works as on Linux. This is a property of the tool rather than a gap in the manager, so no Windows work is planned.

## Usage

```bash
python3 install-myrepos.py --install                 # install upstream HEAD
python3 install-myrepos.py --install 1.20180726      # install a specific ref
python3 install-myrepos.py --upstream URL --install  # install from a mirror
python3 install-myrepos.py --switch 1.20180726       # activate an installed version
python3 install-myrepos.py --status                  # show installed and active versions
python3 install-myrepos.py --remove 1.20180726       # remove a non-active version
python3 install-myrepos.py --version                 # show this manager's version
```

`--install` refuses to overwrite an existing installation and warns when `~/.local/bin` is not on `PATH`. `--remove` refuses to remove the active version; switch first.

## License

This document, *osat-fluent-myrepos-tool*, by **Christopher Steel**, with AI assistance from **Claude (Anthropic)**, is licensed under the [GNU General Public License v3.0 or later](https://www.gnu.org/licenses/gpl-3.0.html).

## Changelog

| Version | Status | Notes |
|---------|--------|-------|
| 0.1.0 | Draft | Initial scaffold: manager with install, switch, status, remove lifecycle, git acquisition with PROVENANCE recording, exported MYREPOS_TOOL_ROOT, validated against the GitHub mirror |
