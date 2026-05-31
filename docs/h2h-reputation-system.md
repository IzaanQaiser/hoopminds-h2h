# HoopMinds H2H Reputation System

## Purpose

Define how HoopMinds H2H turns prediction accuracy into status and repeat-play incentive.

Core framing:

```txt
Your Finals H2H record is your public basketball credibility.
```

## Product Positioning

Avoid framing as simple score feedback:

```txt
You got 4/5.
```

Prefer status framing:

```txt
You are 17/20 this Finals. Top 2%.
```

## Scope Split

## MVP (P0)

Ship these in MVP:

1. permanent series record
2. global rank context
3. percentile display with low-volume guardrail
4. accuracy title tier
5. best receipt per game
6. perfect card badge

## Post-MVP (P1+)

Defer these:

1. friend groups and invites
2. group-only leaderboard
3. `Casual of the Night`
4. group wins
5. top-10% streaks
6. series champion title

## Mechanic Coverage Matrix

This maps the incentive mechanics to delivery phase so nothing is ambiguous.

1. Permanent series record: `P0`
2. Percentile ranking: `P0` with low-volume guardrail
3. Accuracy titles: `P0` with provisional threshold
4. Friend-group shame labels (`Casual of the Night`): `P1+`
5. Receipts for correct calls: `P0`
6. Rare-call labels (`Sharp Call`, `Public Call`, `Contrarian Miss`): `P0`
7. Perfect Card badge: `P0`
8. Streaks (`games played`, `correct-call`, `perfect-card`, `top-10%`, `group wins`): `P1+`

## MVP Reputation Card

Show on `/h2h/results`:

```txt
Display Name: Izaan
Game Score: 4/5
Series Record: 12/15
Accuracy: 80%
Global Rank: 14/220
Global Percentile: Top 6%
Title: Certified Hooper
Best Receipt: Wemby 3 blocks before Brunson 30
```

## Metric Definitions

Game score:

```txt
correct_count / valid_race_count
```

Series record:

```txt
series_correct / series_valid
```

Accuracy:

```txt
series_valid === 0 ? 0 : series_correct / series_valid
```

Global rank:

```txt
Sort by:
1) series_correct desc
2) series_accuracy desc
3) series_valid desc
4) earliest_first_submission asc
```

Percentile:

```txt
percentile = (users_below / total_users) * 100
display label as "Top X%"
```

## Low-Volume Guardrails

Because early MVP may have low participation:

1. always show rank (`Rank X / N`)
2. only show percentile when `N >= 25` (tunable)
3. show title as provisional until user has `>= 10` valid picks

## Title Tiers

```txt
90–100%: Court Vision Demon
80–89%: Certified Hooper
70–79%: Knows Ball
60–69%: Solid Casual
50–59%: Agenda Merchant
Below 50%: Box Score Goblin
```

If `series_valid < 10`, display:

```txt
Provisional: <title>
```

## Receipts and Call Quality Labels

Each correct pick can generate a receipt line:

```txt
Receipt unlocked:
You called Wemby 3 blocks before Brunson 30 points.
Only 18% of players made that call.
```

MVP label rules:

1. `Sharp Call`: correct pick with pick rate `<= 25%`
2. `Public Call`: correct pick with pick rate `>= 60%`
3. `Contrarian Miss`: incorrect pick with pick rate `<= 25%`

Best receipt selection:

1. filter to user’s correct calls
2. compute global pick-rate per race/choice
3. choose the lowest pick-rate correct call

## Perfect Card

Award when:

```txt
correct_count = 5 and valid_race_count = 5
```

Display a prominent badge:

```txt
PERFECT CARD
5/5 on Game X H2H
Top Y%
```

## UX Copy Guidance

Prefer:

1. `Build your Finals ball-knowledge record`
2. `Make your calls`
3. `Top 8% tonight`
4. `Receipt unlocked`
5. `Perfect Card`

Keep non-gambling constraints from core docs.

## Data and API Notes

MVP can derive reputation metrics from existing tables:

1. `h2h_predictions`
2. `h2h_races`
3. `h2h_game_results`
4. `h2h_users`

No friend/group tables are required for MVP reputation.

## QA Acceptance Criteria

1. rank is correct for tie and non-tie cases
2. percentile appears only when threshold is met
3. provisional title appears under valid-pick threshold
4. perfect card appears only on valid 5/5
5. best receipt uses lowest pick-rate correct call
