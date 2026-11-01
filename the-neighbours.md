# The Neighbours

Gron is one answer to "JSON is hard to
search". It is not the only tool on the
street, and this chapter is honest
about the others — what each does that
gron doesn't, and the narrower thing
gron does that they don't. These tools
are characterised from their
documentation and this series' other
guides, not benchmarked in this lab;
`criteria.md` keeps that ledger.

## jq — the processor

The neighbour this guide keeps meeting
(chapter 9). Jq is a complete language
for JSON: filters, comparisons,
arithmetic, reshaping, its own
functions. Anything gron “finds”, jq
can also find, usually with more
precision and always with more to
learn first. The trade is exactly
that: jq asks you to learn jq, while
gron asks you to already know grep.
Most people who work with JSON daily
end up with both, using gron for the
first look and jq for the real
transformation — which is why the
series gives jq its own full guide
and this one the border between them.

## fx and jless — the viewers

Where gron prints text for pipelines,
the viewers draw a screen for a human:
**fx** and **jless** open a document
as a collapsible tree you can fold,
search interactively, and read at
human pace. For *exploring* one
document on a terminal, a viewer is
often the kindest tool. What it does
not give you is a pipeline: the
exploration stays on the screen, and
turning "I found it" into "now process
it" means starting over with another
tool. Gron's statements are the
opposite bargain — nothing to look at,
everything to pipe.

## Python's json.tool — the formatter

`python3 -m json.tool` pretty-prints
JSON and rejects what does not parse.
It is on almost every machine already,
and as a *formatter and first sanity
check* it is perfectly good. It does
not search, select, or reshape;
pretty-printing makes JSON readable,
not greppable — the values still sit
on context-free lines. Gron's flatten
is the step beyond formatting.

## yq — the YAML cousin

The yq guide in this series covers
jq's sibling for YAML. The relevance
here is the border: gron speaks JSON
only. A YAML document must become JSON
first (yq can do that conversion), and
a JSON document that must *stay* YAML
belongs to yq entirely. Teams drowning
in Kubernetes-shaped YAML will live in
yq; teams drowning in API-shaped JSON
will live here and in jq.

## grep itself — the zeroth neighbour

Worth naming: plain grep on the raw
document, no gron at all. For a
document you *know* is printed one
field per line by a tool you control,
raw grep can be enough — until the
producer changes its formatting, or
minifies, or nests deeper, and the
patterns silently stop meaning what
they meant. Gron's whole existence is
the refusal to depend on somebody
else's line breaks. If your JSON comes
from exactly one careful source, raw
grep may serve; the moment it comes
from the wild, flatten first.

## The summary

Viewers for looking, json.tool for
formatting, yq for YAML, jq for
thinking, gron for finding with the
tools already in your hands. None of
them makes the others pointless, and
the pipelines in this guide are
deliberately built so a neighbour can
take over at any joint.
