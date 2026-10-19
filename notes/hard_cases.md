# Milestone 4 — Hard cases

Posts that made me pause while labelling: which two labels each could be, and what I chose.

## From the cold pass (rows 1–20)

### 1. Row 14 — `metrics` vs. `reaction` → chose `metrics`

> "huge win for coleman! taking down the world number 29 in straight sets is a massive confindence booster. Hopefully he can keep that exact same momentum going into the quarterfinals"

It reads as a celebratory reaction ("huge win"), but it names a specific ranking ("world number 29") and a match result ("in straight sets"). I treated those as quantitative performance values.

### 2. Row 6 — `conditional` vs. `reaction` → chose `reaction`

> "Alcaraz before Sinner? How's that make sense? He's been playing after him? Why switch it now? Now Carlos has less than 24 hours. Sinner has over… 🤔"

It deals with scheduling and time math ("less than 24 hours"), but it has no two explicit outcome branches mapping tournament progression or point math. It's a rhetorical complaint, so it defaults to `reaction`.

### 3. Row 16 — dropped biomechanics label vs. `reaction` → chose `reaction`

> "I played tennis for 10 years as a junior and can't fathom creating any sort of power from this position. I'd be poking that into the bottom of the net every time"

It's about stroke position, which the dropped biomechanics label would have taken. With that label gone, qualitative technique commentary falls to `reaction`.

## From the pre-label pass (labelled by Claude under the written rules; not my cold calls)

- **"…if 95+% of other grandslam losers can show restraint on court, she can too."** `metrics` vs. `reaction` → `reaction`. It has a percentage, but it describes player behaviour, not a performance stat.
- **"Its just wanting more commercial breaks, if it was about player safety, then … and if it was about making sure every game was perfectly even then …"** `conditional` vs. `reaction` → `reaction`. It has two explicit branches, but they're about broadcast rules, not a draw, ranking, or qualification scenario.
- **"If he's healthy he's winning in two and if he's not then Demon's winning in three."** `conditional` vs. `reaction` → `conditional`. The two branches predict a match outcome, which matches how stress-test post 1 (Swiatek favourite / beatable) was labelled.
