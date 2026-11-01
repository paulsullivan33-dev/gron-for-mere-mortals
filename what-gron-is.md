# What Gron Is

You have a JSON document — an answer
from a web API, a program's output, a
config file that grew — and you have a
question about it. *Which books are
fully out on loan? What did the third
sensor report? Is there a null hiding
anywhere in this thing?* The document
knows. Getting the answer out is the
annoying part, because JSON's shape
fights the tools you would reach for
first.

**Grep** — the text searcher every
system already has — works one line at
a time. JSON does not care about lines.
One program prints it compressed into a
single line a mile long; another
pretty-prints it across two hundred
lines, with each value on a line of its
own but stripped of context: a line
reading `"rating": null,` does not say
*whose* rating is null. Searching raw
JSON with grep gives you matches you
cannot interpret and misses you cannot
see.

**Gron is the adapter between JSON and
the line tools.** It is a small program
that rewrites a JSON document as a list
of **statements**. A statement is one
line that assigns one value to one
complete **path** — the full route from
the document's root down to that value,
written the way a programmer would
write it:

```text
json.catalogue[1].author.name = "Tomas Reid";
json.catalogue[1].rating = null;
```

Every fact in the document gets exactly
one line, and every line is
self-contained: the path says where the
fact lives, the value says what it is.
That is the whole idea, and it is
enough to change what your old tools
can do:

- `grep` finds facts by path (`author.name`),
  by value (`= 2;`), or by both — chapter 5.
- `sort`, `wc`, `cut` and friends count,
  order and slice facts like any text.
- `sed` edits a value by editing its
  line — chapter 7.
- And gron's reverse gear, **ungron**,
  reads statements back and rebuilds
  JSON from them — chapter 6 — so a
  grep that selects six lines becomes a
  way to *extract a smaller document*,
  not just to look at one.

## A first taste

After installing (chapter 3), flattening
is one line, and the result answers a
question immediately:

```bash
./gron library.json | grep "on_loan = 2"
```

```text
json.catalogue[0].on_loan = 2;
json.catalogue[1].on_loan = 2;
```

Two books have two copies out. The
lab's fixture is an invented library
catalogue, and chapter 4 flattens all
of it: 45 statements from one page of
JSON.

## The ethics line, stated early

Gron reads what it is pointed at. It
has no notion of permission: if a file
or an address contains JSON, gron will
flatten it, including API responses
that carry other people's data or
tokens you were given for one purpose.
The tool is neutral; the pointing is
yours. Flatten your own documents
freely — and think before you flatten,
keep, or pass on a document that holds
someone else's private facts or a
secret key, because gron's statements
preserve every value exactly, secrets
included, in a form that is *easier*
to grep out and copy than the original.
Chapter 12 returns to this.

## What it is not

Gron is not a **query language**. It
cannot compare two values, add numbers,
or reshape a document on purpose — the
comparisons in this guide happen
because grep matched a value you named,
not because gron evaluated anything.
Asking "which books have every copy out"
needs `on_loan` *compared with*
`copies`, and that is jq's territory;
chapter 9 draws the border precisely
and shows the two tools working the
same questions. Gron is not a viewer
either: it prints text for pipelines,
not a screen you browse (chapter 11
meets the viewers). And it is not a
validator: it will tell you JSON is
broken, with a useful message, but
checking a document against a schema is
another tool's job.

## Who it is for

Anyone who already thinks in text
pipelines and keeps meeting JSON at the
end of them. The shell scripter who
wants one field out of an API answer
without learning a query language
first. The troubleshooter staring at a
two-thousand-line config export who
wants to know *what is actually in
here*. The careful editor who would
rather change one visible line and
rebuild than hand-edit nested brackets
and pray. If your questions about JSON
are mostly *where is it* and *what else
looks like this*, gron was built for
exactly those questions.
