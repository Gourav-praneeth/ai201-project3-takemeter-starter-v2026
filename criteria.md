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

## 1. Overall accuracy is at least 0.60 on the held-out set, in all three seeds

**Why this target:** With four labels a coin-flip guesser gets about 0.25, and
always guessing the biggest label would get roughly 0.35 to 0.40 on my data, so
0.60 is well clear of both. I didn't go higher because `analysis` and `opinion`
are separated by a judgement call (see the decision rule in the README) and I
only have about 200 comments. A held-out set of about 40 means one seed moves by
2.5 points per comment.

## 2. Every label has an F1 of at least 0.50 on the held-out set, in all three seeds

**Why this target:** Accuracy can hide a label the model never gets right. My
smallest label will probably be `analysis`, at about 30 to 40 comments, which
puts only 6 to 8 of them in the test split, so its F1 will jump around between
seeds. I set the floor at 0.50 for that reason, well below the 0.60 for overall
accuracy.

## 3. No label has fewer than 30 or more than 80 of the 200 comments

**Why this target:** Under 30 (15%) a label gets about 6 test examples, which
makes criterion 2 mostly noise. Over 80 (40%) the model can win by leaning on that
label. I expect `opinion` to be the biggest, since it is the default for any
comment with a stance, and `analysis` the smallest. If the labelled set falls
outside the range I'll collect more of the thin label from the pool, not trim the
big one.

## 4. On a cold relabel, I agree with my own earlier labels on at least 80% of 30 comments

**Why this target:** The taxonomy depends on a judgement call, so I need to know I
apply it the same way twice. I'll relabel 30 random comments at least two days
later without looking at my earlier labels, and check them with `agreement.py`.
80% allows about 6 disagreements out of 30, which is plausible given the
`analysis`/`opinion` boundary, and any disagreement above that means the
definitions need fixing before I trust the labels. I can't test a second person
here, so this is self-agreement and is likely higher than what two people would
get.

## 5. The most confident half of predictions is at least 10 points more accurate than the whole set

**Why this target:** If the model's confidence means nothing, a "sure" answer is
no better than an unsure one and I can't use it to decide what a person should
review. I'll rank the held-out predictions by top-label probability and compare
accuracy on the top half to overall accuracy. With about 40 test comments the top
half is 20 predictions, so a 10-point gap is 2 comments. I chose that low a bar on
purpose, since a bigger gap would be hard to tell from noise at this size.

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
