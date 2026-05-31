# docs/H2H_PRODUCT_CONTEXT.md

## Product Name

```txt
HoopMinds H2H
```

## Domain

```txt
hoopminds.xyz
```

## Main Page

```txt
/h2h
```

## One-Sentence Description

HoopMinds H2H is a free NBA Finals prediction game where fans choose which star reaches their signature stat milestone first.

## Core Concept

For each NBA Finals game, HoopMinds H2H presents one curated head-to-head player matchup.

The matchup has five “First To” races.

Each race compares two different stat milestones, one for each player.

Example:

```txt
Who gets there first?

Victor Wembanyama: 3 blocks
Jalen Brunson: 30 points
Neither
```

Users make all five predictions before tipoff. Once the game starts, predictions lock. After the game, results are resolved and users receive a score.

## Why This Exists

Most prediction games ask boring questions:

```txt
Who wins?
Over or under?
Who scores more?
```

HoopMinds H2H is built around player identity.

The user is not just guessing a stat. They are choosing which player imposes their game first.

Examples:

* Wemby controls the rim before Brunson controls the scoreboard.
* Brunson pressures the defense before Wemby dominates the glass.
* One player’s signature impact appears before the other player’s.

## Product Thesis

Fans want something simple to play before the game that gives them something specific to watch during the game.

The product should create this loop:

```txt
make calls → watch races unfold → check result → compare with friends → return next game
```

## What This Is Not

This is not:

* a betting app
* a sportsbook clone
* a PrizePicks clone
* a gambling product
* an analytics dashboard
* a report card generator
* a generic NBA trivia game
* a highlight-ranking app

There is:

* no money
* no prizes
* no odds
* no betting
* no payment
* no gambling language

## Target User

The main user is an NBA fan who watches playoff basketball and talks in group chats.

They care about:

* stars
* matchups
* narratives
* being right
* beating friends
* protecting their basketball reputation

The product should take less than 60 seconds to play before tipoff.

## MVP User Story

As an NBA fan, I want to quickly predict five head-to-head player races before the Finals game starts so that I have something specific to track during the game and can compare my score with friends afterward.

## Main User Flow

### Pregame

1. User visits `/h2h`.
2. User sees the current Finals game.
3. User sees the featured matchup.
4. User reads five race cards.
5. User chooses Player A, Player B, or Neither for each race.
6. User enters display name.
7. User submits predictions before lock time.

### Locked State

1. User sees submitted predictions.
2. User cannot edit after lock time.
3. User can share the page with friends.

### Postgame

1. Admin resolves race results.
2. User visits results page.
3. User sees score.
4. User sees correct and incorrect calls.
5. User sees leaderboard rank.

## Current Finals Setup

The current intended MVP game is:

```txt
NBA Finals Game 1
New York Knicks vs San Antonio Spurs
Featured Matchup: Victor Wembanyama vs Jalen Brunson
```

The matchup can change game by game.

## Game 1 Matchup Rationale

Victor Wembanyama vs Jalen Brunson is the cleanest Game 1 matchup because it contrasts two obvious star identities.

Wembanyama:

* rim protection
* rebounding
* blocks
* defensive disruption
* efficient scoring
* threes as a stretch threat

Brunson:

* scoring
* shot creation
* assists
* made field goals
* free throws
* three-point shotmaking

## Game 1 Race Examples

Use API-trackable stats only.

Recommended initial races:

### Race 1

```txt
Victor Wembanyama reaches 3 blocks
vs
Jalen Brunson reaches 30 points
vs
Neither
```

### Race 2

```txt
Victor Wembanyama reaches 12 rebounds
vs
Jalen Brunson reaches 8 assists
vs
Neither
```

### Race 3

```txt
Victor Wembanyama makes 2 threes
vs
Jalen Brunson makes 4 threes
vs
Neither
```

### Race 4

```txt
Victor Wembanyama reaches 20 points
vs
Jalen Brunson reaches 10 made field goals
vs
Neither
```

### Race 5

```txt
Victor Wembanyama reaches 4 stocks
vs
Jalen Brunson reaches 10 free throw attempts
vs
Neither
```

These can be changed by the admin/creator before launch.

## Race Result Rules

Each race has three possible results:

```txt
PLAYER_A
PLAYER_B
NEITHER
VOID
```

* `PLAYER_A`: Player A reached their milestone first.
* `PLAYER_B`: Player B reached their milestone first.
* `NEITHER`: neither player reached their milestone by the end of the game.
* `VOID`: race should not count because of broken data, unclear result, postponed game, or admin decision.

## Scoring Rules

MVP scoring is simple.

```txt
correct race = 1 point
incorrect race = 0 points
void race = not counted
```

Game score:

```txt
correct_count / valid_race_count
```

Series score:

```txt
total_correct / total_valid
```

Accuracy:

```txt
total_correct / total_valid * 100
```

## Lock Rules

Predictions lock at the game lock time.

Frontend should disable submission after lock time.

Backend must also reject submissions after lock time.

Backend lock check is the source of truth.

## User Identity

MVP does not require login.

Use:

* anonymous browser ID
* display name

Store anonymous ID in local storage.

Display name is submitted with predictions.

One anonymous user can submit once per game.

## Leaderboard

MVP leaderboard is global.

Columns:

* rank
* display name
* correct
* total
* accuracy
* games played

Sorting:

1. total correct descending
2. accuracy descending
3. total predictions descending
4. earliest first submission ascending

Friends leaderboard can come later.

## Admin Role

Admin/creator curates each game.

Admin must be able to:

* create/edit game
* create/edit players
* create/edit matchup
* create/edit races
* open/lock/finalize game
* set race results
* void races if needed

For MVP, keep this simple.

## Copy Guidelines

Use:

* “Make your calls”
* “Who gets there first?”
* “First To race”
* “Series accuracy”
* “Leaderboard”
* “Result”
* “Correct”
* “Miss”

Avoid:

* “bet”
* “wager”
* “odds”
* “line”
* “prop”
* “parlay”
* “cash”
* “payout”
* “lock”

## UI Requirements

Use dark mode.

Design should prioritize:

* big player names
* clear race cards
* large tappable options
* obvious selected state
* clear submit button
* clear locked state
* fast mobile experience

The app should not feel like a financial app or sportsbook.

## MVP Success Criteria

MVP is successful if:

* users can play without an account
* predictions persist
* lock time works
* results can be entered
* leaderboard works
* user can understand the game in under 10 seconds
* no subjective stat tracking is required
