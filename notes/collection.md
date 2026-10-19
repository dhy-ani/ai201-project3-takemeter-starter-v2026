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
