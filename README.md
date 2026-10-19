# Construct Ledger: overlap check

A static page that checks a working paper against the 16,426 canonical constructs in the
IS construct ledger. No server, no API key, no per-use cost. Everything runs in the
visitor's browser against three JSON files served next to the page.

## What it does

**Paper text.** Drop a PDF or paste text. The page scans it for the 20,709 construct names
and variants in the ledger, using non-overlapping longest-match, and reports which ledger
constructs the paper already uses, with each construct's definition, year of first
appearance, journal spread, mention count, origin and artifact proximity, and the retained
dissent on both codings.

**Construct list.** One construct per line. Each is checked under the qualification rule from
Section 4.3 of the paper, which gives four verdicts:

| Verdict | Test | What it tells the author |
|---|---|---|
| Already in the ledger | exact match on a name or recorded variant | the construct exists under this name |
| Ledger holds a qualified form | your tokens are a subset of a ledger name's | you are naming the parent of something already specified |
| Your name qualifies an existing construct | a ledger name's tokens are a subset of yours | you are specifying an existing construct, so the modifier has to earn its keep |
| Shares a head noun | same head noun, neither name contains the other | sibling, not synonym, and the reviewer will ask how they differ |

**Propositions.** For any pair of resolved constructs, how many distinct papers in the corpus
assert a relationship between them.

It deliberately does not suggest where a new construct should go. Section 5.6 of the paper
reports that the geometric procedure for finding unoccupied regions does not recover
compositions the field has already made.

## Hosting on GitHub Pages

Fits the free tier with room to spare. Total payload is about 5 MB, against a 1 GB site limit
and a 100 MB per-file limit.

```
git init
git add .
git commit -m "Construct ledger overlap check"
git branch -M main
git remote add origin https://github.com/<user>/<repo>.git
git push -u origin main
```

Then Settings → Pages → Source: *Deploy from a branch* → `main` / `/ (root)`. The site appears
at `https://<user>.github.io/<repo>/` within a minute or two.

The `.nojekyll` file stops GitHub from running the content through Jekyll, which is both
faster and avoids surprises with any file or folder whose name starts with an underscore.

Files:

```
index.html          the page, all CSS and JS inline
.nojekyll
data/constructs.json  4.6 MB   16,426 constructs with definitions and metadata
data/edges.json       0.2 MB   13,772 construct pairs with paper counts
data/meta.json        <1 KB    corpus counts quoted in the header
tools/build-ledger-assets    regenerates data/ from canon.jsonl + ledger.sqlite
```

GitHub Pages serves these gzipped, so the real transfer is roughly 1.5 MB on first visit and
nothing after that.

## Regenerating the data

```
tools/build-ledger-assets \
  --canon /path/to/canon.jsonl \
  --db    /path/to/ledger.sqlite \
  --out   site/data
```

Reads only. Makes no API calls and spends nothing.

## Known gap

The corpus-level count of replicated pairs that this script derives does not reproduce the
figure reported in the paper, which was computed by a separate script. Per-pair counts shown
on the page come from this build and are traceable to `data/edges.json`; the corpus-level
figure is deliberately not displayed until the two are reconciled.
