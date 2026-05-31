# HoopMinds H2H Phase 2 Plan

## Phase Name

```txt
H2H Game Delivery (Playable MVP + Scoring + Leaderboard)
```

## Objective

Implement the complete MVP gameplay loop described in `AGENTS.md`, `docs/h2h-product-context.md`, and `docs/h2h-build-plan.md`:

1. user makes five calls before lock
2. backend stores predictions safely
3. admin enters and finalizes race results
4. user sees score and breakdown
5. user sees reputation card and credibility context
6. global leaderboard updates correctly

Use `docs/h2h-reputation-system.md` for metric definitions, thresholds, and status-copy rules.

## Non-Negotiable Product Rules

1. Keep the experience free and game-like, never betting-like.
2. Support exactly five races for the active game.
3. Accept only `PLAYER_A`, `PLAYER_B`, `NEITHER` for user picks.
4. Admin race results allow `PLAYER_A`, `PLAYER_B`, `NEITHER`, `VOID`.
5. Backend lock check is source of truth.
6. No account system required for MVP.

## MVP Realism Constraints

Assume low initial user volume and no friend-group onboarding dependency.

Scope in MVP:

1. global reputation signals
2. series record
3. rank and conditional percentile
4. title tiers
5. best receipt and perfect card badge

Defer post-MVP:

1. group invites
2. group-only leaderboards
3. `Casual of the Night` labels
4. group wins
5. long streak systems

## Required Routes in This Phase

1. `/h2h`
2. `/h2h/results`
3. `/h2h/leaderboard`
4. `/admin/h2h`

## End-to-End User Flows

## Flow A: Pregame Submission

1. user opens `/h2h`
2. app loads active game, matchup, and five races
3. user selects one option per race
4. user enters display name
5. submit hits backend
6. backend validates and persists
7. UI transitions to already-submitted state

## Flow B: Locked Window

1. user opens `/h2h` after lock time
2. UI shows locked status and disabled controls
3. backend rejects any direct submit attempts

## Flow C: Postgame Results

1. admin sets race results
2. admin finalizes game
3. user opens `/h2h/results`
4. user sees score + race-by-race correctness
5. user sees series record + rank context + title + best receipt

## Flow D: Leaderboard

1. user opens `/h2h/leaderboard`
2. app shows global rank table across finalized games

## Implementation Tracks

## Track 1: `/h2h` Live Gameplay Page

Implement:

1. `MatchupHeader` with series, game number, teams, player matchup
2. lock countdown and lock status
3. five `RaceCard` components
4. display name input
5. submit button state management
6. leaderboard link CTA

State handling:

1. `OPEN`: full form enabled
2. `ALREADY_SUBMITTED`: form disabled, show submitted picks
3. `LOCKED`: no submission allowed
4. `FINAL`: prompt user to view results

Acceptance criteria:

1. user cannot submit until all five races are answered
2. user cannot submit without valid display name
3. mobile layout is clear and tappable

## Track 2: Prediction Submission API

Endpoint:

```txt
POST /api/h2h/submit-predictions
```

Validation requirements:

1. game exists
2. game status is `open`
3. current time is before `locks_at`
4. display name length is 1-20
5. exactly five predictions submitted
6. one prediction per race
7. all race IDs belong to the specified game
8. choices are valid enum values
9. user has not already submitted for this game

Persistence behavior:

1. upsert user by `anonymous_id`
2. update display name if changed
3. insert race predictions
4. enforce uniqueness by DB constraints

Acceptance criteria:

1. duplicates are rejected
2. late submissions are rejected
3. malformed payloads are rejected
4. successful submit persists all five choices

## Track 3: Admin Result Entry and Finalization

Admin page requirements (`/admin/h2h`):

1. password gate with `ADMIN_PASSWORD`
2. view active game and all races
3. set result for each race (`PLAYER_A`, `PLAYER_B`, `NEITHER`, `VOID`)
4. optional result note per race
5. finalize game action

Finalize behavior:

