# MAVT re-edition workflow

A resumable, page-by-page philological re-edition of Sthiramati's
*Madhyāntavibhāgaṭīkā* (MAVT). The base text is Yamaguchi 1934
(Nagoya / Hajinkaku) as OCR'd in `sources/mavt_yamaguchi_ocr.txt`.

The point of the project is **not** to retype Yamaguchi but to interrogate
his reconstructions. Wherever Yamaguchi printed Sanskrit in *italics*, the
manuscript he was working from had a lacuna — those italicised passages are
his guesses, back-translated from the Tibetan or filled in by parallel,
typeset as conjectures. The italics are the parts that matter most. The
manuscript-bearing passages (roman in Yamaguchi) are usually safe and only
need spot-checking; the reconstructed passages need full philological
treatment against the Tibetan, the underlying Vasubandhu *bhāṣya*, the
Maitreya *kārikā*, and any later Sanskrit citation in which they appear.

This workflow file is meant to be re-opened by a future Claude session. It
records: what to do, what *not* to do, what conventions to follow, what is
already done, and what to do next. Update the **Status & next steps**
section after every page.

---

## 1. Source files

| File | Role |
| --- | --- |
| `sources/mavt_yamaguchi_ocr.txt` | base text — the Yamaguchi 1934 edition, OCR'd, page-marked with `# END_OF_PAGE_<n>` lines |
| `references/T06sthmavt-yg-trans.tsv` | sentence-aligned Sanskrit + English from a prior machine pass over Yamaguchi — **do not use the Sanskrit column as evidence** (it is Yamaguchi's reconstruction, same provenance as the base text); the English column is occasionally useful as an orientation gloss |
| `references/mavt-ja.txt` | Yamaguchi's own Japanese translation. Tolerably reliable for the *sense* he read into reconstructed passages. Useful for triage but does not constitute philological evidence. |
| `references/SA_T06_vmvkbh_u.txt` | Vasubandhu's *Madhyāntavibhāgabhāṣya* (MAVBh) — Sanskrit, independent attestation. **Valid evidence.** Sthiramati quotes the *bhāṣya* throughout; matching strings here are gold. |

DharmaMitra `/primary/` is the canonical-corpus search endpoint. Used via
`scripts/primary-search.sh --trim`.

### Index pollution warning

The corpus index *includes* the Yamaguchi MAVT Sanskrit (segments under
`SA_…` IDs that resolve to MAVT). Any hit on Sthiramati's own MAVT in
Sanskrit is **circular** — it's the same text we're trying to fix. The
Tibetan MAVT (segments under `BO_…`) is independent and useful: it is
Yamaguchi's primary reconstruction source, but the Tibetan witness itself
predates him. The MAV kārikā (Maitreya) and the MAVBh (Vasubandhu) are
independent in both Sanskrit and Tibetan.

**Rule:** when a parallel hit is just MAVT itself in Sanskrit, drop it.
When it is MAVT in Tibetan, MAV in any language, MAVBh in any language, or
some unrelated text quoting MAVT, keep it.

---

## 2. Working unit

One **sentence** at a time. A sentence is delimited in the OCR by
single-daṇḍa `|`, double-daṇḍa `||`, or full stop. Verse pādas are kept
together as one unit. When a sentence runs across a page break, take it
whole even if that means crossing a `# END_OF_PAGE_<n>` line.

For each sentence, the agent always:

1. **Reads the OCR window** — the target sentence plus ~5 lines before and
   ~5 lines after, so it can see how Yamaguchi punctuated, where his
   footnotes attach, and what the surrounding argument is.
2. **Extracts the Tibetan** from Yamaguchi's footnotes (lines starting
   `n) Ms. … Tib. …`). The Tibetan in those footnotes is what Yamaguchi
   was looking at when he reconstructed.
3. **Runs the searches** (see §3).
4. **Greps the rest of the MAVT OCR** for nearby Sanskrit terms, similar
   constructions, repeated formulas. Sthiramati recycles vocabulary
   heavily; later attested (roman, non-italic) occurrences are evidence
   for what an earlier reconstructed occurrence ought to read.
5. **Compares evidence to Yamaguchi's reading.** Either confirms it,
   proposes an emendation, or marks it *non liquet* with a reasoned note.
6. **Translates** the sentence on the agreed Sanskrit.

### Italics detection caveat

The OCR marks italics with `*…*`. This is **noisy**:

- Some footnotes are wrapped entirely in `*…*` because the apparatus
  itself is set in italic type in the print — that does not mean their
  content is "reconstructed".
- Some italic spans in the OCR break across line boundaries and the
  asterisks get dropped.
- Roman words occasionally end up wrapped because of nearby italics.

