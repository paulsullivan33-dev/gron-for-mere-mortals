# Recipes

Each recipe is a small job done end to
end with pipelines from the earlier
chapters, against the lab's fixtures —
swap in your own document and the
shapes hold. Every step was run in the
lab (t02, t03, t05, t06, t07).

## Pull every name out of a document

Goal: a plain list of the authors,
nothing else.

```bash
./gron library.json | grep author.name | ./gron --values
```

```text
Elena Marsh
Tomas Reid
Priya Nair
```

The pattern generalises: any field,
any depth — `grep '\.name ='` when
names appear at several levels and you
want them all.

## Extract one record as its own document

Goal: the second book, as JSON, to
send or save on its own.

```bash
./gron library.json | grep 'catalogue\[1\]' | ./gron -u
```

You get the book — under a `catalogue`
array whose first element is `null`,
the gap chapter 6 explained. When the
padding is unwanted, extract at object
level instead where you can, or pipe
the extract through jq to compact it.
Know the trade before you automate it.

## Strip a field everywhere it appears

Goal: remove every `rating`, including
the nulls, from a copy of the
document.

```bash
./gron library.json | grep -v '\.rating =' | ./gron -u > clean.json
```

Verify before trusting: flatten the
result and count —
`./gron clean.json | grep -c rating`
should print `0`. The verification is
part of the recipe, not a garnish;
statement counting is how gron work
gets checked.

## Change a fact in one place

Goal: correct the branch name in a
copy, leaving the original untouched.

```bash
./gron library.json \
  | sed 's/json.branch = "Northgate"/json.branch = "Southgate"/' \
  | ./gron -u > moved.json
```

Then `./gron moved.json | grep branch`
shows the new line. Editing a copy and
diffing the statement streams of
original and copy —
`diff <(./gron library.json) <(./gron moved.json)` —
shows exactly one line changed, which
is the strongest review a small edit
can get.

## Inventory an unfamiliar document

Goal: learn what a document you did
not write actually contains, before
asking it anything.

```bash
./gron big.json | wc -l
./gron big.json | grep -c ' = \[\];'
./gron big.json | grep -c 'null;'
./gron big.json | cut -d. -f2 | sort | uniq -c | sort -rn | head
```

Total statements; how many empty
arrays (5 in the fixture — the
catalogue, three tag lists' starts and
events, as containers print before
their members); how many nulls; and
the second path segment ranked by how
many statements hang under it — in the
fixture, the three books at 11, 11 and
9 lines each, which is the document's
centre of gravity made visible. Four
counts and you know a document's size,
its emptiness, and where its weight
sits. This is the first thing the lab
does with any new JSON, and the recipe
most likely to save you an afternoon
(t07 has the fixture's real numbers).

## Diff two answers by meaning

Goal: did the API's answer actually
change, or just its formatting?

```bash
./gron monday.json > monday.gron
./gron tuesday.json > tuesday.gron
diff monday.gron tuesday.gron
```

Because gron sorts, formatting and key
order vanish from the comparison;
every remaining diff line is a real
change, with its full path attached.
A diff that shows one line changed its
value is a fact; a diff that shows
forty lines moved says the *shape*
changed — and says so in the first
screenful.
