# Milestone 2 — Label taxonomy

Labels are applied in priority order: the first one whose textual rule matches wins. CSV strings are exactly `conditional`, `metrics`, `reaction` (lowercase).

## 1. `conditional` (priority 1)

**Definition:** A post contains two or more explicit branching outcome paths ("if X then Y; if not / otherwise Z") mapping tournament draw progression, ATP/WTA ranking point math, or qualification scenarios.

**Textual rule:** Must contain at least two explicit conditional outcome branches. Single-branch rhetorical hypotheticals ("If he fixes his second serve he'll win a slam") do not qualify and fall to `metrics` or `reaction`.

- [Understanding ATP Points System](https://www.reddit.com/r/tennis/comments/7y6dot/understanding_atp_points_system/)
  > "If Roger does not compete at Wimbledon this year, the points he gained from Wimbledon last year are lost because 52 weeks have passed (meaning he would have dropped 2000 points and gained 0 as he did not compete)."
- [Question about ATP Rankings and Live Race Rankings](https://www.reddit.com/r/tennis/comments/qgwk8s/question_about_atp_rankings_and_live_race_rankings/)
  > "They count the 19 best tournament points plus Tour Finals, if you qualify... In a normal year, at the end of the year the race points will equal the ranking points."

## 2. `metrics` (priority 2)

**Definition:** A post contains at least one explicit quantitative performance value — percentages (%), box-score match statistics (aces, break points, winner/unforced error totals), serve speeds, or exact ranking point values.

**Textual rules:**
- **Approximations:** numbers with hedging qualifiers (about, around, roughly, ~) still count, as long as they are a stat, percentage, or degree measurement.
- **General counts and ordinals:** non-box-score counts ("lost 5 finals", "top 5 at 20 years old", "3 sets") do not trigger `metrics` and default to `reaction` unless tied to percentage or point math.

- [r/tennis Daily Discussion, Jul 13 2026](https://www.reddit.com/r/tennis/comments/1uv451z/rtennis_daily_discussion_monday_july_13_2026/)
  > "Sinner only gave up 22 break point opportunities throughout the tournament, conceding 6 breaks of serve. Since we've started tracking this data, there have only been 11 majors (men's singles) where the champion has been less troubled on serve (<22 bps faced)."
- [Current Top 10 Players H2H Analysis](https://www.reddit.com/r/tennis/comments/1r4loag/current_top_10_players_h2h_analysis_atp_wta/)
  > "Alcaraz, Sinner, and Djokovic have the best performance against all Top 10 players with around 70% winning rate with more than 60 games played against this list of players."

## 3. `reaction` (priority 3, default)

**Definition:** A post consists of impressionistic observations, qualitative stroke commentary, narrative statements, rhetorical hypotheticals, or hot takes that contain no branching scenario logic and no box-score/percentage performance metrics.

- > "Alcaraz plays with so much joy and energy, honestly win or lose he's just incredible to watch."
- > "Medvedev's deep return position works on hard courts but on clay it just leaves way too much court open for short angles."

## Hardest boundary: `conditional` vs. `metrics`

**Borderline post:**
> "Swiatek's won 78% of her first-serve points on clay this year. If she holds that at Roland Garros she's the favourite, if it drops under 70 like Rome she's beatable."

**Decision rule:** If a post has two or more explicit outcome branches, it is `conditional`, even when it also contains stats. Stats alone, with zero or one branch, make it `metrics`.

## Dropped label: biomechanical analysis

Originally a fourth candidate: posts naming a body part tied to a stroke phase ("arm is bent at contact"). Dropped because it appears almost only in technique threads, so the model would learn thread topic (technique vs. pro tour) rather than how substantive a post is. Its decision rule, kept for reference: a body part (arm, hip, wrist, chest, elbow) linked to a specific stroke phase (at contact, during takeaway, on follow-through); shot flight, overall swing path, or general quality judgments did not qualify.

## Stress test (8 AI-generated boundary posts)

| # | Post | Label | Rule |
|---|------|-------|------|
| 1 | "Swiatek's won 78% of her first-serve points on clay this year. If she holds that at Roland Garros she's the favourite, if it drops under 70 like Rome she's beatable." | `conditional` | Two explicit branches |
| 2 | "His elbow is way too high on the takeaway and that's why the ball keeps sailing long. Fix that and he's top 10." | `reaction` | Single-branch rhetorical; no metric |
| 3 | "Bro has lost 5 straight finals. Five. Mental midget." | `reaction` | Career count, not a box-score stat |
| 4 | "If Sinner wins Paris he's guaranteed YE#1. If not, it comes down to Turin." | `conditional` | Two explicit branches |
| 5 | "Watch his wrist on the forehand — it lays back about 90 degrees at contact. Federer's was closer to 60." | `metrics` | Hedged degree measurement counts |
| 6 | "Rune was top 5 at 20 and now he's outside the top 20. Something went badly wrong." | `reaction` | Age and rank ordinals are general counts |
| 7 | "His serve looks completely different this year, much more fluid, way less arm." | `reaction` | Qualitative, no metric or branches |
| 8 | "The race is basically locked at this point, nobody's catching those points." | `reaction` | No metric or branches |

Revisions made after the test: removed the tone-based calls (posts 3 and 6 now follow a written count/ordinal rule), dropped "verified" (a labeler can't check it) in favour of an explicit approximation rule, and required two branches for `conditional` so single "if" hot takes fall out.

## Expected distribution and fallback

Estimated from an unstratified sample: `reaction` ~70–80%, `metrics` ~15–20%, `conditional` ~5%.

**Fallback:** if the first 200-post pull yields fewer than 15 `conditional` posts, merge `conditional` into `metrics` (binary `metrics` vs. `reaction`) and document that multi-branch scenario math was too rare in general feed posts to support a stable 3-class classifier.