Treat asterisks as a *hint*, not a verdict. The authoritative signal for
"this is reconstruction" is: **the manuscript apparatus footnote at that
sentence says `Ms. ……` with ellipses, or the line is purely in italics
with no roman fragments at all**. When in doubt, read the footnotes.

---

## 3. Search protocol (per sentence)

Every sentence gets **at least three searches** plus a grep. All four are
required even when the sentence looks safe.

### 3.1 Sanskrit semantic search (forward)

```bash
./scripts/primary-search.sh --trim <<'JSON'
{
  "search_input": "<the sentence in Sanskrit, with reconstructions in if nothing better available>",
  "search_type": "semantic",
  "filter_source_language": "all",
  "max_depth": 30,
  "do_ranking": false
}
JSON
```

Purpose: find independent Sanskrit attestation of the same phrase
elsewhere (commentaries, citations, doctrinal handbooks).

### 3.1bis (NEW, do this first) — fetch the canonical Tib MAVT block

Before any other search, pull the continuous Tibetan for the page from
the Derge MAVT (`BO_T06_D4032`):

```bash
./scripts/primary-search.sh --trim <<'JSON'
{
  "search_input": "<a Tibetan phrase you expect for this page, e.g. from Yamaguchi's footnotes>",
  "search_type": "semantic",
  "filter_source_language": "bo",
  "max_depth": 50,
  "do_ranking": false,
  "source_filters": {"include_files": ["BO_T06_D4032"]}
}
JSON
```

This typically returns several overlapping segments around the relevant
folio (e.g. `189b-0`, `189b-6`, `189b-9`, `189b-14`, `189b-17`) which
together cover the whole page. Reassemble them into a single Tibetan
block at the top of the page's output file. Every subsequent sentence
section can then quote a substring of that block as its primary
witness, without further calls.

This step is **load-bearing**. It dwarfs the other searches for value
on heavily-reconstructed pages.

### 3.2 Tibetan reverse search (with Sanskrit target filter)

```bash
./scripts/primary-search.sh --trim <<'JSON'
{
  "search_input": "<the Tibetan from Yamaguchi's footnote>",
  "search_type": "semantic",
  "filter_source_language": "bo",
  "filter_target_language": "sa",
  "max_depth": 30,
  "do_ranking": false
}
JSON
```

Purpose: ask "what Sanskrit do other Tibetan→Sanskrit alignments produce
for this Tibetan phrase?" When the alignment system has seen this Tibetan
construction in better-attested Sanskrit texts, the answer constrains
what Sthiramati's original Sanskrit must have looked like.

This is the **most load-bearing** search step. The user has emphasised it
explicitly. Run it even when the Tibetan in the footnote is short.

### 3.3 Phrase search (regular) for any suspect lexeme

If a single word in Yamaguchi looks odd — a hapax, a strange compound, a
weird sandhi — pull it out and:

```bash
./scripts/primary-search.sh --trim <<'JSON'
{
  "search_input": "<the suspect word>",
  "search_type": "regular",
  "filter_source_language": "sa",
  "max_depth": 30,
  "do_ranking": false
}
JSON
```

Asks: "how often is this word/form actually attested?" A zero-hit reading
in Yamaguchi against a 50-hit alternative reading is decisive.

### 3.4 Grep the MAVT OCR itself

```bash
grep -n -E "<root or compound>" sources/mavt_yamaguchi_ocr.txt
```

Look for the same vocabulary used elsewhere in MAVT — but **only count
non-italic (no `*…*`) occurrences as evidence**. Italic occurrences are
just Yamaguchi consistent with himself; they don't independently support a
reading. Also grep the Tibetan footnotes for parallel Tibetan formulas:

```bash
grep -nE "<Tibetan substring>" sources/mavt_yamaguchi_ocr.txt
```

---

## 4. Editorial decisions

When the evidence is in, classify the sentence:

- **Confirmed** — Yamaguchi's reading is supported (or at least
  unrefuted). Keep it. Mark `confirmed`.
- **Improved** — propose a new reading. Document: what Yamaguchi printed,
  what is proposed, what evidence supports it (parallels with nexus
  links, Tibetan back-correspondence, MAVT-internal parallels). Mark
  `proposed`.
- **Non liquet** — evidence too thin or contradictory. State both
  candidates and the reasoning. Mark `non-liquet`.

Never silently emend. Every change carries a justification block.

### What counts as evidence

In descending strength:

