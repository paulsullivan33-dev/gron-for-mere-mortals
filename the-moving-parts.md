# The Moving Parts

Gron is one small program, but it has
two directions and a handful of
options, and a few minutes spent naming
the parts now will make every later
chapter a matter of recognition. By the
end of this chapter you can name what a
statement is made of, what the sorter
does to your keys, and which half of
the tool you are holding at any moment.

## The flattener

The front half reads JSON — from a
file, from standard input, or from a
URL (that last path is
documented-only in this guide; see
`criteria.md`) — parses it properly,
and walks the whole structure. Every
time it reaches a value that cannot be
broken down further — a string, a
number, a true/false, a null — it
writes one statement. On the way down
it also writes a statement for every
container it opens: `json = {};` for
the root object, `json.catalogue = [];`
for an array, and so on. Those container
statements matter: they are how ungron
later knows an empty object from a
missing one.

## The path syntax

A statement's left side is a **path**.
It starts with the fixed name `json`
(the root of your document — a name,
not a variable you chose), then:

- `.key` for each object key that looks
  like a plain word — letters, digits,
  underscores, not starting with a
  digit. `json.hours.weekday`.
- `[n]` for each array position,
  counting from 0. `json.catalogue[2]`.
- `["key"]` for any key that is *not* a
  plain word — keys with spaces, dots,
  digits first, and the like:
  `json["opening hours"]`, `json["a.b"]`,
  `json["123"]`. Chapter 7's transcript
  shows all three, plus a key with an
  accented character, which gron happily
  prints in dot form: `json.café`.

The right side is the value, written in
JSON's own spelling: strings in double
quotes with escapes where needed,
numbers bare, `true`, `false`, `null`.
Every statement ends with a semicolon.
That strict, boring uniformity is the
point: it is what makes the output safe
for grep, and what lets ungron parse
the lines back without guessing.

## The sorter

Unless you say `--no-sort`, gron sorts
its statements before printing. Keys
come out in alphabetical order, arrays
in index order, regardless of the order
in the source document. The lab's
fixture lists `library` first and
`branch` second; gron prints `branch`
first (t01). Sorting is a feature —
output is stable and diff-friendly —
with a cost chapter 6 makes concrete:
ungron rebuilds objects in the sorted
order, so a round trip preserves your
*data* exactly and your *key order*
not at all. JSON itself does not
consider key order meaningful, but your
eyes, reading a rebuilt file, will
notice.

## Ungron

The back half, reached with `-u` or
`--ungron`, reads statements — from a
file or a pipe — parses each one, and
assembles the document they describe.
It is forgiving about structure in one
specific way chapter 6 demonstrates:
feed it only the lines grep selected,
and it builds the partial document
those lines describe, inventing the
intermediate objects and arrays it
needs. It is strict about grammar: a
line that is not a well-formed
statement stops it with exit code 5.

## The values lens

`--values` (or `-v`) is a small extra
on the ungron side: instead of
rebuilding JSON, it prints just the
right-hand values of the statements it
is fed, one per line, strings unquoted.
It is the quick way to turn "grep found
the lines" into "here are the names"
without rebuilding anything.

## The two statement dialects

Everything above describes the default
text dialect. `--json` (or `-j`)
switches gron to a machine dialect:
each statement becomes a JSON array of
`[path, value]`, like
`[["catalogue",0,"author","born"],1974]`.
Same facts, same order, no semicolons —
a form meant for programs to consume,
including ungron itself (`-u -j` reads
it back). Chapter 8 shows both
dialects side by side.

## What is not in the box

There is no config file, no plugin
system, no environment variable that
changes behaviour, and no state kept
between runs. Gron is a filter in the
old Unix sense: data in, data out, the
same every time. Everything it knows is
in the input and the flags, which the
full list of fits on one help screen —
chapter 3 reproduces it from the lab's
own run.
