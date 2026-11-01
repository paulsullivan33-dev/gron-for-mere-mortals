# The Other Modes — Stream, JSON and Values

The default dialect and a grep cover
most days. This chapter covers the
three switches that change gron's
shape for special work: `--stream` for
files holding many JSON documents,
`--json` for programs that want
statements as data, and `--values` for
when the paths have done their job and
only the answers remain. `--no-sort`
gets its honest paragraph at the end.
The runs are t04.

## Stream mode: one JSON per line

Logs and sensor feeds often arrive as
**JSON lines** — one complete document
per line. The lab's `weather.jsonl`
holds three weather-station readings
that way. Feed the file to gron
*without* `--stream` and you get only
the first reading's statements, exit
code 0, no complaint: gron read one
document and stopped, exactly as it
was designed to. The file is not
broken; it is a different shape.

```bash
./gron --stream weather.jsonl
```

Stream mode reads each line as its own
document and flattens them into **one
shared array**:

```text
json = [];
json[0] = {};
json[0].ok = true;
json[0].rh = 43;
json[0].station = "roof-north";
json[0].temp_c = 21.4;
json[1] = {};
...
json[2].temp_c = 22.6;
```

The readings become elements `[0]`,
`[1]`, `[2]`, which makes the whole
file greppable at once —
`--stream … | grep temp_c | gron -v`
lists every temperature in the file
(t05) — and ungron-able back into an
array of readings. The check to
internalise: if a file's first line
alone looks like complete JSON and the
file has many lines, it is JSON lines,
and it wants `--stream`.

## The JSON dialect

`--json` (or `-j`) prints each
statement as a JSON value in its own
right — a two-element array of the
path (as a list of segments) and the
value:

```text
[[],{}]
[["branch"],"Northgate"]
[["catalogue"],[]]
[["catalogue",0,"author","born"],1974]
```

Humans should stay with the default
dialect; this one is for *programs* —
a script that wants to walk statements
without parsing gron's text grammar,
or a pipeline feeding another tool
that speaks JSON natively. It
round-trips through ungron with the
same flag (`gron -u -j`), and it
composes with `--stream`, paths then
starting with the element index, as
t04 shows. Same facts, same order,
same guarantees; only the spelling
changes.

## Values only

Once grep has selected your lines, the
paths have done their work, and often
what you want is just the answers:

```bash
./gron library.json | grep title | ./gron --values
```

```text
The Clockwork Garden
A Field Guide to Clouds
Bread, Salt and Time
```

`--values` (`-v`) prints the right-hand
side of each statement it is fed,
strings without their quotes, one per
line — a plain list, ready for `sort`,
`wc`, a shell loop, or your eyes. It
is the natural last step of a gron
pipeline, and chapter 10's recipes use
it constantly. Note it consumes
*statements*, not JSON: it belongs on
the ungron side of the tool, reading
what grep kept.

## No-sort, honestly

`--no-sort` skips the sorter and
prints statements in document order —
the fixture's `library` first, as
written. The help calls it "(faster)",
and for enormous documents the sort is
real work. For ordinary documents the
lab's advice is to leave sorting on:
stable, alphabetical output is what
makes statement diffs meaningful and
round trips predictable, and the
speed difference at mortal document
sizes is not worth the surprise.
Either way, know which mode produced a
statement file before you diff it
against another.

## Colour, briefly

On a terminal, gron colourises
statements by default (`-c` forces it,
`-m` turns it off). In pipes — which
is where this guide lives — colour is
off automatically, so transcripts and
scripts never see the escape codes.
Nothing in the guide depends on it;
it is there for the interactive
glance.