1. The same wording appearing in Vasubandhu's MAVBh (Sanskrit, independent).
2. The same wording appearing in MAV (Maitreya kārikā) Sanskrit.
3. A non-italic occurrence of the same phrase elsewhere in Yamaguchi's MAVT.
4. A close parallel in another sixth-century Yogācāra source (Asaṅga,
   Vasubandhu's *Triṃśikā* commentary, Dharmakīrti's earliest works,
   Sthiramati's other *ṭīkā*s).
5. The Tibetan back-correspondence under filter_target_language=sa
   returning a stable Sanskrit alignment.

### What does *not* count

- A hit on MAVT itself in Sanskrit (it's circular).
- A reading from Yamaguchi's own apparatus that is itself italicised /
  reconstructed.
- A Japanese-translation paraphrase from `references/mavt-ja.txt`.

---

## 5. Output

One file per page, HTML, in the style of the prior
`output/critical-editions/mavt-ch1-commentary.html`:

```
output/critical-editions/mavt-page-<NN>.html
```

Section per sentence (`§1`, `§2`, …) with the boxes:

- `.yamaguchi` — what Yamaguchi printed (italic spans wrapped in
  `<span class="recon">`).
- `.tibetan` — the Tibetan from Yamaguchi's footnote and/or the canonical
  Tibetan from a `BO_…` segment if found.
- `.proposed` — the proposed Sanskrit reading. Omit when reading is
  confirmed.
- `.justification` — what the evidence is. Include nexus links.
- `.parallel` — each independent witness with its `src_link`.
- `.compare` (before / after) — Yamaguchi vs. proposed, side by side, for
  the cases where a change is being made.
- `.japanese` — the Yamaguchi line from `mavt-ja.txt` for the same
  sentence, as orientation.
- `.translation` — English translation on the agreed Sanskrit.
- `.warn` — anything irregular (lacuna, illegible Tibetan, OCR damage,
  index pollution detected).

Per-page header carries a `STATUS` block: page number, OCR line range,
date of pass, model, what was confirmed / proposed / non liquet, plus any
new lessons that should propagate back into this workflow doc.

---

## 6. Workflow (loop body)

For each page:

1. Read OCR lines for that page plus ±15 lines.
2. Identify sentence units; number them §1, §2, …
3. For each sentence: extract Tibetan from footnotes, do the four
   searches in §3, grep, weigh evidence, classify, write the HTML block.
4. Stitch the page into `mavt-page-<NN>.html` with the prior pages'
   stylesheet.
5. Update **Status & next steps** below with: what got confirmed, what
   got proposed, what stayed *non liquet*, and any new lesson.
6. Hand back to the user. Wait for them to evaluate before starting the
   next page.

---

## 7. Defaults

- Always pipe `/primary/` through `--trim`.
- `max_depth: 30`, `do_ranking: false`, unless the page is a known
  difficult locus (then bump `max_depth` to 50 once).
- Cite `src_link` verbatim — do not reconstruct nexus URLs.
- Keep batches sentence-sized. Do not "batch a page's worth of searches"
  into one call — context cross-contamination ruins triage.
- Pause at the end of every page for user review. Drift is cheaper to
  correct page-by-page than chapter-by-chapter.

---

## 8. Status & next steps

| Page | OCR lines | Date | Pass | Result | File |
| --- | --- | --- | --- | --- | --- |
| 1 | 1157–1191 | 2026-05-28 | initial | 3 proposed, 2 verified, 1 non liquet | `mavt-page-01.html` |
| 2 | 1193–1228 | 2026-05-28 | initial | 2 proposed, 8 verified | `mavt-page-02.html` |
| 3 | 1230–1238 | 2026-05-28 | initial | 2 proposed, 3 verified | `mavt-page-03.html` |
| 4 | 1240–1281 | 2026-05-28 | initial | 3 proposed, 9 verified | `mavt-page-04.html` |
| 5 | 1283–1318 | 2026-05-28 | initial | 2 proposed, 11 verified | `mavt-page-05.html` |
| 6 | 1320–1353 | 2026-05-28 | initial | 2 proposed, 8 verified | `mavt-page-06.html` |
| 7 | 1355–1390 | 2026-05-28 | initial | 0 proposed, 10 verified | `mavt-page-07.html` |
| 8 | 1392–1423 | 2026-05-28 | initial | 0 proposed, 8 verified | `mavt-page-08.html` |
| 9 | 1425–1437 | 2026-05-28 | initial | 0 proposed, 5 verified, 1 non liquet | `mavt-page-09.html` |
| 10 | 1439–1472 | 2026-05-28 | initial | 0 proposed, 8 verified (cross-ref to `mavt-ch1-commentary.html`) | `mavt-page-10.html` |

Next page to attempt: **11** (OCR lines 1474–1509), after user review of
pp. 1–10. Note: pp. 10–14 are also covered (in older style) by
`mavt-ch1-commentary.html`; future per-page work from p. 11 onwards
should either supersede the older file's analysis or note where the
new per-page treatment agrees / disagrees.

### Lessons that should propagate back into §3 of this workflow

(Update this list after every page.)

- **Add a first-step Tibetan-side <code>/primary/</code> query
  restricted to <code>include_files: ["BO_T06_D4032"]</code>** —
  this returns continuous, overlapping segments around the relevant
  folio and gives the canonical Tibetan for the whole page in 1–2
  calls. It is the single highest-yield search on this kind of page.
  Run it before the Sanskrit semantic search, not after.
- **Cross-check Yamaguchi's Japanese against his Sanskrit.** Page-1
  §2 is a clear case where Yamaguchi's own Japanese
  (<em>釋せん</em> = "wishing to explain") disagrees with his
  printed Sanskrit (<em>cikīrṣur bhāṣyam</em> = "wishing to compose
  the bhāṣya"). When the two diverge, the Japanese is often closer
  to the Tibetan. Treat this disagreement as a soft signal that the
  Sanskrit reconstruction is unreliable.
- **The MAVT corpus pollution is real and predictable.** Sanskrit
  semantic searches will always return
  <code>SA_T06_sthmavt</code> / <code>SA_T06_sthmavtyg</code> at
  rank 1. Filter these out with
  <code>jq '.results[] | select(.source | test("sthmavt") | not)'</code>
  rather than visually scrolling past them; saves attention.
- **Apparatus-footnote italics on this page convey no editorial
  signal** — they are pure typographic convention (the footnotes are
  set in italic type wholesale). The main-text italics, by contrast,
  do reliably mark reconstruction. Distinction confirmed by
  inspection of page 1; expect the same on subsequent pages.
- **Grep MAVT for the proposed form before adopting an emendation.**
  Page-1 §4 (<em>pūjayānānām</em>) returned zero hits anywhere else
  in the OCR, confirming it is a one-off Yamaguchi conjecture rather
  than a recurring Sthiramati usage. Absence of internal corroboration
  was itself evidence.
- **OCR has at least one verifiable gap.** At the p. 3 / p. 4
  transition, the śāstra-defining verse <em>yac chāsti ca kleśaripūn
  …</em> is in the corpus-indexed Yamaguchi text but missing from the
  OCR file. When a sentence in the OCR starts with a hanging conjunction
  (<em>ca</em>, <em>tu</em>, <em>vā</em>) and no preceding conjunct is
  visible, suspect an OCR gap and consult the corpus-indexed
  Yamaguchi (<code>SA_T06_sthmavtyg</code>) for the missing run-on.
  This is the **only** philologically-acceptable use of the indexed
  Yamaguchi Sanskrit: not as evidence for emendation, but as a check
  against OCR loss.
- **OCR `*…*` italic markers are wholesale-italicised at footnote
  level.** All numbered footnote blocks at page-bottom are wrapped in
  `*…*`. This is typographic convention (the apparatus is set in
  italic type) and conveys no editorial signal. Do **not** treat any
  italic span inside a footnote as "reconstructed".
- **OCR page-break catch-words are duplicate text artefacts.** The
  p. 2 / p. 3 boundary contains lines 1216–1217 that simply repeat the
  first words of p. 3 (a printer's catch-word reproduced by the OCR as
  duplicate text). Strip these on read.
- **OCR `ṛi` vs `ṛ`.** The Yamaguchi 1934 print uses the convention
  "<em>ṛi</em>" for "<em>ṛ</em>" (e.g. <em>mṛiṣā</em>, <em>pṛithag</em>,
  <em>āvṛittis</em>). Modernise silently.
- **Visarga-as-nasal sandhi** (e.g. <em>paramampāraṅgataḥ</em>) is
  19th-century print convention; modernise to standard
  visarga/anusvāra silently.
- **The <em>sapta-bhāva</em> vs <em>sapta-artha</em> finding (p. 10
  §3) is a key cross-tract lexical choice** — Sthiramati systematically
  substitutes <em>bhāva</em> for MAVBh's <em>artha</em> in this
  context. The Tibetan <em>dngos po bdun</em> witnesses the
  Sanskrit reading and rules out the alternative.
- **Some MAVT passages are <em>Tib. om.</em>** — e.g. p. 10 §4's
  <em>yathoddeśas tathā nirdeśaḥ</em>-paribhāṣā. When a Sanskrit
  sentence is missing in the Tibetan, flag in apparatus rather than
  emend. Either the translators elided a Sanskrit-grammatical maxim
  that didn't carry into Tibetan, or their Sanskrit exemplar lacked
  the sentence; the question can't be resolved from corpus alone.
