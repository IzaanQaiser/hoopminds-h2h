# AGENTS.md

## Project

This repo is for **HoopMinds**, hosted at **hoopminds.xyz**.

The current product being built is the **HoopMinds H2H Finals Game**, available at:

```txt
/h2h
```

Do not build a generic sports analytics dashboard. Do not build a betting app. Do not build a report-card product. The core product is a simple, mobile-first NBA Finals prediction game based on head-to-head “First To” player races.

## Core Product Summary

HoopMinds H2H is a free NBA Finals game where users predict which of two featured players reaches a stat milestone first.

Each NBA Finals game has:

* one curated head-to-head matchup
* two players from opposing teams
* five “First To” races
* three choices per race:

  * Player A reaches their milestone first
  * Player B reaches their milestone first
  * Neither milestone happens

Users make predictions before tipoff. Predictions lock at game start. After the game, results are entered or resolved, and users get a score plus leaderboard position.

## Product URL

```txt
/h2h
```

The homepage can be simple for now. Prioritize `/h2h`.

## Brand

Product/site name:

```txt
HoopMinds
```

Feature name:

```txt
H2H
```

Acceptable labels:

* HoopMinds H2H
* H2H Finals
* First To races
* player races
* make your calls
* series accuracy
* leaderboard

Avoid gambling-adjacent language:

* bets
* props
* odds
* lines
* parlay
* wager
* payout
* cash
* lock
* sportsbook

This must feel like a free basketball IQ game, not a betting product.

## MVP Goal

Build a working MVP before Game 1.

A user must be able to:

1. Visit `/h2h`.
2. See the current Finals game.
3. See the featured player matchup.
4. Make five predictions.
5. Enter a display name.
6. Submit before lock time.
7. Have predictions stored in Supabase.
8. Return after results are entered.
9. See their score.
10. See the leaderboard.

## Tech Stack

Use:

* Next.js App Router
* TypeScript
* Tailwind CSS
* Supabase Postgres
* Vercel deployment

Use server-side API routes or server actions for database writes. Do not put service role keys in client code.

## Source of Truth

Read these docs before making product or architecture decisions:

```txt
docs/H2H_PRODUCT_CONTEXT.md
docs/H2H_BUILD_PLAN.md
```

If a request conflicts with these docs, ask for clarification before changing product direction.

## Development Rules For Codex

When implementing:

1. Keep the product simple.
2. Prioritize `/h2h`.
3. Build mobile-first.
4. Use API-trackable stats only.
5. Avoid subjective basketball events.
6. Keep copy short and clear.
7. Do not add authentication unless explicitly requested.
8. Do not add payment, prizes, betting, odds, or gambling mechanics.
9. Do not over-engineer group/friend leaderboards for MVP.
10. Add manual admin result entry before attempting full automated API resolution.

## Current MVP Data Strategy

For MVP, results can be manually entered through an admin route or seed script.

Do not block the product on live NBA API integration.

Ideal progression:

1. Build static/current Game 1 data.
2. Store predictions.
3. Lock predictions.
4. Manually resolve races.
5. Show results and leaderboard.
6. Add automated API tracking later.

## Required Routes

Minimum routes:

```txt
/h2h
/h2h/results
/h2h/leaderboard
/admin/h2h
```

Optional later:

```txt
/h2h/live
/h2h/group/[groupId]
```

## MVP Pages

### /h2h

Main game page.

Must show:

* HoopMinds H2H branding
* current Finals game
* lock countdown/status
* featured matchup
* five race cards
* display name input
* submit button
* link to leaderboard

### /h2h/results

Postgame result page.

Must show:

* user score
* correct/incorrect race breakdown
* series accuracy
* leaderboard link

### /h2h/leaderboard

Must show:

* rank
* display name
* correct predictions
* total predictions
* accuracy percentage
* games played

### /admin/h2h

Simple protected admin page.

Must allow:

* setting race results
* marking races as Player A, Player B, Neither, or Void
* finalizing game results

For MVP, admin protection can be a simple password using an environment variable.

## Database Expectations

Use Supabase tables for:

* games
* players
* matchups
* races
* users
* predictions
* results

Keep schema simple. See `docs/H2H_BUILD_PLAN.md`.

## Game 1 Context

The intended first matchup is:

```txt
Victor Wembanyama vs Jalen Brunson
```

The Finals matchup is:

```txt
New York Knicks vs San Antonio Spurs
```

Game 1 page should support this matchup, but data should be database-driven enough that the matchup can be changed for future games.

## Race Design Rules

Use only stats that can be obtained easily from a box score or play-by-play API.

Allowed stats:

* points
* rebounds
* assists
* blocks
* steals
* turnovers
* personal fouls
* made field goals
* attempted field goals
* made threes
* attempted threes
* made free throws
* attempted free throws
* stocks = steals + blocks
* points + assists
* points + rebounds
* rebounds + assists

Avoid:

* paint touches
* midrange makes
* clutch buckets
* chase-down blocks
* contested shots
* defensive stops
* screen assists
* hockey assists
* momentum plays
* anything requiring subjective manual film tagging

## UX Style

Design should feel:

* clean
* fast
* mobile-first
* slightly competitive
* basketball-native
* not corporate
* not sportsbook-like

Use dark mode by default.

Race card copy should be simple:

```txt
Who gets there first?

Wemby: 3 blocks
Brunson: 30 points
Neither
```

## Definition of Done For MVP

MVP is complete when:

* `/h2h` works on mobile and desktop
* users can submit all five predictions
* predictions are blocked after lock time
* predictions persist in Supabase
* admin can enter race results
* user results calculate correctly
* leaderboard calculates correctly
* no gambling language appears in UI
