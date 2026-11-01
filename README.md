# Gron for Mere Mortals

JSON is the shape data arrives in now —
from web APIs, from config files, from
programs talking to each other. It is
also awkward to search. The tools you
already know for text, **grep** above
all, work line by line, and JSON does
not promise you one fact per line: a
value you want might sit three brackets
deep on a line with forty other values,
or alone on a line that says nothing
about where it belongs.

**Gron** fixes that with one trick. It
flattens JSON into **statements** — one
line per value, each line naming the
full path to that value, in the style of
a programming language:

```text
json.catalogue[1].author.name = "Tomas Reid";
```

Now grep works. So do sed, sort, cut,
and every other line tool you own,
because the JSON has become ordinary
text with a strict shape. And when you
have found — or edited — the lines you
want, gron's other half, **ungron**,
rebuilds JSON from the statements. Pick
just the title lines out of a catalogue
and ungron hands you back a smaller,
valid JSON document containing only the
titles.

This guide explains the trick, installs
the tool, and then proves the claims
that matter. The lab behind it ran the
real thing: **gron 0.7.1**, the official
release binary, against an invented
library catalogue and a set of weather
station readings. It flattened them,
grepped them, rebuilt them, and checked
the rebuild two ways — a plain byte
comparison (which fails, honestly, and
chapter 6 explains why) and a canonical
comparison through jq (which passes:
the rebuilt document holds exactly the
same data). It edited values with sed,
deleted keys by deleting lines, wrote
statements by hand, fed gron broken
JSON and missing files and recorded the
exact error messages and exit codes, and
ran gron side by side with **jq** —
the series' JSON processor — to show
where each tool earns its place.

What the lab could not do, it did not
fake. Gron can fetch JSON from a URL;
the lab had no server assigned to it, so
that path is taught from the
documentation and labelled as such.
`criteria.md` keeps the full ledger of
what was run and what was only read.

## What this guide covers

- [**What gron is**](what-gron-is.md) —
  the mental model: JSON in, one
  statement per value out, and back
  again — and why that makes grep a
  JSON tool
- [**The moving parts**](the-moving-parts.md) —
  the flattener, the path syntax, the
  sorter, ungron, and the statement
  grammar that ties them together
- [**Installing gron**](installing-gron.md) —
  the official release download, and
  the version check with its one
  honest surprise
- [**Your first flattening**](your-first-flattening.md) —
  a nested document turned into 45
  statements, and how to read them
- [**Grepping JSON**](grepping-json.md) —
  the core skill: finding values by
  path, by content, and by both at once
- [**Ungron — the way back**](ungron-the-way-back.md) —
  rebuilding JSON from statements, the
  round trip proved, and what partial
  extracts rebuild into
- [**Editing JSON with text tools**](editing-with-text-tools.md) —
  changing values with sed, removing
  keys by removing lines, and writing
  statements by hand
- [**The other modes**](the-other-modes.md) —
  stream mode for one-JSON-per-line
  files, the `--json` statement form
  for programs, `--values`, and
  `--no-sort`
- [**Gron and jq**](gron-and-jq.md) —
  the partnership: which questions each
  tool answers best, and pipelines
  that use both
- [**Recipes**](recipes.md) — pull every
  name out of a document, strip a field
  everywhere it appears, diff two API
  answers by meaning, and inventory
  what a document contains
- [**The neighbours**](the-neighbours.md) —
  jq the processor, fx and jless the
  viewers, Python's json.tool, and
  yq's YAML cousinhood: what each does
  that gron doesn't, and vice versa
- [**Limits**](limits.md) — no queries,
  no arithmetic, sorted order imposed
  on your keys, arrays that remember
  gaps as nulls, and a version string
  that says "dev"
- [**Troubleshooting**](troubleshooting.md) —
  exit codes 1, 3 and 5 decoded, the
  JSON-lines file that only shows its
  first line, grep patterns that match
  too much, and statements ungron
  refuses
- [**Glossary**](glossary.md) — the
  terms, collected in one place

## What it doesn't cover

Querying JSON with a real language —
conditions, arithmetic, reshaping.
That is jq's job, and the jq guide in
this series teaches it properly; this
guide shows gron's side and the border
between them. JSON Schema, validation
beyond "does it parse", and streaming
gigabytes are likewise pointed at, not
taught. Gron also reads YAML's cousin
only by accident — it speaks JSON, and
chapter 11 points at yq for the rest.

## How to use this guide

Read chapter 1 for the model and
chapter 2 before installing anything —
the statement grammar is small, and
knowing it turns every later surprise
into a named behaviour. Chapters 3 and
4 put the tool in your hand; chapter 5
is the skill the tool exists for.
Chapter 6 is the one to read slowly:
the round-trip proof, and the two ways
a rebuild can differ from the original
— order and gaps — are the difference
between trusting ungron and being
surprised by it. The lab's transcripts
live in `gron-lab/transcripts/`; its
fixtures are `gron-lab/library.json`
and `gron-lab/weather.jsonl`.

---

## Part of the series

This guide is one of the [For Mere Mortals](https://paulsullivan33-dev.github.io/mere-mortals/) guides: plain-language guides to the tools programmers and makers use.

Related guides:

- [jq for Mere Mortals](https://paulsullivan33-dev.github.io/jq-for-mere-mortals/)
- [yq for Mere Mortals](https://paulsullivan33-dev.github.io/yq-for-mere-mortals/)
- [Finding Files for Mere Mortals](https://paulsullivan33-dev.github.io/finding-files-for-mere-mortals/)

## License

The writing in this guide is licensed under [CC BY 4.0](https://github.com/paulsullivan33-dev/gron-for-mere-mortals/blob/main/LICENSE). Code examples and lab scripts are licensed under the [MIT License](https://github.com/paulsullivan33-dev/gron-for-mere-mortals/blob/main/LICENSE-CODE).
