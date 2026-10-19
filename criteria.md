# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

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

## 1. Overall accuracy ≥ 0.72

Checked against `accuracy` in `results.json`, on every one of the three seeds.

**Why this target:** `reaction` is ~65–70% of random r/tennis posts, and criterion 4 caps it at 70%, so a model that always guesses `reaction` scores at most 0.70. A threshold of 0.72 requires the model to beat that majority-class baseline rather than learn the class distribution.

---

## 2. Macro F1 ≥ 0.55

Checked against `f1_macro` in `results.json`, on every one of the three seeds.

**Why this target:** Macro F1 weights all three labels equally, so the model cannot pass by predicting `reaction` well while failing on `metrics` and `conditional`.

---

## 3. Per-label F1: `reaction` ≥ 0.75, `metrics` ≥ 0.50, `conditional` ≥ 0.35

Checked against `per_label.<label>.f1` in `results.json` for each of the three labels, on every one of the three seeds.

**Why this target:** The thresholds follow each label's support. With roughly 140 `reaction`, 44 `metrics` and 16 `conditional` posts, `conditional` will have only 2–3 posts in the test split, so one miss moves its F1 a lot; its target is looser for that reason. Separate thresholds give every label a clear pass/fail instead of letting macro F1 average a failing label away.

---

## 4. Label balance: largest label ≤ 70%, smallest label ≥ 8%

Checked against the label counts printed by the notebook's section 2 data check (`df["label"].value_counts(normalize=True)` over the full `labels.csv`).

**Why this target:** 70% is the brief's cap — above it the notebook flags `[FIX ME]` and the model learns to guess the majority. The 8% floor means at least 16 of 200 posts are `conditional`, which keeps the smallest label from being starved in the ~30-post test split.

---

## 5. Agreement with the staff labels ≥ 80%

Checked against the `agreement` rate printed by `python agreement.py` (unit 6), comparing my labels on the 30 staff posts to the staff labels.

**Why this target:** The agreement check is the only measure of whether I apply a written rule the way its authors intended. My own decision rules are built on textual triggers (a `%` sign, box-score numbers, two explicit "if" branches), so I expect rule-following to be mostly mechanical; under 80% would mean I'm reading intent into posts instead of applying the rule.

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
