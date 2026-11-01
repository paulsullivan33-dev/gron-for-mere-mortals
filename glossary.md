# Glossary

**Canonical form** — a document
rewritten by fixed rules (here, jq
sorting keys and printing uniformly)
so two documents can be compared for
*same data* without formatting getting
in the way. Chapter 6's round-trip
referee.

**Container statement** — a statement
whose value is `{}` or `[]`: it
announces that an object or array
exists at that path, before the
statements for its contents.

**Exit code** — the number a program
leaves behind saying how it went.
Gron's are documented in its help:
1 open, 3 form statements, 5 parse
statements, and friends.

**Flattening** — rewriting a nested
document as a list of path-and-value
statements, one per line. Gron's front
half; also this guide's word for the
whole trick.

**JSON lines (JSONL)** — a file
holding one complete JSON document
per line, common for logs and sensor
feeds. Gron reads it with `--stream`.

**Leaf** — a value that cannot be
broken down further: a string, number,
boolean or null. Leaves are the
statements with real data in them.

**Path** — the full route from the
document root to one value, written
as segments: `.key` for plain-word
object keys, `[n]` for array
positions, `["key"]` for every other
key.

**Round trip** — flattening a document
and ungroning the result. Chapter 6
proves it preserves the data exactly
and the presentation not at all.

**Statement** — one line of gron's
output: a path, `=`, a value in JSON's
spelling, and a semicolon.
`json.catalogue[0].copies = 4;` is
one.

**Stream mode** — gron's `--stream`:
each input line treated as its own
JSON document, flattened into one
shared array.

**Ungron** — gron's reverse gear
(`-u` / `--ungron`): reads statements
and rebuilds the JSON document they
describe, inventing the structure the
selected lines imply.

**Values mode** — `--values` (`-v`):
prints just the right-hand values of
the statements fed to it, strings
unquoted, one per line.
