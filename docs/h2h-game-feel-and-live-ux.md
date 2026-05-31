# docs/H2H_GAME_FEEL_AND_LIVE_UX.md

## Purpose

Define the game feel, live status UX, and before/during/after experience for HoopMinds H2H.

`/h2h` must not feel like a static form. It should feel like a fast, premium, mobile-first basketball challenge where every call creates tension during the game.

Primary emotional loop:

```txt
make calls -> lock in -> watch races sweat live -> get receipts -> protect series reputation
```

## Core UX Principle

Each game state should answer one question:

1. Pregame: `What am I choosing?`
2. Submitted: `What did I commit to?`
3. Live: `Am I winning or getting cooked?`
4. Finalizing: `When is my official score ready?`
5. Postgame: `How strong is my basketball record?`

## Required Product States

`/h2h` must support and visually differentiate five states:

1. Pregame Open
2. Submitted / Waiting for Tipoff
3. Locked / Game Live
4. Game Finalizing
5. Postgame Results

## State 1: Pregame Open

Goal:

1. get users to submit quickly before tipoff

Required UI:

1. HoopMinds H2H header
2. current Finals game context
3. featured matchup
4. lock countdown
5. five race cards
6. display name input
7. submit button
8. leaderboard link
9. no-betting disclaimer

Hero copy:

```txt
HoopMinds H2H
Five player races. One Finals game.
Make your calls before tipoff.
```

Race card format:

```txt
Race 1 of 5
Who gets there first?

[ Wemby - 3 blocks ]
[ Brunson - 30 points ]
[ Neither ]
```

UX requirements:

1. user understands gameplay in under 10 seconds
2. large tap targets
3. obvious selected state
4. submit disabled until all 5 races are answered
5. urgent countdown behavior near lock

Countdown display:

```txt
> 1h: Locks at 8:30 PM ET
< 1h: Locks in 42m
< 5m: Locks in 4:32
after lock: Calls locked
```

## State 2: Submitted / Waiting for Tipoff

Goal:

1. reinforce commitment and invite return/share

Required UI:

1. clear submission confirmation
2. all five selected calls
3. lock/game countdown
4. share CTA
5. leaderboard link
6. live-return prompt

Copy:

```txt
Your calls are in.
Come back during the game to watch the races sweat.
```

Share copy:

```txt
I made my HoopMinds H2H calls for Knicks-Spurs Game 1.
Beat my Finals record:
[link]
```

UX requirements:

1. no edit after submit for MVP
2. saved state is obvious
3. selected predictions remain readable

## State 3: Locked / Game Live

Goal:

1. make the page feel alive during the game

Required live race fields:

1. race number
2. player A progress and threshold
3. player B progress and threshold
4. user choice
5. current leader
6. race status label
7. completed result, when resolved

Example live card:

```txt
Race 1
Wemby: 2 / 3 blocks
Brunson: 24 / 30 points

Current edge: Wemby
Status: Live
Your call: Wemby
```

Example bars:

```txt
Wemby   ███████░ 2/3 BLK
Brunson ██████░░ 24/30 PTS
```

Race status labels:

```txt
Not started
Live
Sweating
One away
Completed
Neither pending
Void
```

Status guidance:

1. `Not started`: no progress yet
2. `Live`: both milestones still attainable
3. `Sweating`: one side >= 70% to threshold
4. `One away`: one side is one counting stat from threshold
5. `Completed`: one side reached first
6. `Neither pending`: game late and neither hit
7. `Void`: admin-voided race

Live microcopy examples:

```txt
Your call is alive.
You're one Wemby block away.
Brunson is closing fast.
Your call is in danger.
Brunson is 2 points away from cooking this race.
Wemby got there first. You called it.
Brunson got there first. You missed this one.
```

Live UX requirements:

1. support refresh cadence of 15-30s when automation exists
2. support manual admin updates when automation is not ready
3. never show fake progress
4. show `Updating...` when feed is delayed
5. keep user choice visible at all times

Live page priority order:

1. user score so far
2. live race progress
3. leaderboard
4. share/invite CTA

## State 4: Game Finalizing

Goal:

1. prevent confusion between game end and result publish

Required UI:

```txt
Final score is in.
H2H results are being checked.
Come back soon for your score.
```

UX requirements:

1. do not show incomplete/incorrect official score
2. finalize only after race outcomes are verified
3. show latest race progress snapshot if available

## State 5: Postgame Results

Goal:

1. convert results into reputation and replay intent

Required UI:

1. game score
2. series record
3. accuracy
4. rank
5. percentile (if threshold allows)
6. title/tier
7. best receipt
8. race-by-race breakdown
9. leaderboard CTA
10. share CTA
11. next-game teaser

Result summary example:

```txt
You went 4/5.
Series record: 4/5
Accuracy: 80%
Rank: Top 12%
Title: Certified Hooper
```

Race breakdown example:

```txt
Race 1
Your call: Wemby
Result: Wemby reached 3 blocks first
Correct
```

Receipt example:

```txt
Receipt unlocked:
You called Wemby 3 blocks before Brunson 30.
```

Share card text:

```txt
HoopMinds H2H - Game 1
I went 4/5.
Series accuracy: 80%.
Title: Certified Hooper.
Beat my record: hoopminds.xyz/h2h
```

Next-game teaser:

```txt
Game 2 H2H drops soon.
Protect your Finals record.
```

## Premium UX Requirements

Performance:

1. page feels instant
2. avoid heavy animation
3. show skeletons for game/race loads
4. use optimistic UI only after successful submit response
5. mobile-first layout
6. avoid race-card layout shift

Visual style:

1. dark mode default
2. card-based layout
3. large player names
4. clear selected states
5. subtle motion
6. avoid sportsbook-like visual language

Animation usage:

1. choice selection feedback
2. submit success confirmation
3. race completion moment
4. postgame score reveal

## Loading, Empty, and Error States

Loading text:

```txt
Loading tonight's H2H...
Loading leaderboard...
Checking your calls...
```

Errors:

```txt
Couldn't submit your calls. Try again.
Calls are locked for this game.
You already submitted for this game.
Results are not final yet.
```

Empty leaderboard:

```txt
No calls submitted yet.
Be first on the board.
```

## Accessibility Requirements

1. keyboard-accessible buttons
2. selected states not color-only
3. readable text at mobile sizes
4. semantic button controls for race choices

## Live Data Strategy

MVP:

1. manual admin updates are acceptable
2. admin can set race result
3. admin can set race progress values when live automation is absent
4. admin can set game status

Later:

1. automate progress updates from play-by-play data
2. poll every 15-30s during live games

Progress formula:

```txt
progress = current_stat / threshold
```

Clamp visual progress at 100%.

Completion:

1. when either side reaches threshold first, mark race completed
2. store winner
3. store result note
4. store resolved time when available

Result note examples:

```txt
Wemby reached 3 blocks first in Q3.
Brunson reached 30 points first in Q4.
Neither milestone hit.
```

## Final Product Feel

HoopMinds H2H should feel like:

```txt
A live Finals mini-game where every call becomes part of your basketball reputation.
```

It should not feel like:

```txt
A stats dashboard.
A sportsbook.
A static form.
A generic prediction poll.
```

