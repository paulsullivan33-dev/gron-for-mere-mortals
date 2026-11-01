# Installing Gron

By the end of this chapter gron is on
your machine and you have run the two
checks that prove it: the help screen
(which doubles as the tool's whole
manual) and the version print — which
contains this guide's first honest
surprise.

## What you are downloading

Gron is written in Go and published as
a single self-contained binary: no
interpreter, no libraries to install,
nothing else in the archive. The
project lives at
`github.com/tomnomnom/gron`, and its
releases page offers one archive per
platform. The lab used the current
release, **0.7.1**, file
`gron-linux-amd64-0.7.1.tgz`, on
64-bit Linux. Inside the archive is
exactly one file: `gron`, about
7.2 MB (t00).

Download and unpack it somewhere you
keep small tools:

```bash
curl -LO https://github.com/tomnomnom/gron/releases/download/v0.7.1/gron-linux-amd64-0.7.1.tgz
tar xzf gron-linux-amd64-0.7.1.tgz
./gron --help
```

The lab recorded the archive's SHA-256
so its download is identifiable:
`ca0335826b02b044fa05d7e951521e45c6ced1c381a73ed5803450088e18bf22`.
Checksums like this prove the file you
got is the file the lab got; the
project's own signature, where offered,
is the stronger check, and the releases
page is the only place this guide will
ever send you for the binary — tools
that read your data should come from
their makers, not from mirrors.

Other routes exist and are documented
in the project's README: building from
source with Go, and packages in several
distributions' and ecosystems'
repositories. This guide teaches the
release binary it ran; a packaged gron
of the same version behaves the same.

## The help screen is the manual

`gron --help` prints everything: the
options (`-u`, `-v`, `-c`, `-m`, `-s`,
`-k`, `-j`, `--no-sort`, `--version`),
four worked examples, and — unusually
and helpfully — **the exit codes**,
numbered 0 to 6:

```text
Exit Codes:
  0  OK
  1  Failed to open file
  2  Failed to read input
  3  Failed to form statements
  4  Failed to fetch URL
  5  Failed to parse statements
  6  Failed to encode JSON
```

Keep that list. Chapter 13 is built on
it: nearly every gron failure names its
own number, and the number tells you
which half of the tool was running and
what kind of wrong the input was.

## The version surprise

Now the check that does not go as
expected:

```bash
./gron --version
```

```text
gron version dev
```

The official 0.7.1 release binary
reports itself as **dev**. That is a
packaging wart in the release, not a
sign you downloaded the wrong thing —
the lab's binary came from the
project's own release archive, checksum
above, and behaves as 0.7.1 throughout
this guide. It does mean you cannot
ask a stray gron on a machine *which*
gron it is and get a useful answer;
if version matters to you, keep the
archive name with the binary. Chapter
12 lists this among the limits, because
it is one.

## First proof it works

Feed it the smallest possible document:

```bash
echo '{"a": 1}' | ./gron
```

```text
json = {};
json.a = 1;
```

Two statements, exit code 0. The tool
is installed, and you have already seen
the shape of everything in this guide:
a container statement for the object,
and one statement for the value inside
it. Chapter 4 scales that up to a real
document.
