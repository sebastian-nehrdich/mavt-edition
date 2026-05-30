# MAVT chapter 4 — Sthiramati's *Madhyāntavibhāgaṭīkā*, re-edition

A machine-driven, sentence-by-sentence philological re-edition of
**chapter 4 of Sthiramati's *Madhyāntavibhāgaṭīkā* (MAVT)** — the
*Pratipakṣa-bhāvanā-pariccheda*, the chapter on the cultivation of
antidotes (the 37 *bodhipakṣya-dharmāḥ*) — based on Susumu Yamaguchi's
editio princeps (Nagoya: Hajinkaku, 1934).

The point of the project is not to retype Yamaguchi but to **interrogate
his reconstructions.** Wherever Yamaguchi printed Sanskrit in *italics*,
the Kathmandu manuscript he was working from had a lacuna — those
italicised passages are his guesses, back-translated from the Tibetan
or filled in by parallel. Ninety years on, those guesses can be
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
├── all-pages.html                     thumbnail grid of all 321 PDF pages
├── mavt-reedition-workflow.md         methodology + lessons learned
├── images/
│   └── page-001.jpg … page-321.jpg    every page of the PDF, 400 DPI JPG
└── editions/
    └── chapter-4/                     pratipakṣa-bhāvanā-pariccheda
        ├── mavt-page-166.html         per-page format,
        ├── mavt-page-167.html         each with its scan embedded
        ├── mavt-page-168.html
        ├── mavt-page-169.html
        └── mavt-page-170.html
```

**The original PDF is not redistributed here.** All 321 PDF pages are
provided as 400-DPI JPG images in `images/`; the underlying scan is
the Yamaguchi 1934 edition, available from major academic libraries.

### How to read the editions

Each HTML file is self-contained (CSS embedded). Open in a browser or
view through the GitHub Pages site at
**https://sebastian-nehrdich.github.io/mavt-edition/**.

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

| Edition file | Yamaguchi p. | PDF file p. |
| --- | --- | --- |
| `editions/chapter-4/mavt-page-166.html` | 166 | 204 |
| `editions/chapter-4/mavt-page-167.html` | 167 | 205 |
| `editions/chapter-4/mavt-page-168.html` | 168 | 206 |
| `editions/chapter-4/mavt-page-169.html` | 169 | 207 |
| `editions/chapter-4/mavt-page-170.html` | 170 | 208 |

PDF file page N corresponds to Yamaguchi page (N − 38).

## Methodology

See [`mavt-reedition-workflow.md`](mavt-reedition-workflow.md) for the
full step-by-step protocol: how each sentence is anchored, how
parallel-searches are run against the Tibetan canonical witness, what
counts as admissible evidence (and what does not — notably, the
corpus-indexed Yamaguchi Sanskrit itself), and lessons learned.

The work was produced by a Claude (Anthropic) agent driving the
[DharmaMitra](https://dharmamitra.org/) `/primary/` and
`/cat-translate/` API endpoints. No human philologist has yet reviewed
the output sentence-by-sentence.

## Citation

If you use any reading from this repository, please cite as:

> Anthropic Claude agent (driving DharmaMitra `/primary/`), *Sthiramati MAVT ch. 4 re-edition draft*. GitHub: github.com/sebastian-nehrdich/mavt-edition.

Yamaguchi's original is:

> Yamaguchi, Susumu (ed.), *Madhyāntavibhāgaṭīkā: Exposition systématique du Yogācāravijñaptivāda*. Nagoya: Librairie Hajinkaku, 1934.

## License

Editorial work in this repository is released under
[CC-BY-4.0](https://creativecommons.org/licenses/by/4.0/). The
included page-image scans from Yamaguchi 1934 are reproduced for
scholarly comparison; the underlying typesetting is in the public
domain in the EU and in many other jurisdictions.
