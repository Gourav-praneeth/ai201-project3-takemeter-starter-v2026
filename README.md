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

TakeMeter sorts comments from r/boardgames, a public subreddit with plenty of
text and no spoilers, by what kind of contribution they are. It has four labels:
`analysis` (an argument that rests on a checkable detail), `opinion` (a verdict
with only general support), `experience` (a story with no verdict) and
`logistics` (practical questions and answers). The aim is to tell a substantial
comment from a confident one, which a reader can't do from length or tone alone.
The comments come from the `boardgames` subset of the Hugging Face
`REDDIT_comments` dataset.



---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `analysis`

**Definition:** Argues a point whose main claim rests on a specific, checkable detail in the post (a named game, rule, number, event, or quoted passage).

**Example 1:**
> There are a lot of problems with this article. First, and most glaring: the author seems unaware that this isn't the first game in the Vast series. Vast: The Crystal Caverns came out two years ago. [...] What criteria are you using to define which games are like this one? [...] Where did you find data saying that many of these games are shunned by gamers for lacking variety?

**Example 2:**
> I wouldn't call it the *Kickstarter* period - better to talk about *crowdfunding*. I prefer *GMT*'s P500 - where the publisher still bears the brunt of the risk and the consumer can even retract their order before it ships out (i.e. they're not even charged until the last minute).

### `opinion`

**Definition:** States a view or verdict supported only by general reasoning, feeling, or the poster's own say-so, with nothing checkable that the claim depends on.

**Example 1:**
> I dunno who these people are that know their exact IQ and compare it with others...what a specifically odd way to try and feel good about yourself. An IQ is such a tiny insignificant part of a whole person - much healthier to work on improving social skills, coping and mental health wellness.  Focusing on a high IQ score may eventually leave you a lonely, albeit smart, person.

**Example 2:**
> This is a bit much. I don't really disagree with the first comment. And I also don't think the "you must be fun at parties" comment is mature. But, this is really over the top. No one is "sick" for being personally upset about a life situation that doesn't directly involve fearing for their lives. [...]

### `experience`

**Definition:** Narrates something that happened to the poster, or what they play or own, without arguing a verdict.

**Example 1:**
> I was always a boardgame player throughout my life. My friends and I played **Axis and Allies** in college a lot but as an adult my first purchase was either **Dominion** or **Ticket to Ride** I can't remember which but I've been hooked on modern games ever since.

**Example 2:**
> My earliest gaming memory is playing scrabble with my family when I was 7 or so. My mom would always get annoyed at my dad for pulling super obscure words from his head [...] Now, playing with him as an adult, I finally feel for her.

### `logistics`

**Definition:** Asks for, offers, or relays practical information (questions, recommendations, listings, how-to, answers), or is pure social glue (thanks, agreement, small talk), without arguing a position.

**Example 1:**
> If you aren't in a rush, the Bring n Buy Auction at Breakout will probably be your best bet

**Example 2:**
> **FS/FT:** Most of these are like new or played once and in great shape. Located in **Atlanta** [...] **Dogs of War (unopened, in shrink)** [...] **Sheriff of Nottingham** [...] **Want:** **Trade on the Tigris** **Paypal/cash**

### The hardest boundary

**Which two labels:** `analysis` and `opinion`

**The post that sits between them:**
> I think the theme and design is timeless. Portugal will always be known for those tiles and as the world marches on to become more and more modern and uniform, cultural heritage like that will become even more powerful. The actual game design is also timeless - people will always love to look at glossy cubes, touch them, hear the clack-clack. [...] The game has zero setup time, so it will always be quick on the table and therefore super suitable for mainstream masses. [...] And most importantly, the game is really hard to master.

It names concrete things (Portuguese tiles, zero setup time, the factories) and is
long, but its conclusion is "timeless", a judgement.

**The decision rule I used every time:** Find the post's main claim, then imagine
the checkable details in it are false. If the poster would have to withdraw or
change the claim, it's `analysis`. If they could shrug and keep it, it's `opinion`,
however long the post is or however many concrete features it lists. For the post above
(a comment about Azul), "timeless" survives even if the tiles and setup details were wrong, so
it's `opinion`. In the Vast post, "the author seems unaware of the earlier game"
collapses if the earlier game didn't exist, so it's `analysis`.

When a post fits two labels in general, label by what it is *for*: a verdict is
`opinion`, a question or pointer is `logistics`, and narration with no verdict is
`experience`.

---

## The Dataset

**Where the posts came from:** 335 comments from r/boardgames, taken from the
`boardgames` subset of the Hugging Face dataset
[HuggingFaceGECLM/REDDIT_comments](https://huggingface.co/datasets/HuggingFaceGECLM/REDDIT_comments)
(one parquet shard, public subreddit, collected in 2017 to 2018). Reddit and
BoardGameGeek blocked direct collection, so I used the dataset instead. These are
comments, not top-level posts. Three batches went into `labels.csv`:

1. **Rows 1 to 200:** a random pool, 50 comments from each of four length bands
   (12 to 25, 26 to 60, 61 to 120, 121 to 250 words). I dropped deleted and removed
   comments, bots, links, and anything under 12 or over 250 words.
2. **Rows 201 to 275:** 75 comments from the same shard, chosen by a keyword filter
   (quotes, numbers, "because", "rulebook", "edition") because the first 200 held
   only about 15 `analysis` comments.
3. **Rows 276 to 335:** 60 more from the same shard with a stricter filter
   (a quote or number plus a rule or expansion word), for the same reason.

Batches 2 and 3 were pulled to find more `analysis`. Both used a filter and not a
random draw, so the file over-represents comments that cite rules, numbers and
quotes, and the label shares below are not the community's natural mix. The source
permalink for every row is in `data/labels_source.csv`, in the same order.

**How I labelled them:** Claude (an AI tool) read all 335 comments and labelled
every row from my written definitions and decision rule. Every row's `note` says
`ai-prelabel`. **I have not yet read and corrected them.** I did not label any
comments cold. The `note` on 20 randomly chosen rows says
`ai-prelabel, cold slot: relabel unaided`, and `data/cold_20_blank.csv` lists
those 20 with no label. I will label them unaided before looking at the AI
labels, then set `note` to `cold` on those rows and use them for the agreement
report. Reading the AI labels did lead me to widen `logistics` to cover social
glue (thanks, agreement, bare questions), since many replies fit none of the four
definitions.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `opinion` | 122 | 36.4% |
| `logistics` | 117 | 34.9% |
| `experience` | 65 | 19.4% |
| `analysis` | 31 | 9.3% |
| **Total** | 335 | 100% |

No label is above 70%. `analysis` is the thin one. It is about 9% of the random
batch too, so I think it is genuinely rare in this community and I could only get
to 31 with the filtered batches.

**Three hard cases**

**1.**
> *The post:* "Generally, with how low the designer's profit margins are on board games combined with the amount of work that goes into design and distribution, the profit per hour that goes to the designer is typically less than minimum wage for all but the most successful games.  It's a cutthroat market full of passion projects, games are priced to break even rather than make a profit."
>
> *Could have been:* `analysis` or `opinion`
>
> *I chose, because:* `opinion`. It sounds like an argument about economics, but it gives no figure, source or named example. The claim "less than minimum wage" would stand unchanged if I swapped in any numbers, so it fails the delete-the-detail test.

**2.**
> *The post:* "Been out of print for about 1-2 years now.   This was probably the clue that AEG decline renewing licenses for variois Love Letters as they probably planned to sell the Love Letter license to someone else. I dont see Zman as the company to get franchise products, especially after the mystery from Star Wars Carcassonme never getting an official US release."
>
> *Could have been:* `analysis` or `opinion`
>
> *I chose, because:* `analysis`, but only just. It names checkable facts (out of print for 1 to 2 years, AEG and Z-Man, the Star Wars Carcassonne release) and the inference leans on them. But "probably" shows it is speculation. I kept the label because if the out-of-print date were wrong the inference would have to go.

**3.**
> *The post:* "The Grimm Forest has been my most annoying game of 2018. The fact that everyone can see what you are doing and what you are after means that making a meaningful decision more often than not goes bad for you. The first play I did was going horribly bad, then I just gave up and started selecting my locations randomly. [...] The second play I did I played this way from the start, and won before anyone else had their second building complete."
>
> *Could have been:* `experience` or `opinion`
>
> *I chose, because:* `experience`. Most of the comment narrates two plays and what happened. But it opens with a verdict ("most annoying game"), which under my tie-break would make it `opinion`. I went with the narration because it is most of the text. This is the kind of row I expect to disagree with myself on when I relabel.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:**

**Settings:** <!-- epochs, learning rate, batch size, seed -->

**Anything I changed from the defaults, and why:**

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Moment 2**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Pre-labelling disclosure:**

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
