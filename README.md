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

**Moment 3 (Unit 6)**

- *What I asked for:* Run the baseline, the three seeds and the confusion
  matrix, and work out why `hot_take` was the weakest label.
- *What came back:* The AI retrained each seed and printed the 8 posts the
  model got wrong. That showed they were plain, unambiguous hot takes
  predicted at 0.35–0.43 confidence, which pointed at under-training rather
  than labelling and led to the epochs change.
- *What I changed:* I kept the improvement to one setting (epochs) as the
  unit requires, and reported criterion 5 as MISSED after the change rather
  than reading it generously.

**Moment 4 (Unit 6)**

- *What I asked for:* Label the 30 staff posts under the staff taxonomy.
- *What came back:* `my_staff_labels.csv`, with the rule used on each
  borderline post (listed in the Agreement Report).
- *What I changed:* Nothing yet. These labels were AI-assisted, like
  `labels.csv`, so the agreement rate will measure that same labelling
  process.

**Pre-labelling disclosure:** All 205 posts and their labels were generated
together by AI. No real posts were pre-labelled, and I did not do the
cold-labelling pass.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

Zero-shot `facebook/bart-large-mnli` given only `label_definitions.txt`, scored
on the same 31 posts in `test_split.csv` as the trained model (seed 42, the
3-epoch Unit 5 run).

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.355 | 0.871 | +0.516 |
| Macro F1 | 0.175 | 0.865 | +0.691 |
| F1 — `analysis` | 0.524 | 0.846 | +0.322 |
| F1 — `hot_take` | 0.000 | 0.750 | +0.750 |
| F1 — `reaction` | 0.000 | 1.000 | +1.000 |

**What I predicted before I looked:** The trained model's Unit 5 numbers were
already known, so this isn't a blind prediction. Before running the baseline,
I expected it to do best on `analysis`, because the posts with numbers and
names look the most "argued", and worst on `hot_take` vs `reaction`, because
both are short and unsupported.

**What the gap actually means:** The baseline didn't find the task hard in an
interesting way. It labelled **all 31 posts `analysis`**, so its 0.355 is
just the share of `analysis` posts in the test split. My definitions all
mention WRC, the game and the writer's reasoning, and the zero-shot model
apparently read every post as "about WRC with a point to make".

I also ran it once with **bare label names** (no definitions file):

| Measure | Baseline, bare names | Baseline, my definitions |
|---|---|---|
| Overall accuracy | 0.484 | 0.355 |
| Macro F1 | 0.438 | 0.175 |
| F1 — `analysis` | 0.167 | 0.524 |
| F1 — `hot_take` | 0.516 | 0.000 |
| F1 — `reaction` | 0.632 | 0.000 |

With bare names it called 21 of 31 posts `hot_take`. So my definitions made
the zero-shot baseline **worse** by 0.129 accuracy. They work for a person
deciding between labels, but as a prompt for an entailment model every post
matches the `analysis` definition. Fine-tuning added a lot here (+0.52
accuracy), but it was beating a baseline that was close to always guessing
one label.

---

## Run Log — Before

Seeds 42, 7 and 2024. Notebook defaults: 3 epochs, lr 2e-5, batch 16, max
length 128. Device: CPU (PyTorch 2.14.1+cpu). Test split is 31 posts per seed
(10–11 per label).

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥ 0.65 every seed | 0.871 | 0.968 | 0.903 | MET |
| 2. Macro F1 | ≥ 0.60 every seed | 0.865 | 0.967 | 0.896 | MET |
| 3. Every label's F1 (lowest shown) | ≥ 0.45 every seed | 0.750 (`hot_take`) | 0.947 (`hot_take`) | 0.824 (`hot_take`) | MET |
| 4. Largest label share | ≤ 70% | 34.1% | 34.1% | 34.1% | MET |
| 5. Confidence: top third ≥ 0.70 and ≥ 0.10 above bottom third | both | 1.000 vs 0.700 (gap 0.30) | 1.000 vs 0.900 (gap 0.10) | 1.000 vs 0.800 (gap 0.20) | MET |

Spread across seeds: accuracy moved 0.097 and `hot_take` F1 moved 0.197. That
is just under the notebook's ten-point warning line, so a target that landed
inside that band couldn't really be called either way.

### Confusion matrix

Seed 42, printed by section 7:

| true \ predicted | analysis | hot_take | reaction |
|---|---|---|---|
| **analysis** | 11 | 0 | 0 |
| **hot_take** | 4 | 6 | 0 |
| **reaction** | 0 | 0 | 10 |

Summed across all three seeds (93 test posts):

| true \ predicted | analysis | hot_take | reaction |
|---|---|---|---|
| **analysis** | 33 | 0 | 0 |
| **hot_take** | 5 | 22 | 3 |
| **reaction** | 0 | 0 | 30 |

