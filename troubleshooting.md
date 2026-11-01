# Troubleshooting

Gron fails in a small number of ways,
and its help screen numbers them. This
chapter is organised by what you saw:
the message, the exit code, and the
fix. Every failure here was produced
in the lab on purpose (t04); the
codes are the ones `--help` prints.

## "failed to form statements" — exit 3

```text
$ echo '{"temp_c": 21.4' | ./gron
failed to form statements: unexpected EOF
(exit 3)
```

Exit 3 is the flatten side saying the
input is not whole JSON. Gron names
the parse problem in plain terms —
`unexpected EOF` for a truncated
document (an interrupted download, a
log cut mid-line), `invalid character
'o' in literal null` for text that was
never JSON at all. Empty input is the
same code with the message `EOF`: a
file that exists but holds nothing.
Fix the input, not the tool: check
where the JSON comes from, and
remember that "exit 3" always means
the *document* was wrong.

## "Failed to open file" — exit 1

```text
$ ./gron missing-file.json
open missing-file.json: no such file or directory
(exit 1)
```

The message is the operating system's
own. Wrong path, wrong directory, or
a file a previous pipeline step was
supposed to create and didn't. Because
gron is usually one link in a pipe,
check the step *before* it first.

## "ungron failed" — exit 5

```text
$ echo 'json.x = ;' | ./gron -u
ungron failed for `json.x = ;`: invalid value ``
(exit 5)
```

Exit 5 is the rebuild side rejecting a
statement. Causes, in order of
likelihood: a grep or sed step altered
a line's shape (a substitution that
ate a quote or a semicolon); the input
was never statements at all (JSON fed
to `-u` by mistake — it fails on the
first line, complaining about an
empty statement); or a hand-written
statement has a grammar slip. Ungron
names the offending line when it can.
Read that line against chapter 2's
grammar — path, `=`, JSON-spelled
value, semicolon — and the slip is
usually visible. When a long statement
stream fails, use the semicolon as a
stencil: `grep -nv ';$' file.gron`
lists every line that does not end
like a statement, which is where
editing accidents collect.

## Only the first line of my file appears

The file is JSON lines — one document
per line — and gron was not told.
Without `--stream`, gron reads one
document (the first), succeeds, and
stops, exit 0, no warning (chapter 8).
The fix is the flag; the habit that
prevents it is checking an unfamiliar
file's shape before the pipeline, not
after the answer looks thin.

## Grep matches too much — or nothing

Too much: patterns are regular
expressions. `grep catalogue[1]`
treats `[1]` as a character class and
matches `catalogue0`, `catalogue1`…
Escape the brackets (`catalogue\[1\]`)
or use `grep -F` for literal text.
The dot in `author.name` matches any
character — harmless until a document
grows an `authorXname`. Anchor values
with `= …;` as chapter 5 teaches.

Nothing: check the sorter's spelling
of the path against the document's
real keys (a key with a space prints
bracketed — `json["opening hours"]`,
never dotted), and check the case of
both pattern and keys. And check you
flattened the document you meant to:
exit code 0 on the wrong file is still
exit 0.

## My extract has nulls I didn't select

Not a failure — array gaps, chapter 6.
Selecting `catalogue[1]` alone rebuilds
`[null, {…}]`, because JSON arrays have
no holes. Select `[0]` too, select the
whole array, or compact the extract
with jq afterwards.

## The rebuild reordered my keys

Also not a failure — the sorter,
chapters 2 and 12. Ungron preserves
data, not object key order. If a
downstream consumer truly depends on
key order, that consumer is depending
on something JSON does not guarantee,
and gron is the wrong editor for that
job; jq or a text editor will respect
the file as written.

## `gron --version` says "dev"

It does (chapter 3). The release
binary was built without its version
stamp. Confirm a binary by where it
came from — the project's releases
page, the archive's checksum — not by
asking it. If two grons disagree in
behaviour, replace both with the
release you meant to have.
