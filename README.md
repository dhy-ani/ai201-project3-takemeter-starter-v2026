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

TakeMeter sorts public comments from [r/tennis](https://www.reddit.com/r/tennis/), the main Reddit community for pro ATP/WTA tennis, by the kind of support a post offers.

| Label | What the post does |
|---|---|
| `conditional` | Lays out two or more explicit "if X, then Y" outcome branches |
| `metrics` | Cites an explicit performance number |
| `reaction` | Opinion, narrative, or a hot take with neither |

It's a fine-tuned DistilBERT classifier trained on 200 hand-checked comments.

---

## Label Taxonomy

Labels apply in priority order: `conditional` → `metrics` → `reaction`. The first rule a post meets wins. Full notes, the stress test, and the dropped fourth label are in [notes/labels.md](notes/labels.md).

### `conditional`

**Definition:** A post that contains two or more explicit branching outcome paths ("if X then Y; if not / otherwise Z") mapping tournament draw progression, ATP/WTA ranking-point math, or qualification scenarios. A single-branch hypothetical ("If he fixes his second serve he'll win a slam") doesn't count.

**Example 1:** ([Understanding ATP Points System](https://www.reddit.com/r/tennis/comments/7y6dot/understanding_atp_points_system/))
> If Roger does not compete at Wimbledon this year, the points he gained from Wimbledon last year are lost because 52 weeks have passed (meaning he would have dropped 2000 points and gained 0 as he did not compete).

**Example 2:** ([Question about ATP Rankings and Live Race Rankings](https://www.reddit.com/r/tennis/comments/qgwk8s/question_about_atp_rankings_and_live_race_rankings/))
> They count the 19 best tournament points plus Tour Finals, if you qualify... In a normal year, at the end of the year the race points will equal the ranking points.

### `metrics`

**Definition:** A post that contains at least one explicit quantitative performance value: a percentage, a box-score stat (aces, break points, winners/unforced errors), a serve speed, or an exact ranking-point value.
- Hedged numbers ("about", "~") still count.
- General counts and ordinals ("lost 5 finals", "top 5 at 20", "straight sets") don't count.

**Example 1:** ([r/tennis Daily Discussion, Jul 13 2026](https://www.reddit.com/r/tennis/comments/1uv451z/rtennis_daily_discussion_monday_july_13_2026/))
> Sinner only gave up 22 break point opportunities throughout the tournament, conceding 6 breaks of serve. Since we've started tracking this data, there have only been 11 majors (men's singles) where the champion has been less troubled on serve (<22 bps faced).

**Example 2:** ([Current Top 10 Players H2H Analysis](https://www.reddit.com/r/tennis/comments/1r4loag/current_top_10_players_h2h_analysis_atp_wta/))
> Alcaraz, Sinner, and Djokovic have the best performance against all Top 10 players with around 70% winning rate with more than 60 games played against this list of players.

### `reaction`

**Definition:** The default. Impressionistic observations, qualitative stroke commentary, narrative, rhetorical hypotheticals, or hot takes, with no branching scenario logic and no performance metric.

**Example 1:**
> Alcaraz plays with so much joy and energy, honestly win or lose he's just incredible to watch.

**Example 2:**
> Medvedev's deep return position works on hard courts but on clay it just leaves way too much court open for short angles.

### The hardest boundary

**Which two labels:** `conditional` vs. `metrics`

> Swiatek's won 78% of her first-serve points on clay this year. If she holds that at Roland Garros she's the favourite, if it drops under 70 like Rome she's beatable.

**The decision rule I used every time:** If a post has two or more explicit outcome branches, it is `conditional`, even when it also contains stats. Stats with zero or one branch make it `metrics`. Ranking ordinals, ages, and set or title counts are never metrics.

---

## The Dataset

**Where the posts came from:** Public r/tennis comments from Oct 2025 – Oct 2026, pulled from the Arctic Shift public Reddit archive. Reddit's own API returned 403 without an account.
- **Random sample (200):** 2,600 comments across 26 two-week windows, filtered for bots, deleted posts, length, and duplicates, then sampled with at most 6 per thread.
- **Rebalancing:** that sample came out 96% `reaction`, so I swapped in 54 posts from targeted keyword searches ("first serve", "aces", "break points", "if he wins", "qualifies if", "scenarios"…). Each one was checked against the rules above.

Full method: [notes/collection.md](notes/collection.md).

**How I labelled them:**
- I labelled the first 20 rows cold, with no tools (`note` = `cold`).
- The other 180 were pre-labelled by AI under my written rules (`note` = `prelabeled` or `prelabeled; targeted search`).
- `labels.csv` also has a `source` column with each comment's permalink.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `reaction` | 138 | 69.0% |
| `metrics` | 51 | 25.5% |
| `conditional` | 11 | 5.5% |
| **Total** | 200 | 100% |

`conditional` is under my own 8% floor (criterion 4). Two-branch scenario posts are rare even in targeted searches, at roughly 1 in 50 candidates.

**Three hard cases**

**1.**
> *The post:* "huge win for coleman! taking down the world number 29 in straight sets is a massive confindence booster. Hopefully he can keep that exact same momentum going into the quarterfinals"
>
> *Could have been:* `metrics` or `reaction`
>
> *I chose, because:* `metrics` in my cold pass, because "world number 29" and "straight sets" felt like quantitative values. Afterwards I settled the rule the other way: ranking ordinals and set counts are general counts, so they're `reaction`. I left the cold label unchanged as the honest record of my first pass. This is the boundary I'm least consistent on.

**2.**
> *The post:* "Alcaraz before Sinner? How's that make sense? He's been playing after him? Why switch it now? Now Carlos has less than 24 hours. Sinner has over… 🤔"
>
> *Could have been:* `conditional` or `reaction`
>
> *I chose, because:* `reaction`. It deals with scheduling and time math, but it has no two explicit outcome branches, so it's a rhetorical complaint.

**3.**
> *The post:* "I played tennis for 10 years as a junior and can't fathom creating any sort of power from this position. I'd be poking that into the bottom of the net every time"
>
> *Could have been:* the dropped biomechanics label, or `reaction`
>
> *I chose, because:* `reaction`. It's about stroke position, which biomechanics would have taken. With that label dropped (it tracked thread topic, not substance), qualitative technique talk is `reaction`. It also has a number ("10 years") that isn't a performance stat.

More hard cases from the pre-label pass are in [notes/hard_cases.md](notes/hard_cases.md).

---

## The Training Run

**Base model:** `distilbert-base-uncased`

**Settings:** 3 epochs, learning rate 2e-5, batch size 16, max length 128 tokens, seed 42.

**Device:** `cpu`, recorded in `results.json` (torch 2.14.1+cpu).

**Anything I changed from the defaults, and why:** Nothing. I left every hyperparameter at the notebook default.

**Split sizes:** train 139 · val 31 · test 30 (stratified 70/15/15).

| Label | Train | Val | Test |
|---|---|---|---|
| `conditional` | 7 | 2 | **2** |
| `metrics` | 36 | 8 | **7** |
| `reaction` | 96 | 21 | 21 |

`conditional` has only 2 test examples and `metrics` has 7, both under about 8, so their F1 will swing heavily between seeds.

The first run predicted `reaction` for every test post:
- accuracy 0.700, macro F1 0.275
- F1 0.000 for `conditional` and `metrics`

I haven't changed anything in response. Diagnosing it is next unit's work.

---

## How I Used AI

**Moment 1: stress-testing the label definitions**

- *What I asked for:* Eight posts that sit on the boundaries between my draft labels, to classify with my definitions as written.
- *What came back:* Eight posts. Labelling them showed I was deciding two of them by tone, not by my written rule:
  - "Bro has lost 5 straight finals" — I called it `reaction` because it was an insult.
  - "Rune was top 5 at 20" — I called it `reaction` because it was narrative.
  - My `metrics` definition said "verified", which a labeller can't check.
- *What I changed:*
  - Dropped the biomechanics label.
  - Added `reaction` as the default.
  - Required two explicit branches for `conditional`.
  - Replaced "verified" with an explicit rule for approximate numbers.
  - Added a written rule that counts and ordinals aren't metrics.

**Moment 2: checking my acceptance criteria were checkable**

- *What I asked for:* Whether someone who couldn't ask me could check each criterion, plus one "too easy" and one "too hard" reason per target.
- *What came back:* Three of my five couldn't be checked as written:
  - My balance cap (75%) conflicted with the notebook's 70% check.
  - Cohen's kappa isn't something `agreement.py` reports, and no second person labels my 200 posts.
  - Precision above 0.85 confidence isn't in `results.json`.
- *What I changed:* I rewrote each criterion against what the tools actually report: the 70% cap, the raw agreement rate from `agreement.py`, and the `results.json` fields. I picked the numbers.

**Pre-labelling disclosure:**
- Rows 21–200 (`note` = `prelabeled`) were pre-labelled by Claude (Claude Code), applying my written rules from `notes/labels.md`.
- Claude also collected the posts, wrote the collection scripts, and ran the targeted search and swap-in for the 54 `prelabeled; targeted search` rows.
- The 20 `cold` rows are mine alone.

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
