# MAVT — Sthiramati's *Madhyāntavibhāgaṭīkā*, re-edition

A machine-driven, sentence-by-sentence philological re-edition of
**Sthiramati's *Madhyāntavibhāgaṭīkā* (MAVT)**, based on Susumu
Yamaguchi's editio princeps (Nagoya: Hajinkaku, 1934). This repository
covers the first chapter (*Lakṣaṇa-pariccheda*) and the fourth
(*Pratipakṣa-bhāvanā-paricchedaḥ*); the remaining chapters are
in-progress.

The point of the project is not to retype Yamaguchi but to **interrogate
his reconstructions.** Wherever Yamaguchi printed Sanskrit in *italics*,
the Kathmandu manuscript he was working from had a lacuna — those
italicised passages are his guesses, back-translated from the Tibetan
or filled in by parallel. Eighty-five years on, those guesses can be
re-examined against:

- the **canonical Tibetan MAVT** of Mahājana / Śīlendrabodhi (Derge
  Tengyur, `BO_T06_D4032`), which Yamaguchi used heavily and is now
  fully indexed in modern corpora;
- the underlying **Vasubandhu MAVBh** (Pandeya, Nagao) and the
  Maitreya **MAV** kārikā;
- **Sthiramati's own related works** (Triṃśikā-bhāṣya, Pañcaskandhaka,
  Trisvabhāva-nirdeśa-bhāṣya) which share vocabulary and idiom;
- **cross-tract parallel-search** via the DharmaMitra
  [`/primary/`](https://dharmamitra.org/) endpoint, which returns
  near-neighbours across the canonical Buddhist corpus.

## Repository contents

```
mavt-edition/
├── README.md                          this file
├── index.html                         landing page (GitHub Pages)
├── viewer.html                        side-by-side viewer ?ed=…&img=…
├── all-pages.html                     thumbnail grid of all 321 PDF pages
├── mavt-reedition-workflow.md         step-by-step methodology, status tracker, lessons
├── images/
│   └── page-001.jpg … page-321.jpg    every page of the PDF, 150 DPI JPG
└── editions/
    ├── chapter-1/                     preface + lakṣaṇa-pariccheda
    │   ├── mavt-page-01.html          page-by-page format, the preface
    │   ├── mavt-page-02.html          (one HTML file per Yamaguchi-page,
    │   ├── … through …                 each with its scan embedded)
    │   ├── mavt-page-10.html          page-10 (chapter-1 opening)
    │   └── mavt-ch1-commentary.html   older multi-page format, pp. 10–14
    └── chapter-4/                     pratipakṣa-bhāvanā-pariccheda
        └── mavt-ch4-commentary.html
```

**The original PDF is not redistributed here.** All 321 PDF pages are
provided as 150-DPI JPG images in `images/`; the underlying scan is
the Yamaguchi 1934 edition, available from major academic libraries.

### How to read the editions

Each HTML file is self-contained (CSS embedded). Open in a browser.
Each Yamaguchi-page is segmented into numbered "sentence units"
(§1, §2, …), with for each unit:

- **Yamaguchi text** — verbatim from the OCR, with italic-vs-roman
  preserved (italic = his reconstruction, roman = manuscript-attested).
- **Tibetan witness** — the canonical Tibetan from Derge MAVT, with
  segment ID and a deep-link into the DharmaMitra reading room.
- **Justification** — what the search/parallels yielded.
- **Proposed reading** (only where a change is warranted) — the
  emendation, with status chip: `VERIFIED` (no change),
  `PROPOSED` (substantive emendation offered), or `NON LIQUET`
  (both candidates remain in play).
- **Yamaguchi's Japanese** — orientation gloss from his own translation.
- **English translation** of the agreed Sanskrit.

A summary table at the bottom of each page-file lists all sentence
units with status + headline change.

### Page-image cross-reference

| Edition file | OCR Yamaguchi-page | PDF file-page |
| --- | --- | --- |
| `mavt-page-01.html` | 1 | 39 |
| `mavt-page-02.html` | 2 | 40 |
| `mavt-page-03.html` | 3 | 41 |
| `mavt-page-04.html` | 4 | 42 |
| `mavt-page-05.html` | 5 | 43 |
| `mavt-page-06.html` | 6 | 44 |
| `mavt-page-07.html` | 7 | 45 |
| `mavt-page-08.html` | 8 | 46 |
| `mavt-page-09.html` | 9 | 47 |
| `mavt-page-10.html` | 10 | 48 |
| `mavt-ch1-commentary.html` | 10–14 | 48–52 |
| `mavt-ch4-commentary.html` | (ch. 4) | — see file |

When an edition file cites OCR line numbers, those refer to the
line-numbered OCR text on which the project is based (not preserved
in this repository — it is in the agent's working directory).

## Methodology

See [`mavt-reedition-workflow.md`](mavt-reedition-workflow.md) for the
full step-by-step protocol: how each sentence is anchored, how
parallel-searches are run against the Tibetan canonical witness, what
counts as admissible evidence (and what does not — notably, the
corpus-indexed Yamaguchi Sanskrit itself), and lessons learned across
the first ten pages.

The work was produced by a Claude (Anthropic) agent driving the
[DharmaMitra](https://dharmamitra.org/) `/primary/` and
`/cat-translate/` API endpoints over many sessions. No human
philologist has yet reviewed the output sentence-by-sentence.

## Status

Pages with re-edition coverage so far:

| Pages | File(s) | Headline findings |
| --- | --- | --- |
| pp. 1–9 (preface) | `mavt-page-01.html` … `mavt-page-09.html` | substantive emendations on pp. 2, 3, 4, 5, 6 (about 14 proposals total); pp. 7–9 mostly verify Yamaguchi |
| p. 10 (ch. 1 opening) | `mavt-page-10.html` + `mavt-ch1-commentary.html` | `sapta-bhāva` ≠ MAVBh's `sapta-artha`; `Tib. om.` flag on the `yathoddeśas tathā nirdeśaḥ` paribhāṣā |
| pp. 11–14 (ch. 1 continued) | `mavt-ch1-commentary.html` | kārikā I.1 commentary, sad-asat-lakṣaṇa block, anti-Vaibhāṣika reading at §11 |
| ch. 4 | `mavt-ch4-commentary.html` | pratipakṣa-bhāvanā |

Next: pp. 11+ in the per-page format superseding the older `ch1-commentary` style.

## Citation

If you use any reading from this repository, please cite as:

> Anthropic Claude agent (driving DharmaMitra `/primary/`), *Sthiramati MAVT re-edition draft*. GitHub: github.com/sebastian-nehrdich/mavt-edition.

Yamaguchi's original is:

> Yamaguchi, Susumu (ed.), *Madhyāntavibhāgaṭīkā: Exposition systématique du Yogācāravijñaptivāda*. Nagoya: Librairie Hajinkaku, 1934.

## License

Editorial work in this repository is released under
[CC-BY-4.0](https://creativecommons.org/licenses/by/4.0/). The
included page-image scans from Yamaguchi 1934 are reproduced for
scholarly comparison; the underlying typesetting is in the public
domain in the EU and in many other jurisdictions.
