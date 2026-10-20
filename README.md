# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

TakeMeter sorts posts from a WRC (World Rally Championship) fan community,
modelled on [r/EASPORTSWRC](https://www.reddit.com/r/EASPORTSWRC/), by **how
they argue**: `analysis` (a claim backed by a checkable fact), `hot_take` (a
confident claim with no support), or `reaction` (an in-the-moment feeling).
The posts cover both the real championship (the 2025 title fight, the 2026
season) and the EA SPORTS WRC game (setups, wheels vs pads, performance, the
end of support). It labels the style of a post, not whether the opinion is
right.

> **Disclosure:** the 205 posts in `labels.csv` are **synthetic**. They were
> written by AI in the style of Reddit/Steam rally-game discussion, and the
> ones about the real sport use real, checkable WRC results (sources below).
> None were copied from Reddit. Results here measure performance on this
> synthetic set, not on real community posts.

---

## Label Taxonomy

### `analysis`

**Definition:** Makes a point about WRC or the game and backs it with a
specific, checkable fact: a result, a stat, a timed comparison, or a test the
writer ran.

**Example 1:** "Ogier won the 2025 title by four points while skipping three
rounds. Eleven starts beat Evans' fourteen. The part-time schedule everyone
said would cost him clearly didn't."

**Example 2:** "Ran the same Finland stage ten times in the Rally1 Yaris, five
with stock diff and five with preload up. Average was 1.6s quicker with more
preload, nearly all of it on exits from the slow junctions."

### `hot_take`

**Definition:** A confident claim about drivers, teams, rallies or the game
with no support offered. It might be true; it isn't argued.

**Example 1:** "Evans will never win a world title. Some drivers just don't
have it."

**Example 2:** "Setups don't matter. Anyone saying otherwise is coping."

### `reaction`

**Definition:** An immediate personal response to something that just
happened, on a stage the writer just drove or in a rally they just watched.
Feeling, not argument.

**Example 1:** "Lappi rolling 800 metres into the stage. I just put my head in
my hands."

**Example 2:** "Finally finished Kenya without breaking the suspension. I could
cry."

### The hardest boundary

**Which two labels:** `analysis` and `hot_take`

**The decision rule I used every time:**
If the post names a specific, checkable fact **and that fact is the reason
given for the conclusion**, it's `analysis`, even if the tone is heated. If a
fact appears but the conclusion doesn't follow from it ("Toyota won 12 rallies
so the series is fixed"), the fact is decoration and it's a `hot_take`. A
post whose main point is how the writer feels right now is a `reaction`, even
if it contains a number.

---

## The Dataset

**Where the posts came from:** All 205 rows are synthetic posts written to
read like r/EASPORTSWRC and the EA SPORTS WRC Steam discussions. Real-world
facts used in them come from public reporting:
[Ogier's ninth title (wrc.com)](https://www.wrc.com/en/news/ogier-savours-ninth-world-title-after-intense-season-finale),
[2025 season result (Toyota UK)](https://mag.toyota.co.uk/wrc-2025-result/),
[Hyundai's Rally Finland 2026 review](https://motorsport.hyundai.com/media-center/news/2026/wrc/23-review-rally-finland),
[WRC2 mid-season review (wrc.com)](https://www.wrc.com/en/news/mid-season-review-wrc2-title-fight-wide-open-after-first-half),
and [EA SPORTS WRC getting no more content (TheSixthAxis)](https://www.thesixthaxis.com/2025/04/30/ea-sports-wrc-will-get-no-more-content-future-rally-game-development-paused/).
The game-tuning posts (setups, times, settings) are invented first-person
experiences, not verified measurements.

**How I labelled them:** The posts were written to a target label, so each
label is the intended one rather than an independent judgement. The `note`
column marks every row as synthetic and says which kind:

| Note | Rows |
|---|---|
| `synthetic; written in r/EASPORTSWRC style` | 147 |
| `synthetic; grounded in real WRC result` | 46 |
| `synthetic; hard case` | 12 |

The cold-labelling step (20 posts labelled by hand with no help) was **not**
done, because I chose synthetic data to save time.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `analysis` | 70 | 34.1% |
| `hot_take` | 68 | 33.2% |
| `reaction` | 67 | 32.7% |
| **Total** | 205 | 100% |

**Three hard cases**

**1.**
> *The post:* "Toyota won 12 rallies last year and honestly the whole series
> is fixed for them. Everyone knows the rules are written around the Yaris."
>
> *Could have been:* `analysis` or `hot_take`
>
> *I chose, because:* `hot_take`. The 12 wins is a real number, but "fixed"
> doesn't follow from it, and the rules claim has no support at all.

**2.**
> *The post:* "My first sub-5 minute run on Monte stage 1!! 4:58! I've been
> trying for a week!!"
>
> *Could have been:* `reaction` or `analysis`
>
> *I chose, because:* `reaction`. The time is specific, but it isn't evidence
> for any claim; the post is celebrating.

**3.**
> *The post:* "Neuville in Finland: suspension damage AND a 20 second penalty
> for checking in late to service, on the same day. Two separate
> self-inflicted problems is why he ended up fifth."
>
> *Could have been:* `analysis` or `reaction`
>
> *I chose, because:* `analysis`. It's written like a heated reaction, but the
> two specific incidents are the reason given for the conclusion.

---

## The Training Run

**Base model:** `distilbert-base-uncased`

**Settings:** 3 epochs, learning rate `2e-5`, batch size 16, max length 128
tokens, seed 42.

**Anything I changed from the defaults, and why:** Nothing.

**Hardware:** CPU, PyTorch `2.14.1+cpu` (recorded in `results.json`).

**Split sizes:** 143 train, 31 validation, 31 test. Test split: 11
`analysis`, 10 `hot_take`, 10 `reaction`.

**Seed 42 held-out results:** accuracy `0.871`, macro F1 `0.865`. Per-label
F1: `analysis` `0.846`, `hot_take` `0.750`, `reaction` `1.000`. All four
errors were `hot_take` posts predicted as `analysis`. The most-confident third
was 1.000 accurate vs 0.700 for the least-confident third. These are numbers
on synthetic data only. The three-seed comparison is for Unit 6.

---

## How I Used AI

**Moment 1**

- *What I asked for:* Help turning the starter's three labels into
  definitions that fit WRC discussion, plus a decision rule for the
  `analysis`/`hot_take` boundary.
- *What came back:* The definitions above and the rule "a fact only makes it
  analysis if it's the reason for the conclusion".
- *What I changed:* I kept the starter's label names and focused the rule on
  posts that cite a real result but draw a conclusion it doesn't support.
  The criteria in `criteria.md` were also drafted with AI help.

**Moment 2**

- *What I asked for:* A synthetic dataset, because I didn't have time to
  collect and hand-label 200 real posts. I asked for it to use real WRC
  information and read like Reddit.
- *What came back:* 205 posts. About a quarter cite real 2025–2026 results
  found by web search, and 12 are deliberate borderline cases. A first,
  generic version scored 0.969, which told me the posts were too easy to tell
  apart, so it was replaced with this one.
- *What I changed:* I disclosed the synthetic source here and in every row's
  `note`, and report the model's numbers only as results on synthetic data.

**Pre-labelling disclosure:** All 205 posts and their labels were generated
together by AI. No real posts were pre-labelled, and I did not do the
cold-labelling pass.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy |  |  |  |
| Macro F1 |  |  |  |
| F1 — `label_one` |  |  |  |
| F1 — `label_two` |  |  |  |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
