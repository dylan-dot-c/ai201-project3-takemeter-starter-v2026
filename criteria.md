# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

> **Disclosure:** This is an AI-assisted draft prepared for a synthetic
> practice dataset, not criteria personally developed from reading a real
> community sample. Review and rewrite the targets and reasons in your own
> words before treating them as your project criteria.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1. Overall accuracy

Across the three held-out test splits, accuracy is at least **0.65 on each
seed**.

**Why this target:** With three labels, a uniform random guess is right about
one time in three. Requiring 65% means the classifier must do substantially
better than guessing on these synthetic WRC and game-discussion posts.
This target still needs reconsideration against a real sample.

---

## 2. Macro F1

Macro F1 is at least **0.60 on each of the three seeds**.

**Why this target:** Macro F1 weights `analysis`, `hot_take`, and `reaction`
equally, so a strong score on one kind of example cannot hide a weak score on
another. The synthetic dataset is close to balanced and cannot establish how
frequently these types occur in the real WRC community.

---

## 3. Per-label performance

The F1 score for **each label is at least 0.45 on every seed**.

**Why this target:** A useful classifier should not effectively ignore one of
the three kinds of game discussion. The threshold is intentionally lower than
the macro-F1 target because each class has only about ten examples (10–11) in this
synthetic 15% test split, so a few borderline posts can move its F1
considerably.

---

## 4. Dataset balance

No one label accounts for more than **70%** of the labelled posts.

**Why this target:** If one label dominates, a model can look accurate by
repeatedly guessing it and still fail to distinguish analysis, unsupported
opinions, and personal reactions. The 70% ceiling is also the notebook's
explicit warning threshold. This synthetic dataset was intentionally balanced,
so passing this check does not show that a real Reddit sample is balanced.

---

## 5. Confidence

On each seed, accuracy in the most-confident third is at least **0.70** and is
at least **0.10 higher** than accuracy in the least-confident third.

**Why this target:** If the model is to be used to sort community posts, its
most certain predictions should be dependable. A ten-point gap asks confidence
to rank predictions meaningfully rather than merely producing high scores.

> **Revised in unit 6:** On each seed, accuracy in the most-confident third is
> at least **0.70**, and the least-confident third contains at least as many
> errors as the most-confident third. If a seed has no errors at all, the
> ranking part is reported as "not testable" rather than as a miss.
>
> **Why revised:** The ten-point gap can't be met by a model that makes no
> mistakes. After the improvement, all three seeds scored 1.000 in both
> thirds, so the gap was 0.000 and the original criterion says MISSED. That
> verdict says confidence failed to rank predictions, when really there was
> nothing to rank. The criterion couldn't measure what it was written to
> measure once accuracy hit the ceiling. The original verdicts stay as
> recorded in the README, and the 0.70 target is unchanged.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
