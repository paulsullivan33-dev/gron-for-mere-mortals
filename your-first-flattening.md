# Your First Flattening

By the end of this chapter you have
flattened a real, nested document and
can read the result fluently: which
lines are containers, which are values,
and why the order is not the order you
wrote. The check is a count — the
statements must account for every value
in the document, and you will see how
to make that count yourself.

## The fixture

The lab's document is an invented
library catalogue, `gron-lab/library.json`,
kept small enough to hold in your head
and nested enough to be honest work:
an object holding strings, a number, a
boolean, a list of three book objects —
each with an author object, a tag list,
and a rating that is sometimes `null` —
an hours object, and one empty list.
45 facts and containers in all, as the
run is about to show.

## The flattening

```bash
./gron library.json
```

The first lines (t01):

```text
json = {};
json.branch = "Northgate";
json.catalogue = [];
json.catalogue[0] = {};
json.catalogue[0].author = {};
json.catalogue[0].author.born = 1974;
json.catalogue[0].author.name = "Elena Marsh";
json.catalogue[0].copies = 4;
```

Read them the way chapter 2 promised.
`json = {};` opens the root object.
`json.catalogue = [];` announces the
array *before* its members, and
`json.catalogue[0] = {};` announces the
first book before its fields. Every
container speaks first, then its
contents. A leaf like
`json.catalogue[0].author.born = 1974;`
is the full story of one value: where
it lives, what it is, nothing else on
the line.

Two things to notice in the full
output. First, **the order changed**:
the fixture lists `library`, then
`branch`; gron prints `branch` first,
and within each book `author` before
`copies` before `id` — alphabetical,
always, because of the sorter (chapter
2). Second, **nothing was lost and
nothing summarised**: the `null`
ratings and the closed-on-Sunday `null`
are statements like any other, and the
empty `events` list is there as
`json.events = [];` — present, and
visibly empty.

## The count check

```bash
./gron library.json | wc -l
```

```text
45
```

Forty-five statements. You can audit
that number by hand against the
fixture: 1 root + 4 top-level leaves +
3 hours leaves + 1 hours container +
1 events + 1 catalogue container +
3 books × (1 book container + 1 author
container + 7 leaves) + the tag arrays
(1 + 2, 1 + 2, 1 + 1). The point is not
the arithmetic; it is that flattening
is *accountable*. A transformation you
can count is a transformation you can
trust, and chapter 6 uses the same
instinct — compare after rebuilding —
at document scale.

## Small documents, whole

The extremes behave the way the model
predicts (t01). A bare number is one
statement: `json = 42;`. A bare string,
likewise: `json = "just a string";`. A
two-element array is a container and
two leaves:

```text
json = [];
json[0] = true;
json[1] = false;
```

And a deep, narrow document —
`{"a": {"b": [1, 2, {"c": null}]}}` —
produces a chain of containers ending
in the leaves, the path growing one
segment per level. There is no special
case anywhere in this chapter: nested
or flat, object or scalar, the rule is
one line per node, path on the left,
value on the right. That uniformity is
what the next chapter spends.
