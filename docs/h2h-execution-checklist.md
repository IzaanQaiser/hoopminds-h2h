# HoopMinds H2H Execution Checklist

Use this as the operational tracker for delivery. Do not start Phase 2 until Phase 1 gate is complete.

## Phase 1: Foundation Scaffolding

Reference:

1. `docs/h2h-phase-1-foundation-scaffolding-plan.md`

Checklist:

- [ ] Create required app routes and API route skeletons
- [ ] Create `components/h2h` shell components
- [ ] Add `lib/h2h` shared modules (`types`, `copy`, `statLabels`, `anonymousUser`, `scoring`, `reputation`, `receipts`)
- [ ] Add Supabase client/server helper modules
- [ ] Add `.env.example` with all required env vars
- [ ] Add runtime env guards for missing vars
- [ ] Create and apply Supabase migrations for all H2H tables
- [ ] Add practical DB indexes for game/prediction/result queries
- [ ] Create deterministic Game 1 seed script/data
- [ ] Scaffold all H2H API endpoints with payload validation shells
- [ ] Standardize API success/error response structure
- [ ] Wire placeholder UI states for open/locked/submitted/final
- [ ] Add admin password gate scaffold
- [ ] Set up lint, typecheck, and baseline tests
- [ ] Verify no service role key appears in client code

Phase 1 gate:

- [ ] All required routes render successfully
- [ ] Migrations + seed run clean from fresh DB
- [ ] API route shape tests pass
- [ ] Codebase is ready for full gameplay logic

## Phase 2: H2H Gameplay Delivery

Reference:

1. `docs/h2h-phase-2-h2h-game-delivery-plan.md`
2. `docs/h2h-reputation-system.md`

Checklist:

- [ ] Implement `GET /api/h2h/current-game` with real DB data
- [ ] Build `/h2h` with real matchup + race rendering
- [ ] Implement five-race selection and display name validation
- [ ] Implement `POST /api/h2h/submit-predictions` full validations
- [ ] Add user upsert by anonymous ID and duplicate prevention
- [ ] Implement already-submitted state on `/h2h`
- [ ] Implement locked state UI and backend enforcement
- [ ] Build `/admin/h2h` race result editor (`PLAYER_A/B/NEITHER/VOID`)
- [ ] Implement finalize-game scoring write to `h2h_game_results`
- [ ] Mark game final only after finalize succeeds
- [ ] Build `/h2h/results` score + breakdown view
- [ ] Build `/h2h/results` reputation card (series record, rank, title, best receipt, perfect badge)
- [ ] Implement percentile display threshold logic (`N >= 25`)
- [ ] Implement provisional title threshold logic (`>= 10 valid picks`)
- [ ] Build `/h2h/leaderboard` aggregation + sorting
- [ ] Add required no-betting disclaimer copy
- [ ] Run copy audit to remove prohibited gambling terms
- [ ] Validate mobile-first behavior across required pages

Phase 2 gate:

- [ ] User can submit complete picks before lock
- [ ] Backend rejects late, malformed, and duplicate submissions
- [ ] Admin can resolve and finalize game
- [ ] Results display is correct post-finalize
- [ ] Reputation metrics display correctly post-finalize
- [ ] Leaderboard rank order is correct and stable
- [ ] MVP criteria in `AGENTS.md` are fully satisfied

## Mock Stream + QA Data

Reference:

1. `docs/h2h-mock-stream-and-test-data-plan.md`

Checklist:

- [ ] Add deterministic event timeline fixture
- [ ] Add expected race outcomes fixture
- [ ] Add expected user score fixture
- [ ] Add mock replay utility script
- [ ] Add optional non-prod mock stream endpoint (if needed)
- [ ] Verify race outcomes against fixtures
- [ ] Verify finalized scores against fixtures
- [ ] Verify leaderboard ordering against fixtures
- [ ] Verify percentile threshold behavior with small and larger sample sizes
- [ ] Verify title provisional threshold behavior
- [ ] Verify perfect-card badge behavior

## Post-MVP Social Layer (Deferred)

- [ ] Group invites and group join flow
- [ ] Group-only leaderboard route and data model
- [ ] `Casual of the Night` group label
- [ ] Group wins metric
- [ ] Streak metrics beyond MVP

## Final MVP Release Readiness

- [ ] Required routes are fully functional: `/h2h`, `/h2h/results`, `/h2h/leaderboard`, `/admin/h2h`
- [ ] Prediction lock behavior is enforced server-side
- [ ] Supabase writes are server-side only
- [ ] No auth/payment/prizes/betting mechanics were introduced
- [ ] Manual admin resolution flow is usable end-to-end
- [ ] Global leaderboard works across finalized games
- [ ] UI language stays basketball-native and non-gambling
