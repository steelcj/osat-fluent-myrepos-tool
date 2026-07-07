# Making Commits, A User Guide

Version: 0.1.0
Status: Draft
Style Guide: style-guide--plain-language-for-general-audiences v0.2.0

## Abstract

This guide shows you how to make each kind of commit used in OSAT repositories. There are four kinds, and one of them comes in two sizes. Each section gives you the exact commands to run. A summary table at the end helps you pick the right kind at a glance.

## Before you start

A commit is a saved snapshot of your changes, with a message that says what changed. This guide assumes you have git installed and a repository to work in. Some commands use `mr`, a tool that can run the same command in many repositories at once. The mr commands are optional. Every kind of commit can be made with plain git.

One rule matters more than all the others. A commit should be one kind only. Never mix a version change into an everyday commit. The sections below show you how to keep them apart.

## The kinds of commits

| Kind | What it does | How often | Safe to batch with mr? |
|------|--------------|-----------|------------------------|
| Work commit | Saves everyday changes to content, code, or docs | Often | Yes |
| Version bump, repo | Marks a new version of the whole repository, with a tag | Rarely | No |
| Version bump, document | Marks a new version of one document | Sometimes | No |
| Initial commit | The first commit of a brand new repository | Once per repo | No |
| Parking commit | Records a decision you are putting off, in ROADMAP.md | Sometimes | Yes |

"Safe to batch" means you can run it across many repositories in one command. Version bumps need your judgment each time, so they are always done one repository at a time.

## Work commit

Use this for everyday changes. The commit message has two parts. The subject line says what changed. The body is the output of `git status`, pasted in as it is.

```bash
git add .
git status
git commit
git push
```

Write the subject line yourself. Copy the `git status` output into the body. Do not include any `VERSION` file changes or changelog edits. Those belong to a version bump.

With mr, one command does the add, the message, and the body for you:

```bash
mr wcommit 'describe the change'
mr push
```

The `mr wcommit` command will refuse to run if it sees a version bump in progress. That protection is the point of using it.

## Version bump, repo scope

Use this when the whole repository is ready for a new version number. Do your work commits first. The bump commit should contain only the version change and the changelog entry.

```bash
python3 bump-version.py patch
```

Use `patch` for small fixes, `minor` for new content or features, and `major` for big changes. Then open `docs/en/README.md` and add a changelog row that says what this version contains.

```bash
git add .
git status
git commit
git tag -a v0.1.1 -m "version 0.1.1"
git push
git push origin v0.1.1
```

The tag push is a one-time act for each version. Never batch this kind of commit.

## Version bump, document scope

Use this when one document changes enough to need a new version, but the repository itself does not. Unlike a repo bump, the content change and the version change go in the same commit. That is because a document's version line, its date, and its changelog row must always change together.

Edit the document. Update its `Version:` line. Add a row to its changelog table. If the document is still at major version zero, rename the file to match:

```bash
git mv my-guide-v0-1-0.md my-guide-v0-2-0.md
git add .
git status
git commit
git push
```

Give the commit a subject like `my-guide: v0.1.0 -> v0.2.0`. Bump one document per commit. Two documents means two commits. Document bumps never get a tag. Tags belong to the repository, not to documents.

## Initial commit

Use this once, when a new repository is born. It contains everything in the scaffold.

```bash
git init
git add .
git status
git commit
git push -u origin main
git tag -a v0.1.0 -m "version 0.1.0"
git push origin v0.1.0
```

The `-u` flag is only needed on this first push. After this, plain `git push` is enough.

## Parking commit

Use this when you are putting off a decision or a piece of work. Add an entry to `ROADMAP.md` that says what you are deferring and why. This way the repository remembers, even if you do not.

```bash
git add .
git status
git commit
git push
```

Give the subject a `park:` prefix, like `park: docs repo decision deferred to archive tier`. With mr:

```bash
mr park 'docs repo decision deferred to archive tier'
mr push
```

The `mr park` command refuses to run unless `ROADMAP.md` actually changed. A parking commit that parks nothing is just a mislabeled work commit.

## Rules to remember

A commit is one kind only. Work commits never touch version files. Version bumps never carry unrelated work. Tags belong only to repo bumps and initial commits. Bumps are never batched. When in doubt, make two commits instead of one.

## License

This document, *Making Commits, A User Guide*, by **Christopher Steel**, with AI assistance from **Claude (Anthropic)**, is licensed under the [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/).

![CC License](https://mirrors.creativecommons.org/presskit/buttons/88x31/svg/by.svg)

## Changelog

| Version | Status | Notes |
|---------|--------|-------|
| 0.1.0 | Draft | Initial draft: five command sequences, summary table, rules to remember |
