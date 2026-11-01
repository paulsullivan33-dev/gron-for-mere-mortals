# Limits

A tool this small has edges you can
reach in an afternoon. This chapter
names them all, so none of them
arrives as a surprise mid-pipeline.
Every item here was either observed in
the lab or follows directly from the
help text; `criteria.md` separates
the two.

## It cannot think

Gron does not evaluate. No comparisons
(`on_loan` against `copies`), no
arithmetic, no conditions, no
aggregation, no reshaping beyond
selection. Grep's matches are textual:
`grep rating` returns the null rating
with the real ones. Questions of
meaning belong to jq (chapter 9), and
any pipeline that needs an answer
gron cannot state should be rebuilt
with jq rather than coaxed.

## It imposes sorted order

Statements come out sorted, and
ungron rebuilds objects in that order.
Your key order — which may carry
meaning to humans, to diffs, to other
tools that print objects in file
order — does not survive a round trip.
The data does; the presentation does
not. `--no-sort` keeps document order
in the statements, but ungron still
prints its own layout: gron is not a
byte-preserving editor, and jobs that
need surgical, format-preserving edits
belong to a real editor or to jq with
care.

## Arrays remember gaps as nulls

A partial extract that selects element
`[1]` without `[0]` rebuilds with a
`null` in the gap (chapter 6). There
is no flag to change this; it is what
"the statements describe an array"
means. Extract whole arrays or
object-level subtrees when padding
would corrupt your meaning.

## Text grammar, text traps

Statements are text, so text's traps
apply. Grep patterns are regular
expressions: the `.` in `author.name`
also matches `authorXname`, brackets
must be escaped, and a pattern like
`rating` matches `rating_count` too.
The fixtures in this guide are small
enough to forgive all of that; real
documents demand the anchored patterns
chapter 5 teaches. Likewise, editing
statements with sed edits *text*: a
careless substitution can rewrite a
string value that merely looks like a
path.

## Whole documents, in memory

Gron parses the whole input into
statements (stream mode still builds
one combined array of them). It is a
tool for documents a person might
plausibly open — kilobytes to tens of
megabytes — not a streaming engine for
gigabyte logs or an endless feed;
jq's streaming mode and the log tools
of other guides live on that street.
The lab's fixtures are deliberately
small; nothing in this guide measures
gron at the top of its range, and the
text does not pretend otherwise.

## Small print from the lab

- **The version string says “dev”.**
  The official 0.7.1 binary answers
  `--version` with `gron version dev`
  (chapter 3). You cannot interrogate
  a found gron about its version and
  learn anything.
- **JSON lines need the flag.** A
  JSON-lines file without `--stream`
  silently yields its first document
  only (chapter 8) — exit 0, no
  warning. Silent partial answers are
  the worst kind; check the shape of
  unfamiliar files first.
- **Ungron's errors are terse.** A
  malformed statement earns
  `invalid statement` and exit 5, with
  the offending line named when gron
  can name it — enough to fix the
  line, no more.
- **URLs are someone else's
  computer.** Gron can fetch a URL and
  flatten what returns, errors and
  all; the lab did not run that path
  (no server was assigned to it), and
  chapter 1's ethics note applies with
  full force to anything fetched.
- **The root name is a real key.**
  Gron prints every statement rooted at
  `json`, but ungron accepts any root
  name and treats it as an ordinary top
  key: statements rooted at `data` and
  at `other` ungron into one object
  holding both (t08). The flip side is
  merging two documents' statements —
  both rooted at `json` — merges their
  trees under that one key, later
  statements winning shared paths.
  Rename a root with sed (`s/^json/data/`)
  before merging when the documents
  must stay distinct.
