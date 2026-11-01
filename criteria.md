# Writing criteria for this guide

Every "for Mere Mortals" guide is written to the same nine-point
standard, so a reader who trusts one can trust the rest. Here it
is, with the verification notes for this particular guide below.

1. **Fix it first.** Reader outcomes lead, not action items: each
   chapter opens by naming the outcome it delivers, shows a check
   the reader can run to confirm they got it, and a reasonable
   reader finishes able to state what was done, what it means,
   and what to try next.
2. **Plain language.** Short sentences, everyday vocabulary,
   technical terms defined on first use. No slang, and no
   idioms that only make sense in one country — the reader may
   be translating as they go.
3. **No dead ends.** A guide never tells the reader to do
   something it cannot show them how to verify. Every claim a
   reader relies on — an install, a command, an output — is
   something this guide demonstrates or clearly labels
   documented-only below.
4. **Live-verified where it matters.** Procedures are run for
   real in the guide's lab wherever the build machine allows it,
   and the transcripts kept in the repository are the evidence.
   What could not be run is listed honestly below, never
   presented as if it had been.
5. **General audiences.** Nothing in this guide — examples,
   fixtures, names, addresses — is modelled on any particular
   reader or author's real life, network, or organisations.
   The lab's fixtures are an invented library catalogue and
   invented weather-station readings.
6. **Standalone.** A reader needs this guide, its lab, and
   nothing else. Where another guide would genuinely help, it
   is pointed to once, by name, and the chapter still completes
   without it.
7. **Honest about limits.** Chapter 12 names what the tool
   cannot do: no evaluation of any kind (no comparisons,
   arithmetic or reshaping), sorted order imposed on rebuilt
   objects, array gaps rebuilt as nulls, whole documents held
   in memory rather than streamed without limit, a release
   binary whose `--version` says "dev", and JSON-lines input
   that silently yields only its first document without
   `--stream`.
8. **The ethics rule is in the text, early.** Chapter 1 states
   it before any procedure depends on it: gron flattens
   whatever it is pointed at, including API responses holding
   other people's data and secret keys, and its statements
   preserve every value in a form that is easier to grep out
   and copy than the original — so the decision about what may
   be flattened, kept, and passed on belongs to the person
   pointing the tool, and this guide's fixtures contain
   nothing real to leak.
9. **Builds clean.** The site builds with MkDocs in strict mode
   with no warnings, and a fresh reader following the chapters
   in order reaches the stated outcome of each.

## Verification notes for this guide

The build machine is a 2-CPU, 7.7 GB Ubuntu 24.04 sandbox,
shared with other work while the lab ran. Gron **0.7.1** was
installed from the official release archive
`gron-linux-amd64-0.7.1.tgz` (GitHub, tomnomnom/gron;
SHA-256 of the archive
`ca0335826b02b044fa05d7e951521e45c6ced1c381a73ed5803450088e18bf22`)
and run unmodified. The one wrinkle is the tool's own: the
release binary answers `--version` with `gron version dev`,
so 0.7.1 is established by the archive, not by the binary —
chapter 3 says so plainly. jq 1.7 (the system package) played
the partner and referee roles in chapters 6 and 9. All
transcripts live in `gron-lab/transcripts/`; the fixtures are
`gron-lab/library.json` and `gron-lab/weather.jsonl`.

Run for real, with transcripts as evidence:

- Install, the full `--help` output including the exit-code
  table, and both `--version` spellings printing "dev" (t00).
- First flattening of the library fixture: all 45 statements,
  the count checked with `wc -l`, and the scalar and deep
  nesting cases (`42`, a bare string, `[true, false]`,
  a four-level chain ending in null) (t01).
- Grepping by path (`title`, `author.name`), by anchored value
  (`on_loan = 2`), by content (`grep -i cloud`), by structure
  (`tags[1]`), by absence (`null`), and counting (`-c`) (t02).
- The round trip: ungron rebuild; SHA-256 hashes shown to
  differ; jq-canonicalised forms diffed identical. Partial
  extracts rebuilt (titles only; whole catalogue; second book
  alone, showing the `null` gap fill), and `--values` (t03).
- Modes and failures: `--json` dialect and its ungron
  round trip, `--stream` over JSON-lines (and the same file
  without the flag, silently yielding its first document),
  `--no-sort`, truncated JSON → exit 3, non-JSON → exit 3,
  missing file → exit 1, empty input → exit 3, malformed
  statements into ungron → exit 5 (t04).
- Gron and jq side by side: the same lookups both ways, jq's
  `select` answering the comparison question gron cannot,
  the null-printing partial objects that show why, an average
  computed by jq from a gron-selected list (t05).
- Editing: a value changed with sed and read back, fields
  removed with `grep -v` and verified absent, statements
  written by hand and inserted mid-stream, odd keys — spaces,
  a dot, a leading digit, an accented character — flattened,
  grepped and rebuilt exactly (t06).
- The recipes run as recipes: statement inventories of the
  fixture (45 statements, 5 empty-array containers, 2 nulls,
  per-segment counts), field stripping verified by a zero
  count, and a one-line statement diff for a one-value edit
  (t07).
- Root names: ungron accepting statements rooted at names
  other than `json` and merging two roots into one object —
  a behaviour the limits chapter reports as found, after an
  early draft of that chapter guessed it wrong and the lab
  corrected it (t08).

Documented only — described from the project's documentation,
not run, and the text does not claim otherwise:

- Fetching JSON from a URL (`gron https://…`, and the `-k`
  insecure flag): the lab had no server assigned to it, so
  no fetch was run. The exit code 4 for fetch failures is
  quoted from `--help`.
- Package-manager and source installs (the project's README
  routes); the guide teaches the release binary it ran.
- The neighbour tools of chapter 11 — fx, jless and
  Python's json.tool are characterised, not run; jq and yq
  are covered by their own guides in this series (jq was run
  here in its partner role only).
- Behaviour at very large document sizes: the lab's fixtures
  are deliberately small, and chapter 12's memory caution is
  a statement about the tool's design, not a measurement.
- Colourised output (`-c`/`-m`): off automatically in pipes,
  which is where every transcript was made.

Fixtures are this guide's own and deliberately generic: an
invented public library's catalogue (invented books, authors
and branch names) and three invented weather-station
readings — no real people, organisations, or data. Nothing
in the lab was fetched from a live service.
