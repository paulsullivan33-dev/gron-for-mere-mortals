# Gron and jq

Two JSON tools live in this series,
and this chapter exists so you reach
for the right one first. By the end of
it you can sort any question about a
JSON document into *gron country* or
*jq country*, and build the pipelines
that use both — because the honest
answer for real work is usually both,
each doing the half it is good at. The
side-by-side runs are t05. The jq guide
in this series teaches jq itself; this
chapter only draws the border and
shows the handshakes.

## The one-sentence border

**Gron finds; jq thinks.** Gron
answers questions of *location and
selection* — where is it, what matches
this text, give me those lines — by
turning the document into text your
existing tools can search. Jq answers
questions of *meaning* — comparisons,
arithmetic, reshaping — by evaluating
a small language against the document.
If your question contains a verb like
*find*, *list*, *count* or *remove*,
start with gron. If it contains
*where the two numbers agree*, *total*,
*average* or *turn into*, start with
jq.

## The same questions, both tools

One field of one book:

```bash
jq '.catalogue[1].title' library.json
./gron library.json | grep 'catalogue\[1\].title' | ./gron --values
```

Both print the clouds field guide.
Jq's way is shorter *when you already
know the path*. Gron's way earns its
keep when you do not — when the
question is "title *anywhere*", or
"anything mentioning clouds", where
jq needs a recursive search and gron
needs `grep -i cloud`.

Every author's name:

```bash
jq -r '.catalogue[].author.name' library.json
./gron library.json | grep author.name | ./gron -v
```

Identical output, three names.
Averages, though, are jq's alone:
the mean temperature of the weather
readings is one jq expression
(`add / length` over a slurped array),
while the gron pipeline contributes
the honest half it can — selecting the
temperature lines and listing the
values — and then stops. Gron has no
arithmetic, and chapter 12 does not
let it pretend otherwise.

## Where the border shows

"Which books are fully out on loan?"
needs `on_loan` *compared with*
`copies`, book by book. Jq does it in
one expression with `select`. The
gron-side attempt is instructive:
grepping `on_loan = 2` finds the two
books with two copies out — but
"fully out" is a relationship, not a
value, and rebuilding from those lines
gives partial objects whose other
fields are absent (feeding them to jq
prints `null` where the untouched
fields would be — t05). Gron found
candidates; only jq could judge them.
Knowing which half of a question is
finding and which is judging is the
whole skill this chapter teaches.

## Pipelines that use both

The tools compose in both directions,
and the compositions are the recipes
of daily work:

- **jq reshapes, gron searches.** A jq
  filter produces a new document; gron
  flattens *that* to verify the
  reshaping did what you meant —
  statements are the easiest diff a
  transformation can be checked with.
- **Gron selects, jq reads.** Grep the
  statements, ungron the extract (end
  of chapter 6), then let jq pull
  precisely typed values out of the
  smaller document — as chapter 7's
  sed edit did when it asked jq for
  `.branch`.
- **Either one validates the other.**
  A document both tools parse is a
  document parsed twice by independent
  code — a cheap cross-check the lab
  used for its round-trip proof, where
  jq's canonical form was the referee.

Reach for gron first when the document
is unfamiliar and the question is
*what is in here*; reach for jq first
when the document is familiar and the
question is *what does it mean*. The
rest of the time, the pipe between
them is short and both directions are
open.
