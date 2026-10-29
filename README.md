# Construct Ledger

Every theoretical construct stated in the abstracts of five management and organization
journals, extracted with a verbatim span from the text it came from, merged at a
calibrated threshold, and open to inspection.

**Live site:** https://ccfarre.github.io/construct-ledger/

Research artifact for the *Organization Science* special issue **AI-Enabled Frontiers in
Organizational Science**.

---

## What this is

A language model can read 9,086 abstracts and name the constructs in each one. The
problem has never been whether it can; it is whether anyone should believe the result.
This ledger is built so that the answer does not rest on trust.

Every extracted construct carries a **provenance span**: a string of three or more words
copied verbatim from the abstract it was drawn from. The span is checked against the
source locally, at no cost, by exact string match. For this corpus, **40,979 of 40,979
spans were found verbatim in their own abstract**. That check is mechanical, it is free,
it runs in seconds, and anyone can re-run it. An instrument that reports its own
hallucination rate is a different object from one that asks to be taken on faith.

The site lets a reader do four things:

1. **Read the ledger's figures**, all drawn from the shipped data rather than hand-entered.
2. **Paste a working paper** and see two things: the constructs it appears to propose,
   which are recurring terms that appear nowhere in the corpus as a construct name, each
   placed by its head-noun family and nearest existing neighbours; and the constructs it
   borrows, with the empirical neighbourhood each one belongs to.
3. **See the network.** Reading a paper draws the constructs it works with, the corpus
   ties among them, and the ties observed inside the manuscript itself, which is what
   gives a construct the corpus has never seen a position among constructs it has.
   Browsing a construct draws the company it keeps.
4. **Browse 23,522 constructs**, with every name that was merged into each one.
5. **Check the grounding** for any construct, with the span highlighted in its context and
   a DOI to the source.

## The corpus

| | |
|---|---|
| Journals | Academy of Management Journal, Academy of Management Review, Administrative Science Quarterly, Organization Science, Strategic Management Journal |
| Years | 1990 to 2025 |
| Abstracts usable after cleaning | 9,108 |
| Abstracts yielding at least one construct | 9,086 |
| Abstracts refused by the model | 22 |
| Construct mentions | 40,979 |
| Distinct constructs after merging | 23,522 |
| Constructs used in five or more papers | 1,083 |

The 22 refusals are worth reading rather than hiding. Every one is a record whose
deposited text is not an abstract: eight "Publications Received" book lists, an editor's
comment, an obituary, an erratum notice, an editorial-team announcement, a presidential
address, and nine cases where the publisher deposited an acknowledgements footnote, a
rights notice, an epigraph or an author list in place of the abstract. The model returned
nothing in exactly the cases where there was nothing to return, and nowhere else.

## The merge threshold was measured, not chosen

Constructs are merged when two names denote the same thing. Doing that by exact name
leaves most of the space as one-off phrasing, so merging uses embedding similarity plus a
qualification rule. The threshold was calibrated rather than picked: positives are pairs
of wordings the extractor independently gave the same name, negatives are random pairs,
and the threshold is the lowest value holding false merges under one percent.

| Corpus | Calibrated threshold | Same-construct pairs retained | False merges |
|---|---|---|---|
| Management and organization | 0.83 | 97.2% | < 1% |
| Information systems | 0.84 | 97.4% | < 1% |

The two fields separate at essentially the same cosine, and their random-pair
distributions are nearly identical (medians 0.756 and 0.751), so one threshold serves
both. The shipped ledger uses **0.84**, the stricter of the two.

## Where it fails

Three limits, all of them visible in the data rather than argued away.

**The qualification rule over-merges.** A merge requires one name's word set to be a
superset of the other's, a rule built for a field where the modifier individuates the
construct. In management the two-word name is often the general construct and the longer
name a theorised specialisation of it, so the rule absorbs distinctions that matter:
*transient competitive advantage* into *competitive advantage*, *firm specific human
capital* into *human capital*, *corporate social performance* into *corporate
performance*. The same shape appears in information systems, where *IT innovation* is
absorbed into *innovation*. This is a property of the rule, not of either field, and every
merge is inspectable in the browser.

**The single-use tail is threshold-sensitive.** Moving the threshold from 0.83 to 0.88
moves the construct count by nine percent, and 93% of that movement is constructs
mentioned once. Any statistic resting on the single-use tail should be read with that in
mind; statistics on constructs used in ten or more papers barely move.

**The instrument shapes the count.** The information systems ledger was extracted with a
fuller schema than the management ledger and yields 3.49 constructs per abstract against
4.51. Until the same prompt is run on both corpora, per-document comparisons across the
two fields are confounded, and only mention-normalised comparisons are safe.

## Reproducing it

The tools are command-line executables, Python standard library only except where noted,
each resumable and each refusing to spend money without `--go`.

```
tools/iscorpus-mgmt-v2    assemble the corpus from OpenAlex and Crossref
tools/isclean-mgmt        strip rights notices and publisher boilerplate
tools/iscx-mgmt-v4        extract constructs (concurrent, cost-ceilinged, resumable)
tools/iscledger-mgmt      put the jsonl ledger into the sqlite shape iscgeo reads
tools/iscembed-mgmt       embed name-and-definition strings (resumable, retrying)
tools/iscgeo              calibrate the threshold and merge into canonical constructs
tools/build-assets-mgmt   build the site data from the canonical ledger
```

Every cost-bearing step prints an estimate and waits for `--go`, meters the real spend as
it runs, stops at a ceiling computed from observed rather than assumed cost, and never
pays twice for work already done. `--selftest` on each tool runs without network access.

| Step | Cost | Wall clock |
|---|---|---|
| Corpus assembly | free | 41 min |
| Extraction, 9,086 abstracts | $12.49 | 206 min at 8 workers |
| Embedding 40,945 strings | $0.20 | 15 min at 4 workers |
| Calibration and merging | free | 4 min |
| Grounding verification | free | seconds |

Total model spend for the corpus: **about $12.69**, of which $12.49 is extraction metered from the token counts the API returns and about $0.20 is embedding, estimated from character counts because that endpoint does not meter tokens.

`numpy` is required for the merging step. Everything else is standard library.

## What is in `data/`

```
data/mgmt/meta.json          corpus counts, calibration, grounding rate
data/mgmt/constructs.json    one compact record per construct, for search and browse
data/mgmt/figures.json       precomputed series behind the figures
data/mgmt/graph.json         co-occurrence ties, neighbourhoods, head-noun families
data/mgmt/detail/NNNN.json   definitions and provenance, 100 constructs per file
```

**Abstracts are not redistributed.** Each provenance record ships the span, 120 characters
of context either side, and the DOI. That is what verification needs; the corpus itself
remains with its publishers. The construct names, definitions and structural roles are
model output over published abstracts.

## Citation

Carrasco-Farré, C. (2026). *Construct Ledger: an auditable map of a field's theoretical
vocabulary.* Research artifact.

## Licence

Code is MIT, see `LICENSE`. The derived ledger in `data/` is released for research use;
the underlying abstracts remain the property of their publishers and are not included.
