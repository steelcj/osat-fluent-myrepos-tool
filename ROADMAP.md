# ROADMAP

Version: 0.1.0
Status: Draft

## Near term

- Validate acquisition against the canonical upstream (`git://myrepos.branchable.com/`) from a network that can reach it; this scaffold was validated against the GitHub mirror
- Decide whether `git://` (unauthenticated, unencrypted) is acceptable as the default upstream or whether an https mirror should be preferred, and record the decision
- macOS validation

## Later

- Optional man page installation into `~/.local/share/man/man1/` on install and switch, currently man pages are built into the artifact only
- Feed the git-acquired artifact pattern (PROVENANCE file, upstream version discovery) back into the governance specification alongside the sat-tool archetype write-back
