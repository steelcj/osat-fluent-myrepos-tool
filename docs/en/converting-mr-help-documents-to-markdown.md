# Converting mr help documents to markdown

 `perldoc` outputs **POD** (Plain Old Documentation), the cleanest approach is to convert the POD 

## Option 1 — Use `pod2markdown` (best if available)

Install:

```bash
sudo apt install libpod-markdown-perl
```

Then:

```bash
pod2markdown ~/.local/bin/mr > mr.md
```

or:

```bash
pod2markdown $(which mr) > mr.md
```

You get clean Markdown suitable for a web document.

------

## Option 2 — Use Perl's POD converter directly

The Perl module `Pod::Markdown` can be used directly:

```bash
sudo apt install libpod-markdown-perl
```

Then:

```bash
perl -MPod::Markdown -e '
my $p = Pod::Markdown->new;
$p->parse_from_file($ARGV[0]);
print $p->as_markdown;
' ~/.local/bin/mr > mr.md
```

------

## Option 3 — Use `pandoc`

If you already have Pandoc installed:

```bash
perldoc -u ~/.local/bin/mr | pandoc -f pod -t markdown -o mr.md
```

Explanation:

- `perldoc -u` outputs **raw POD source**
- `pandoc` converts POD → Markdown

This is often the most flexible option.

------

## Option 4 — Extract only the documentation section

If you want the actual embedded documentation:

```bash
perldoc -u ~/.local/bin/mr > mr.pod
```

Then:

```bash
pandoc mr.pod -f pod -t gfm -o mr.md
```

`gfm` gives GitHub-flavored Markdown, which is usually a good web-ready format.

------

## Recommendation for web-ready markdown workflow

```bash
sudo apt install libpod-markdown-perl
pod2markdown ~/.local/bin/mr docs/tools/myrepos.md
```

Then clean up:

- add YAML front matter
- add your preferred metadata
- move examples into fenced code blocks
- add links
- commit it alongside your documentation repository

A small wrapper script would make this repeatable:

```bash
#!/bin/bash

SOURCE="${1:-$(which mr)}"
DEST="${2:-mr.md}"

pod2markdown "$SOURCE" > "$DEST"
```

Then:

```bash
./pod-to-md.sh ~/.local/bin/mr docs/myrepos.md
```

This would fits nicely with a static publishing workflow.