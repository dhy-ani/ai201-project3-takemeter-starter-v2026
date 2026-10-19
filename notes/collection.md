# Milestone 4 — How the 200 posts were collected

**Source:** public r/tennis comments, Oct 2025 – Oct 2026, pulled from the [Arctic Shift](https://arctic-shift.photon-reddit.com/) public Reddit archive. Reddit's own JSON API returned 403 without an account, and its RSS feeds were rate-limited to a few threads per half hour, so I switched to the archive.

**Pull:** 26 two-week windows across the year, 100 comments each → 2,600 comments.

**Filtering:**
- Dropped AutoModerator, `[deleted]`, and `[removed]` comments
- Kept 40–1,500 characters and at least 8 words (outside URLs)
- Removed exact duplicates (case and punctuation ignored)
- Left 1,757 candidates

**Sampling (seed 2026):**
- At most 6 comments from any one thread, so no single match thread dominates
- 123 distinct threads in the final 200
- Because `conditional` and `metrics` are rare in random posts (see `notes/labels.md`), I deliberately oversampled before filling the rest at random:
  - up to 25 comments containing two or more "if"s
  - up to 60 comments containing a percentage or a 2+ digit number
- These are only surface patterns. A comment with two "if"s isn't necessarily `conditional`, and the labels decide that.

**Whole posts:** comment text is kept in full, with no trimming.

**`labels.csv` columns:** `text`, `label`, `note`, plus a `source` permalink for each comment. The notebook ignores extra columns.

## Rebalancing with a targeted search

**The problem.** After pre-labelling, the random 200 came out as:

| Label | Count | Share |
|---|---|---|
| `reaction` | 192 | 96% |
| `metrics` | 6 | 3% |
| `conditional` | 2 | 1% |

That's far over the brief's 70% cap.

**The search.** I ran targeted keyword searches of r/tennis comments on Arctic Shift, Oct 2025 – Oct 2026:
- `metrics` queries: "first serve", "aces", "break points"
- `conditional` queries: "if he wins/loses/reaches", "qualifies if", "scenarios", "clinch", "needs to win", "if both", and similar

I kept only candidates that passed the same length and duplicate filters as before, then read each one against the written rules. A candidate went in only if it met the rule:
- `conditional`: two explicit outcome branches about a match, draw, ranking, or qualification
- `metrics`: an explicit percentage, box-score count, serve speed, or ranking-point value

**The swap.**
- 45 `metrics` and 9 `conditional` posts went in, marked `prelabeled; targeted search` in `note`.
- The same number of pre-labelled `reaction` rows came out: the mod-removal template first, then a seeded random draw (seed 2026).
- The 20 cold rows were kept.

**Final counts (200 rows):**

| Label | Count | Share |
|---|---|---|
| `reaction` | 138 | 69.0% |
| `metrics` | 51 | 25.5% |
| `conditional` | 11 | 5.5% |

No more than 7 rows come from any one thread, and there are no duplicates.

**Caveats**
- **`conditional` is rare.** It's genuinely scarce in r/tennis. Across roughly 1,500 targeted candidates, only about 1 in 50 had two real outcome branches. The final count of 11 (5.5%) is below criterion 4's 8% floor.
- **Topic bias.** Targeted rows come from keyword searches, so some `metrics` posts cluster in live match threads. The model may partly learn "live match thread" vs. general discussion, the same thread-topic risk that got biomechanics dropped.
