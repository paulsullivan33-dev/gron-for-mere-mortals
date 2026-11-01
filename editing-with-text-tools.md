# Editing JSON with Text Tools

By the end of this chapter you can
change a value in a JSON document,
remove a field wherever it appears,
and add a field — without opening the
document in an editor and without
touching a bracket. Every edit happens
to statement lines, in the open, where
you can inspect it before ungron makes
it real. The runs are t06.

## Change a value with sed

The fixture says the library's branch
is Northgate. Move it:

```bash
./gron library.json \
  | sed 's/json.branch = "Northgate"/json.branch = "Southgate"/' \
  | ./gron -u | jq .branch
```

```text
"Southgate"
```

The edit is one substitution on one
visible line. Compare the alternative:
opening the JSON, finding the field,
keeping the comma rules straight.
Here, the risk profile is different
and smaller — if the pattern matches
nothing, nothing changes, and the
statement stream can be inspected
*before* the ungron step whenever you
want to see exactly what will be
rebuilt. (The `jq` at the end is only
reading the one value back for
display; chapter 9 gives jq its own
say.)

## Remove a field by removing its lines

Deletion is selection with the sign
flipped. `grep -v` keeps every
statement *except* the matches:

```bash
./gron library.json | grep -v rating | ./gron -u \
  | jq '.catalogue[0] | has("rating")'
```

```text
false
```

Every `rating` is gone — the real one
and the `null` alike — because the
lines are gone. The same pattern with
`events` removes the empty events list
entirely (`has("events")` comes back
`false`). One caution, and it is the
chapter's main one: patterns match
substrings. `grep -v rating` would
also remove a field called
`rating_count` or a title containing
the word. Anchor to the path segment —
`grep -v "\.rating ="` — when the
document is bigger than a fixture and
the blast radius matters.

## Add a field by writing a statement

Statements are just text, so a new
fact is a new line. You can append one
with sed, or write statements from
scratch and ungron them alone:

```bash
printf 'json.added = "by hand";\n' | ./gron -u
```

```json
{ "added": "by hand" }
```

And inserted mid-stream, a new
statement takes its place in the
rebuilt document like a native:

```bash
./gron library.json \
  | sed 's/json.founded = 1962;/json.founded = 1962;\njson.renovated = 2019;/' \
  | ./gron -u | jq '{founded, renovated}'
```

```json
{ "founded": 1962, "renovated": 2019 }
```

This is also the honest way to think
about what a statement file *is*: a
document in line form, editable by
anything that edits lines — including
a text editor, a diff, or a code
review.

## Odd keys behave

Real documents have keys that are not
plain words. Gron quotes them in
bracket form, and everything in this
chapter still works (t06):

```text
json = {};
json.café = true;
json["123"] = "numeric key";
json["a.b"] = 1;
json["opening hours"] = "9 to 5";
```

Note `json["a.b"]`: the dot is *inside*
the quoted key, so the path stays
unambiguous — `a` the object containing
`b` would print as `json.a.b`, a
different line entirely. Grep for the
bracketed form, edit it, ungron it:
the round trip restores the exact key.
Even `café` flattens in dot form and
rebuilds correctly. The grammar has no
opinion about your keys; it just
refuses to let two different
structures print as the same line.
