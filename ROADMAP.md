# ROADMAP

Version: 0.1.0
Status: Draft

## Near term

### Installer issue

```bash
[MYREPOS-TOOL] Installing myrepos from git://myrepos.branchable.com/
  man page built:   /tmp/myrepos-tool-4l0_h0yv/myrepos/man/man1/mr.1  ✓
  man page built:   /tmp/myrepos-tool-4l0_h0yv/myrepos/man/man1/webcheckout.1  ✓
  artifact placed:  ~/.local/share/myrepos-tool/1.20180727  ✓  (upstream version 1.20180727)
  env file written: ~/.config/myrepos-tool/myrepos-tool.env  ✓
  wrapper written:  ~/.local/bin/mr  ✓
  wrapper written:  ~/.local/bin/webcheckout  ✓

[MYREPOS-TOOL] myrepos 1.20180727 installed and active.
  Verify with:  mr --version
```

```bash
mr --version
Unknown option: version
Usage: mr [options] action [params ...]
(Use mr help for man page.)
```

```bash
mr help
You need to install the perl-doc package to use this program.
```

```bash
sudo apt update
sudo apt install perl-doc
```

- Validate acquisition against the canonical upstream (`git://myrepos.branchable.com/`) from a network that can reach it; this scaffold was validated against the GitHub mirror
- Decide whether `git://` (unauthenticated, unencrypted) is acceptable as the default upstream or whether an https mirror should be preferred, and record the decision
- macOS validation

## Later

- Optional man page installation into `~/.local/share/man/man1/` on install and switch, currently man pages are built into the artifact only
- Feed the git-acquired artifact pattern (PROVENANCE file, upstream version discovery) back into the governance specification alongside the sat-tool archetype write-back
