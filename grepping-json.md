# Grepping JSON

This is the chapter gron exists for.
By the end of it you can answer the
three shapes of question people
actually ask a JSON document — *where
does X live*, *which things have value
Y*, and *show me everything about Z* —
using nothing but grep on flattened
statements. The check throughout is
the fixture: every answer below can be
verified against `library.json` by
eye (the runs are t02).

## Search by path

Because every line carries its full
path, a field name is a search key that
cannot lie about which object it came
from:

```bash
./gron library.json | grep title
```

```text
json.catalogue[0].title = "The Clockwork Garden";
json.catalogue[1].title = "A Field Guide to Clouds";
json.catalogue[2].title = "Bread, Salt and Time";
```

Compare that with grepping the raw
file, where a title line might sit
next to any book's other fields — or,
in a compressed document, on the same
line as all of them. Here, the match
*is* the answer, path included. The
same move works at any depth:

```bash
./gron library.json | grep author.name
```

returns the three authors, each line
proving which book it belongs to.

## Search by value

The right side of a statement is as
searchable as the left. Values have a
strict spelling — strings quoted,
numbers bare, the semicolon anchoring
the end — so patterns can be precise:

```bash
./gron library.json | grep "on_loan = 2"
```

```text
json.catalogue[0].on_loan = 2;
json.catalogue[1].on_loan = 2;
```

That pattern cannot match `on_loan = 21`
(the space and the exact digits are in
the pattern, and the `;` is one step
away if you want it: `"on_loan = 2;"`).
Searching for absence is just as easy,
and genuinely useful in real documents:

```bash
./gron library.json | grep null
```

finds both nulls in the fixture — an
unrated book and the Sunday hours —
each with the path that explains it.

## Search by both, and by shape

Path and value patterns combine in one
grep, or in two greps piped. Case
folds with `-i` when you are hunting
content rather than structure:
`grep -i cloud` finds the one title
mentioning clouds. Structure itself is
a pattern too. Array positions are
text: `grep "tags\[1\]"` selects every
*second* tag (the brackets need
escaping, because grep reads them as
pattern syntax — chapter 13 returns to
this). And counting is free:

```bash
./gron library.json | grep -c "json.catalogue\["
```

prints `34` — every statement under
the catalogue, containers and leaves
together. Counts like that are how you
inventory a document you did not
write: how many objects, how many
ratings, how many of anything.

## The discipline

Two habits keep grep honest on
statements. First, **anchor values**:
prefer `"= 2;"` over `"2"` — a bare
digit matches years, counts, ratings
and index numbers alike, while the
anchored form names one fact. Second,
**remember grep sees text, not
meaning**: `grep rating` returns the
book whose rating is `null` along with
the rated ones, because the *line*
matches. Gron has not misunderstood;
it has no opinions about your pattern.
When a question needs actual
understanding — comparing two fields,
say — that is the border chapter 9
draws, and jq is waiting on the other
side of it.

What you have, though, is already most
of daily JSON work: find every
occurrence of a field, every holder of
a value, every null, every second tag —
each answer arriving with its path
attached, ready to be read, counted,
or, as the next chapter shows, fed
back into gron to become a document
again.
