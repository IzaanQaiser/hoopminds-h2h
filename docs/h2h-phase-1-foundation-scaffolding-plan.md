# HoopMinds H2H Phase 1 Plan

## Phase Name

```txt
Foundation Scaffolding (Frontend + Backend Plumbing)
```

## Objective

Build the technical foundation for the H2H MVP so that all required routes, database tables, API surfaces, and UI shells exist and are wired with safe defaults.

By the end of this phase, the app should be structurally complete and ready for full gameplay logic in Phase 2.

## Guardrails (Must Follow)

1. Prioritize `/h2h` and required MVP routes only.
2. No authentication system in MVP.
3. No gambling mechanics or gambling language.
4. No service role key in client code.
5. Keep data model simple and aligned with `docs/h2h-build-plan.md`.

## In Scope

1. Project structure and route scaffolding.
2. Supabase migration and seed foundation.
3. API endpoint scaffolding with strict validation shapes.
4. Frontend component scaffolding and state shells.
5. Admin password protection scaffold.
6. Testing and verification harness.

## Out of Scope (Phase 2)

1. Final gameplay scoring behavior.
2. Complete admin finalize workflow.
3. Production-ready leaderboard aggregation logic.
4. Full results display logic.
5. Live external NBA API integration.

## Deliverables

1. Route skeletons for all required pages.
2. API route skeletons for all required operations.
3. Supabase SQL migrations for all core tables.
4. Deterministic seed script for Game 1.
5. Shared H2H types, copy constants, and utility modules.
6. Baseline tests (lint + typecheck + API smoke tests).
7. Game-feel scaffolding hooks for `OPEN`, `SUBMITTED`, `LIVE`, `FINALIZING`, `FINAL`.

## Detailed Workstreams

## Workstream 1: App and File Structure

Create and verify:

```txt
app/
  page.tsx
  h2h/page.tsx
  h2h/results/page.tsx
  h2h/leaderboard/page.tsx
  admin/h2h/page.tsx
  api/h2h/current-game/route.ts
  api/h2h/submit-predictions/route.ts
  api/h2h/results/route.ts
  api/h2h/leaderboard/route.ts
  api/h2h/admin/set-race-result/route.ts
  api/h2h/admin/finalize-game/route.ts
components/h2h/
  H2HGamePage.tsx
  MatchupHeader.tsx
  RaceCard.tsx
  PredictionForm.tsx
  LockedPicks.tsx
  ResultsSummary.tsx
  ReputationCard.tsx
  LeaderboardTable.tsx
  AdminRaceResultForm.tsx
lib/h2h/
  types.ts
  scoring.ts
  reputation.ts
  receipts.ts
  anonymousUser.ts
  copy.ts
  statLabels.ts
lib/supabase/
  client.ts
  server.ts
```

Acceptance criteria:

1. `npm run dev` boots with no missing imports.
2. Required routes render at least placeholder content.
3. No dead links between H2H pages.

## Workstream 2: Environment and Secrets Contract

Create `.env.example` with:

```txt
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
ADMIN_PASSWORD=
```

Implement runtime guards:

1. Server-only modules throw on missing server env vars.
2. Client modules only access `NEXT_PUBLIC_*` vars.
3. Admin route fails closed when password env var is missing.

Acceptance criteria:

1. Missing env vars produce clear server errors.
2. Secret env vars do not appear in client bundles.

## Workstream 3: Supabase Data Foundation

Create migration(s) for:

1. `h2h_games`
2. `h2h_players`
3. `h2h_matchups`
4. `h2h_races`
5. `h2h_users`
6. `h2h_predictions`
7. `h2h_game_results`

Add practical indexes:

1. game status + lock/start timestamps
2. predictions by `(game_id, user_id)`
3. races by `(game_id, race_number)`
4. results by `(game_id, user_id)`

Create deterministic seed data for:

1. 2026 Finals Game 1 (`Knicks @ Spurs`)
2. `Victor Wembanyama` and `Jalen Brunson`
3. one matchup + five races
4. game status set to `open` in dev seed flow

Acceptance criteria:

1. Fresh DB migration succeeds from zero.
2. Seed inserts all core entities once.
3. Reseed does not create duplicate logical records.

## Workstream 4: Shared Domain Modules

Implement `lib/h2h/types.ts`:

1. `GameStatus`
2. `RaceResult`
3. `PredictionChoice`
4. `H2HStat`
5. DTOs for API request and response payloads

Implement utility modules:

1. `anonymousUser.ts` for local storage ID lifecycle
2. `statLabels.ts` for stable display text
3. `copy.ts` for centralized approved copy
4. `scoring.ts` placeholder signature (full logic in Phase 2)
5. `reputation.ts` placeholder signatures for rank/percentile/title logic
6. `receipts.ts` placeholder signatures for rare-call/receipt logic

Acceptance criteria:

1. API and UI compile against shared types.
2. No duplicated string enums in multiple files.

## Workstream 5: API Scaffolding and Validation Shells

Scaffold these endpoints with typed request/response contracts:

1. `GET /api/h2h/current-game`
2. `POST /api/h2h/submit-predictions`
3. `GET /api/h2h/results`
4. `GET /api/h2h/leaderboard`
5. `POST /api/h2h/admin/set-race-result`
6. `POST /api/h2h/admin/finalize-game`

Validation shell requirements:

1. Reject malformed payloads consistently.
2. Return standardized error structure.
3. Keep route handlers small and composable.

Acceptance criteria:

1. Every endpoint responds with JSON.
2. Invalid payloads return predictable status + error code.

## Workstream 6: Frontend Scaffolding

Build shell components with mobile-first layout and dark mode baseline:

1. matchup header shell
2. five race card shell
3. prediction form shell
4. locked picks shell
5. results summary shell
6. reputation card shell
7. leaderboard table shell
8. admin race result shell
9. live race progress shell
10. finalizing state shell

Add navigation links:

1. `/h2h` -> `/h2h/leaderboard`
2. `/h2h/results` -> `/h2h/leaderboard`
3. `/h2h` -> `/h2h/results` (when appropriate)

Acceptance criteria:

1. Works in mobile viewport first.
2. No route dead ends.
3. Copy stays aligned with approved H2H language.

## Workstream 7: Test Harness and Quality Gates

Set up baseline quality scripts:

1. lint
2. typecheck
3. unit test runner
4. API smoke tests for route shape

Add a Phase 1 verification checklist:

1. migrations pass
2. seed pass
3. route compile pass
4. API shape tests pass
5. no secret leakage

Acceptance criteria:

1. All quality scripts run locally.
2. Failing validations are easy to diagnose.

## Phase 1 Exit Gate

Phase 1 is complete only when all statements are true:

1. All required routes exist and render.
2. All required API routes exist and validate payload shape.
3. Supabase schema and seed are stable and repeatable.
4. Shared types/utilities are in place and used.
5. Baseline tests pass.
6. Codebase is ready for full gameplay implementation in Phase 2.