**My biggest off-diagonal number, and what it means:** 4 real `hot_take`
posts were called `analysis` on seed 42, and none went the other way. Across
all three seeds, **every one of the 8 errors was a true `hot_take`**: 5 went
to `analysis` and 3 to `reaction`. The model never mislabelled a real
`analysis` or `reaction` post. It hadn't learned what a `hot_take` is, only
what the other two look like, and it put plain confident claims into
whichever of those they resembled on the surface.

---

## Verdicts and Diagnoses

Against the Unit 5 targets, before the improvement:

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Overall accuracy | ≥ 0.65 every seed | MET | Lowest seed was 0.871. |
| 2 | Macro F1 | ≥ 0.60 every seed | MET | Lowest seed was 0.865. |
| 3 | Per-label F1 | ≥ 0.45 every label, every seed | MET | Lowest of all nine values was `hot_take` 0.750 on seed 42. |
| 4 | Balance | no label > 70% | MET | Largest label is `analysis` at 70/205 = 34.1%. |
| 5 | Confidence | top third ≥ 0.70 and ≥ 0.10 above bottom third | MET | Top third was 1.000 on all seeds. Seed 7's gap is exactly 0.10 (10/10 vs 9/10), so it meets "at least 0.10" with nothing to spare. One more error in the top third would have made it a miss. |

**Diagnoses**

