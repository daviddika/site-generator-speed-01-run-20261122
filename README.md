# site-generator, but faster — a fast drop-in for makefun-site 1.0.0

[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/daviddika/site-generator-speed-01-run-20261122/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/daviddika/site-generator-speed-01-run-20261122/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

A faster drop-in for make.fun's own makefun-site 1.0.0: `solve.js` answers
exactly as makefun-site does on every case — same front matter, template
dialect, Markdown subset, slug rule and build report, byte for byte — in
about half the CPU time.

## How it is faster

- Inline Markdown parsing copies plain runs whole and escapes each run once,
  instead of one HTML-escape call per character.
- Markdown lexing dispatches on the line's first characters instead of
  running a regex per line; front matter is scanned with `indexOf`, never by
  splitting the whole source.
- Slugs are cached, with an ASCII fast path (NFKD is the identity on ASCII);
  each source's word count and long date are computed once and reused by its
  list card and its page.
- Template rendering appends into one shared array per render and joins once,
  instead of building a string per node.

## Files

- `solve.js` — the generator: `run(input)` returns makefun-site 1.0.0's
  answer for `{site, templates, files}`. Dependency-free ES module, Node 22.
- `main.ts` — the house driver, unchanged: reads `/input/cases.json`,
  answers each case with `run`, writes `/output/answers.json`.

## Verification

- Practice set: 3/3 cases identical to the reference (kit scorer: passed 1,
  cases_passed 3/3), build ~205 ms vs ~470 ms for the reference on this
  machine.
- 24 further cases drawn fresh from the kit generator (8 other seeds):
  24/24 identical to the reference oracle.
- 155 hand-built edge cases (front-matter refusals, bad dates, unicode
  slugs, CRLF, shadowed statics, template parse errors, recursive includes,
  unclosed fences/code/emphasis, broken and relative links): 155/155
  identical to the reference.
