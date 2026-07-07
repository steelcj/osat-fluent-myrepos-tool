# Custom mr Commands for the OSAT Commit Workflow

Version: 0.1.0
Status: Draft
Style Guide: style-guide--technical-documentation-for-technologists v0.2.0

## Abstract

This document defines the custom myrepos commands that automate the batchable commit types from the OSAT commit typology. It provides two commands, `mr wcommit` for work commits and `mr park` for parking commits, with guards enforcing the typology's purity rule. Version bumps of either scope are deliberately excluded from mr; the rationale is recorded below. The implementation in this document was validated against a live mr installation.

## Decisions and rationale

We chose new action names over overriding mr's built-in `commit` action. Overriding `git_commit` would have kept muscle memory, but it would silently change upstream semantics for anyone reading a standard mr reference, and explicitness over convenience is the collection's character. The built-ins remain stock; our contract lives under our own names.

We chose to implement only the batchable types. The typology's batch rule reserves version bumps for per-repository tooling, and defining a bump as an mr action would invite running it from a top-level directory across every repository at once, minting tags nobody reasoned about. The absence of `mr bump` is a designed absence.

We placed the actions in the `[DEFAULT]` section of `~/.mrconfig` so they are available in every registered repository. Actions defined in chained `.mrconfig` files inside repositories require trust entries in `~/.mrtrust`; keeping the definitions in the home config avoids that ceremony while the definitions are stable.

## The commands

Add the following to the `[DEFAULT]` section of `~/.mrconfig`. Continuation lines are indented with a tab, per mrconfig syntax. Actions run under `set -e` with arguments available in `$@`.

```ini
[DEFAULT]
# Work commit: batch-safe. Refuses when a version bump of either scope is in flight.
git_wcommit =
	if git status --porcelain | grep -qE '[ /]VERSION$'; then
		echo "wcommit refused in $MR_REPO: VERSION is modified, a repo bump is in flight" >&2
		exit 1
	fi
	if git status --porcelain | grep -E '^.?[MRD]' | grep -qE -- '-v[0-9]+-[0-9]+-[0-9]+\.md'; then
		echo "wcommit refused in $MR_REPO: a versioned document changed, use a document bump commit" >&2
		exit 1
	fi
	if [ -z "$(git status --porcelain)" ]; then
		echo "wcommit: nothing to commit"
		exit 0
	fi
	git add .
	git commit -m "${1:?usage: mr wcommit '<subject>'}" -m "$(git status)"

# Parking commit: records a deferred decision in ROADMAP.md.
git_park =
	test -n "$(git status --porcelain -- ROADMAP.md)" || { echo "park refused in $MR_REPO: ROADMAP.md unchanged" >&2; exit 1; }
	git add .
	git commit -m "park: ${1:?usage: mr park '<what was parked>'}" -m "$(git status)"
```

## How the guards enforce the typology

The first `wcommit` guard matches a modified `VERSION` file at the repository root or any depth. The pattern `[ /]VERSION$` anchors on the character before the filename in `git status --porcelain` output, a space for root-level files and a slash for nested ones, so a bare line-start anchor would miss both.

The second guard matches modified, renamed, or deleted files whose names carry the pre-release version suffix (`-v0-1-0.md`), the signal of a document-scope bump in flight. Untracked files are excluded deliberately: a new version-suffixed document is an initial draft, which is work.

The `park` guard requires `ROADMAP.md` to be among the changes, since a parking commit that parks nothing is a mislabeled work commit.

Both commands reuse the `git status` output as the commit body, taken after `git add .` so the body lists the staged changes, matching the commit and versioning workflow contract.

## Usage

```bash
mr wcommit 'describe the change'      # from the top level: batch across all registered repos
cd ~/src/some-repo
mr wcommit 'describe the change'      # inside a repo: acts on that repo only
mr park 'docs repo decision deferred to archive tier'
```

Pushing remains a separate act. mr's built-in `mr push` batches plain `git push` across repositories, which is the workflow's subsequent-push contract, so no custom push command is needed. First pushes of new branches and all tag pushes are per-repository acts outside mr.

## Validation record

The implementation above was exercised against a live mr installation with the following matrix: a normal work commit landing with the status body, refusal on a modified tracked `VERSION`, refusal on a modified version-suffixed document, acceptance of a new untracked version-suffixed document, park refusal without a `ROADMAP.md` change, park success with one, and a clean no-op on an unchanged repository. All cases behaved as specified.

## License

This document, *Custom mr Commands for the OSAT Commit Workflow*, by **Christopher Steel**, with AI assistance from **Claude (Anthropic)**, is licensed under the [GNU General Public License v3.0 or later](https://www.gnu.org/licenses/gpl-3.0.html).

## Changelog

| Version | Status | Notes |
|---------|--------|-------|
| 0.1.0 | Draft | Initial draft: wcommit and park actions with purity guards, decisions on naming and the designed absence of mr bump, validation record |