No criterion was missed before the improvement, so there's no miss to
diagnose. What I can diagnose is the **weakest label**, `hot_take`, which was
lowest on all three seeds and accounts for all 8 errors. These are the posts
it got wrong (found by retraining each seed with the notebook's functions):

| Seed | Post | Predicted | Model confidence |
|---|---|---|---|
| 42 | "Toyota drivers only win because of the car. Put them in a Hyundai and they're midfield." | `analysis` | 0.43 |
| 42 | "Fourmaux is the fastest driver in the world right now." | `analysis` | 0.39 |
| 42 | "Co-drivers deserve as much credit as drivers and the titles should reflect that." | `analysis` | 0.41 |
| 42 | "Kenya is the worst rally on the calendar and it should be dropped." | `analysis` | 0.39 |
| 7 | "Pajari will be world champion within two years. Book it." | `reaction` | 0.35 |
| 2024 | "Night stages are a gimmick and add nothing." | `reaction` | 0.38 |
| 2024 | "Setups don't matter. Anyone saying otherwise is coping." | `reaction` | 0.39 |
| 2024 | "Fourmaux won SS1 in Finland which proves he's the fastest driver alive, period." | `analysis` | 0.41 |

**Cause: the model was under-trained, not the labels inconsistent.** Evidence:

1. **The posts aren't borderline.** Seven of the eight are textbook
   `hot_take`s with no fact in them at all. Only the Fourmaux SS1 post is one
   of my 12 deliberate hard cases. If this were a labelling problem, the
   errors would cluster on hard cases, and they don't.
2. **The model was barely more confident than a coin toss.** Every wrong
   answer had a top probability of 0.35–0.43, against 0.33 for a pure
   three-way guess. Mean confidence was only 0.41–0.44 even on correct
   answers.
3. **Training loss stopped at about 0.96.** An untrained three-label model
   starts at ln 3 ≈ 1.10, so three epochs on 143 posts moved it very little.
   Validation loss was still falling at every epoch on all three seeds (seed
   42: 1.01 → 0.91 → 0.86), so training stopped before it levelled off.
4. **The direction matches surface cues.** Four of the five errors sent to
   `analysis` name a team, driver or rally (Toyota, Fourmaux twice, Kenya),
   as nearly all `analysis` posts do. The three sent to `reaction` are short and
   punchy, like most `reaction` posts. An under-trained model leans on cues
   like that.

**What the agreement report can and can't add:** it's the instrument that
separates a labelling problem from a model problem, but the staff answer key
hasn't come back from my TF yet (see below). My 30 staff labels were made the
same way my `labels.csv` was, so once the rate is in it tests that labelling
process directly. Until then, the evidence above is what the diagnosis rests
on.

---

## Agreement Report

**Status:** I labelled all 30 staff posts under `data/staff_taxonomy.md` and
committed them as `my_staff_labels.csv`, without seeing the staff labels.
`data/staff_labels.csv` comes from the TF, and **I haven't received it yet**,
so `python agreement.py` hasn't been run.

**Agreement rate:** ___ / 30 = ___% *(pending staff labels)*

**How my 30 were labelled:** like `labels.csv`, they were labelled with AI
help (see How I Used AI). That makes this a fair check of the process that
produced my training labels. These are the posts where I had to use a rule,
so they're where I expect any disagreements:

| Post | My label | Rule I used |
|---|---|---|
| sp_005 "Carbon plates aren't the whole story. My times dropped 2%…" | `analysis` | Rule 1: the second 2% drop, in the same shoes, is the reason for the conclusion. |
| sp_007 "Ran the same loop in 4C and in 24C…" | `analysis` | Rule 3: personal and recent, but the 90 seconds is the argument. |
| sp_010 "Three of us ran the same session…" | `analysis` | Rule 1: a sample of three, but the 10-second comparison carries the claim. |
| sp_014 "Times are down about 3% since the shoe rules changed, so obviously…" | `hot_take` | Rule 1: almost the staff's own example. The 3% is decoration on a conclusion already reached. |
| sp_019 "Nobody actually enjoys the long run…" | `hot_take` | Rule 2: a general claim about everyone, not the writer's own experience. |
| sp_021 "Four minutes off. FOUR…" | `reaction` | Rule 2: the number is the event, and the post is about how it felt. |
| sp_027 "Wind was so bad on the out section I was leaning at about 20 degrees…" | `reaction` | The 20 degrees is description, not evidence for a claim. |

**Disagreements**

*To fill in once `agreement.py` has run: three lines per disagreement (the
post, both labels, my call and why from the staff rules), then the pile:
(a) applied loosely, (b) rule doesn't say, (c) ambiguous and my reading is
defensible.*

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:** *(pending)*

---

## The Improvement

**What I changed:** One training setting. `EPOCHS` went from **3 to 8** in
section 2 of the notebook. Labels, taxonomy, data and criteria are unchanged.

**Which diagnosis pointed at it:** The `hot_take` diagnosis above. The
errors were confident-sounding but unambiguous posts, made at close to chance
confidence, with training loss barely below its starting value. That's an
under-trained model, not mislabelled data. Relabelling would have changed
posts that were already labelled correctly, and adding more `hot_take`s would
have left the same three-epoch budget to learn from them.

### Run Log — After

Same seeds, same splits, same device. Only `EPOCHS = 8` changed.

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥ 0.65 every seed | 1.000 | 1.000 | 1.000 | MET |
| 2. Macro F1 | ≥ 0.60 every seed | 1.000 | 1.000 | 1.000 | MET |
| 3. Every label's F1 (lowest shown) | ≥ 0.45 every seed | 1.000 | 1.000 | 1.000 | MET |
| 4. Largest label share | ≤ 70% | 34.1% | 34.1% | 34.1% | MET |
| 5. Confidence: top third ≥ 0.70 and ≥ 0.10 above bottom third | both | 1.000 vs 1.000 (gap 0.00) | 1.000 vs 1.000 (gap 0.00) | 1.000 vs 1.000 (gap 0.00) | MISSED |

Confusion matrix after (seed 42; seeds 7 and 2024 are also all on the
diagonal):

| true \ predicted | analysis | hot_take | reaction |
|---|---|---|---|
| **analysis** | 11 | 0 | 0 |
| **hot_take** | 0 | 10 | 0 |
| **reaction** | 0 | 0 | 10 |

**Did it help, and how do I know:** Yes, on the problem it was aimed at.
All 8 `hot_take` errors are gone on all three seeds, including the Fourmaux
SS1 hard case. Mean confidence on correct answers rose from 0.41–0.44 to
0.74–0.77, and final training loss fell from about 0.96 to 0.56–0.61. That's
what fixing under-training should look like. The cost is time: each seed took
about 170 s to train instead of about 65 s on CPU.

Two caveats. First, criterion 5 went from MET to **MISSED**. The model didn't
get worse; with zero errors the bottom third can't be 0.10 below the top
third. Its diagnosis is under What's Still Broken, and the criterion revision
is in `criteria.md`. Second, 1.000 on 31 synthetic posts says the synthetic
task is easy to separate. It doesn't say the classifier is perfect.

---

## What's Still Broken

**Criterion 5 (confidence), MISSED after the improvement.** Cause: a ceiling
effect, not a model fault. With 31/31 correct on every seed there is no error
for confidence to rank, so the gap is 0.000 by construction. I didn't lower
the target. I added a revision underneath the original in `criteria.md`,
because the criterion as written can't tell "confidence doesn't rank errors"
apart from "there are no errors". To actually test it I'd need a harder test
set where the model makes mistakes, such as real r/EASPORTSWRC posts.

**My targets were set too easily for this data.** Every accuracy and F1
criterion passed on every seed even before the improvement. That's partly
because the data is synthetic: posts were written to a target label, so
`analysis` posts tend to be long with numbers, `hot_take`s short and flat,
and `reaction`s exclamatory. I'm not revising those targets, since they were
measurable and passing isn't a reason to revise. The fix is real data, not
new numbers.

**The agreement report is incomplete** until the TF sends
`data/staff_labels.csv`. Then: run `python agreement.py`, commit
`agreement_results.json`, and adjudicate each disagreement above.

**The gap between what I meant my labels to capture and what the model
learned:** I meant `analysis` vs `hot_take` to turn on whether a fact carries
the argument. The before-matrix shows the 3-epoch model instead leaned on
whether a post *mentions* a driver, team or rally: 4 of its 8 errors were
plain claims naming one, sent to `analysis`. The 8-epoch model gets every
test post right, but with only one decorative-fact hard case across the test
splits, I can't show it learned "load-bearing fact" rather than "long post
with numbers". Synthetic posts don't contain enough of the hard pattern to
tell the difference.


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