1. load all submitted users for game
2. load all race results
3. ignore `VOID` and `null` races
4. count per-user correct predictions
5. compute `accuracy = correct / valid`
6. upsert `h2h_game_results`
7. mark game as `final`

Acceptance criteria:

1. finalize is idempotent (safe to rerun)
2. void race handling is correct
3. game status changes to final only after finalize succeeds

## Track 4: Results Page

Page requirements (`/h2h/results`):

1. identify user via anonymous ID
2. show game score (correct/valid)
3. show series accuracy
4. show race-by-race result and user pick
5. show reputation card fields
6. link to leaderboard

Acceptance criteria:

1. displays clear score after finalize
2. handles no-result-yet state gracefully
3. works for users who submitted and users who did not submit

## Track 5: Reputation System (MVP v1)

Compute and show:

1. game score
2. series record
3. series accuracy
4. global rank (`Rank X / N`)
5. global percentile label (`Top Y%`) when sample threshold is met
6. title tier from accuracy
7. provisional title state when sample is too small
8. best receipt from correct calls
9. perfect card badge for valid 5/5

Rules:

1. always show rank
2. only show percentile when total participants threshold is met (recommended `N >= 25`)
3. only show non-provisional title when user has enough sample (recommended `>= 10 valid picks`)
4. best receipt picks the user’s correct call with lowest global pick-rate
5. if user had zero correct calls, hide best receipt module

Acceptance criteria:

1. reputation card values match DB aggregates
2. low-volume percentile guardrail works
3. provisional title guardrail works
4. perfect badge appears only on valid 5/5

## Track 6: Leaderboard Page

Query requirements:

1. aggregate across finalized games
2. fields: `display_name`, `correct_total`, `valid_total`, `accuracy`, `games_played`
3. additional optional fields for reputation framing:
   1. `rank`
   2. `percentile_label`
   3. `perfect_cards`
4. sort order:
   1. `correct_total desc`
   2. `accuracy desc`
   3. `valid_total desc`
   4. `earliest_submission asc`

UI requirements:

1. rank column
2. display name
3. correct predictions
4. total predictions
5. accuracy percentage
6. games played
7. optional status marker (`Perfect Card xN`)

Acceptance criteria:

1. ranking is stable and deterministic
2. empty state handled for no finalized games

## Track 7: Copy and Compliance Pass

Confirm app copy includes:

1. “Make your calls”
2. “Who gets there first?”
3. “Series accuracy”
4. “Leaderboard”
5. “Build your Finals ball-knowledge record”

Confirm app copy excludes:

1. bet
2. wager
3. odds
4. line
5. prop
6. parlay
7. payout
8. cash
9. sportsbook

Footer required:

```txt
HoopMinds H2H is a free basketball prediction challenge. No betting, no odds, no paid entry, and no cash prizes.
```

Acceptance criteria:

1. no prohibited terms in user-facing UI
2. required disclaimer visible on core pages

## Track 8: Robustness and Edge Cases

Validate:

1. lock boundary timing (`exactly at locks_at`)
2. submit retry behavior under network errors
3. race result changes before finalize
4. finalize with all races `VOID`
5. finalize with some races unresolved (`null`)
6. unknown anonymous ID on results page
7. percentile threshold boundary (`N = 24` vs `N = 25`)
8. provisional title threshold boundary (`valid picks = 9` vs `10`)
9. perfect-card edge case with `VOID` races

Acceptance criteria:

1. edge cases return clear, non-confusing messages
2. no inconsistent scores in DB

## Phase 2 Exit Gate

Phase 2 is complete only when all statements are true:

1. user can submit five race picks before lock on `/h2h`
2. backend enforces lock and duplicate protections
3. admin can set race results and finalize game
4. `/h2h/results` shows correct score breakdown
5. `/h2h/results` shows reputation card metrics correctly
6. `/h2h/leaderboard` aggregates and sorts correctly
7. all UI copy remains non-gambling
8. MVP works on mobile and desktop
