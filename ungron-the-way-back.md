# Ungron — The Way Back

Flattening is only half the trick. By
the end of this chapter you can rebuild
JSON from statements, prove the rebuild
faithful — and state precisely the two
ways it is *not* identical to the
original. You can also extract a
partial document by selecting lines,
and predict what ungron does with gaps
in an array. The runs are t03.

## The round trip

```bash
./gron library.json | ./gron --ungron > rebuilt.json
```

(`-u` is the short form; the lab uses
both in this guide.) The rebuilt
document is pretty-printed JSON
holding the catalogue, the hours, the
nulls, the empty list — everything.
Now the proof, done two ways because
the two ways answer different
questions.

**Byte comparison fails.** The SHA-256
hashes of `library.json` and
`rebuilt.json` differ (t03). Of course
they do: the original was hand-written
with its own spacing and key order,
and ungron prints its own tidy layout
with the sorter's alphabetical order.
Byte identity was never on offer.

**Canonical comparison passes.** Run
both documents through a normaliser
that sorts keys and prints uniformly —
jq with `-S` — and diff the results:

```bash
jq -S . library.json > canonical-a.json
jq -S . rebuilt.json > canonical-b.json
diff canonical-a.json canonical-b.json && echo ROUNDTRIP-SEMANTIC-IDENTICAL
```

```text
ROUNDTRIP-SEMANTIC-IDENTICAL
```

No differences: same objects, same
arrays in the same order, same values,
same nulls. That is the guarantee to
carry with you — **ungron preserves
data exactly and presentation not at
all**. Key order inside objects is the
visible casualty; JSON semantics do
not depend on it, but chapter 12 notes
the human cost.

## Extracting with grep

The round trip's real payoff is that
the statements in the middle do not
have to be *all* the statements:

```bash
./gron library.json | grep title | ./gron -u
```

```json
{
  "catalogue": [
    { "title": "The Clockwork Garden" },
    { "title": "A Field Guide to Clouds" },
    { "title": "Bread, Salt and Time" }
  ]
}
```

Three title lines became a valid,
smaller document. Ungron invented the
structure the lines imply — the
catalogue array, the book objects —
without being told. Grepping
`catalogue` instead selects the whole
subtree (plus its container lines,
which grep matches too) and rebuilds
just the catalogue under a root of its
own. Selection *is* extraction: this
is the single most useful pipeline in
the guide.

## Gaps become nulls

One behaviour to learn before it
surprises you. Select only the second
book:

```bash
./gron library.json | grep "catalogue\[1\]" | ./gron -u
```

The result's catalogue is
`[null, { ...book two... }]`. Ungron
knows an element `[1]` exists, arrays
have no holes in JSON, so position
`[0]` is filled with `null`. The data
you selected is exact; the padding is
the price of array honesty. Object
keys have no such effect — a missing
key is simply missing — which is one
reason partial extracts of objects are
cleaner than partial extracts of
arrays. Chapter 13 lists the symptom
("my extract has nulls I didn't
select") with this chapter as the
explanation.

## Ungron as a filter for other tools

Note the shape of every pipeline in
this chapter: gron, then *any text
tools at all*, then ungron. The middle
step in this chapter was grep, but the
contract is just "statement lines in,
statement lines out" — which is why
the next chapter can edit values with
sed and delete keys with `grep -v`,
and why a statement file can be saved,
diffed, version-controlled, and
rebuilt next week. The statements are
not an intermediate format you must
hurry through; they are a place JSON
can live, in text form, for as long as
you find that convenient.
