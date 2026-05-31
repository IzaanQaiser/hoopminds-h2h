# HoopMinds H2H Mock Stream and Test Data Plan

## Purpose

Provide deterministic mock data and replay tooling for local development and QA without blocking MVP on live NBA APIs.

This supports faster iteration on:

1. race resolution logic
2. scoring correctness
3. UI state transitions
4. leaderboard math

## Important Constraint

For MVP, official outcomes are still manually entered via `/admin/h2h`.

Mock stream is a testing and simulation aid, not the production source of truth.

## Data Artifacts To Add

## 1) Static Seed Data

Use seed data for:

1. one active game (`2026 NBA Finals Game 1`)
2. player matchup (`Victor Wembanyama` vs `Jalen Brunson`)
3. five configured races

File suggestion:

```txt
supabase/seed-h2h-game1.sql
```

## 2) Event Timeline Fixture

Create deterministic event timeline JSON.

File suggestion:

```txt
data/mock/h2h-game1-events.json
```

Event schema (suggested):

```ts
type MockGameEvent = {
  sequence: number;                  // strict increasing integer
  gameClock: string;                 // e.g. "Q1 10:34"
  timestampIso: string;              // deterministic ordering
  playerKey: "PLAYER_A" | "PLAYER_B";
  stat: "PTS" | "REB" | "AST" | "BLK" | "FGM" | "FG3M" | "FTA" | "STL";
  delta: number;                     // increment amount
  note?: string;                     // optional human-readable context
};
```

## 3) Expected Outcome Fixtures

Create expected results snapshots for QA assertions.

File suggestions:

```txt
data/mock/h2h-game1-expected-race-results.json
data/mock/h2h-game1-expected-user-scores.json
```

## Mock Replay Utility

Add a replay script that consumes timeline events and derives race outcomes.

File suggestion:

```txt
scripts/replay-h2h-mock-stream.ts
```

Replay behavior:

1. load active race definitions
2. process events in `sequence` order
3. keep running stat totals per player
4. mark race winner at first threshold hit
5. mark race `NEITHER` if end-of-game reached with no thresholds
6. output deterministic summary JSON

CLI options (suggested):

1. `--game-slug finals-2026-g1`
2. `--timeline data/mock/h2h-game1-events.json`
3. `--write-summary data/mock/h2h-game1-computed-summary.json`
4. `--dry-run` (console only)

## Optional Dev Endpoint (Non-Production Only)

Optional route:

```txt
/api/h2h/mock-stream
```

Use only in non-production environments.

Endpoint behaviors:

1. return computed per-race progress snapshot
2. return current race leader status
3. return derived final outcomes for QA

Hard rule:

1. endpoint disabled in production

## Test Scenario Matrix

Minimum scenarios:

1. `PLAYER_A` reaches threshold before `PLAYER_B`
2. `PLAYER_B` reaches threshold before `PLAYER_A`
3. neither threshold is reached (`NEITHER`)
4. admin overrides to `VOID`
5. tie-adjacent ordering is deterministic by event sequence
6. one user fully correct (5/5)
7. one user partially correct
8. lock-time rejection scenario
9. duplicate submission rejection scenario
10. all races voided results in `valid_race_count = 0`

## QA Verification Workflow

1. reset and seed DB
2. submit test predictions for multiple anonymous users
3. run mock replay to generate expected race outcomes
4. set admin race results to expected outcomes
5. finalize game
6. compare persisted `h2h_game_results` against expected fixture
7. validate leaderboard ordering against expected ranking fixture

## Determinism Rules

1. never use real-time random values in fixtures
2. preserve strict event `sequence`
3. use fixed timestamps in fixtures
4. avoid locale-dependent formatting in expected output

## Acceptance Criteria

1. mock replay outputs the same summary on every run
2. expected race outcomes match admin-entered outcomes in QA tests
3. expected user scores match finalized DB results
4. leaderboard sort is verified with fixture-based assertions

