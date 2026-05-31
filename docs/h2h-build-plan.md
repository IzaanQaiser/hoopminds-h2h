# docs/H2H_BUILD_PLAN.md

## Build Objective

Build the HoopMinds H2H MVP at:

```txt
/h2h
```

The MVP should support one active NBA Finals game with five head-to-head player races, anonymous prediction submission, manual result entry, results calculation, and a global leaderboard.

## Companion Planning Docs

Use these documents together with this build plan:

1. `docs/h2h-phase-1-foundation-scaffolding-plan.md`
2. `docs/h2h-phase-2-h2h-game-delivery-plan.md`
3. `docs/h2h-mock-stream-and-test-data-plan.md`
4. `docs/h2h-execution-checklist.md`

## Recommended Stack

```txt
Next.js App Router
TypeScript
Tailwind CSS
Supabase Postgres
Vercel
```

## Suggested Folder Structure

```txt
app/
  page.tsx
  h2h/
    page.tsx
    results/
      page.tsx
    leaderboard/
      page.tsx
  admin/
    h2h/
      page.tsx
  api/
    h2h/
      current-game/
        route.ts
      submit-predictions/
        route.ts
      leaderboard/
        route.ts
      results/
        route.ts
      admin/
        set-race-result/
          route.ts
        finalize-game/
          route.ts

components/
  h2h/
    H2HGamePage.tsx
    MatchupHeader.tsx
    RaceCard.tsx
    PredictionForm.tsx
    LockedPicks.tsx
    ResultsSummary.tsx
    LeaderboardTable.tsx
    AdminRaceResultForm.tsx

lib/
  supabase/
    client.ts
    server.ts
  h2h/
    types.ts
    scoring.ts
    anonymousUser.ts
    copy.ts
    statLabels.ts

docs/
  H2H_PRODUCT_CONTEXT.md
  H2H_BUILD_PLAN.md

AGENTS.md
```

## Environment Variables

```txt
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
ADMIN_PASSWORD=
```

Do not expose `SUPABASE_SERVICE_ROLE_KEY` to the browser.

## Supabase Schema

Use this as the initial SQL schema.

```sql
create table h2h_games (
  id uuid primary key default gen_random_uuid(),
  slug text unique not null,
  series_name text not null,
  game_number int not null,
  away_team text not null,
  home_team text not null,
  starts_at timestamptz not null,
  locks_at timestamptz not null,
  status text not null default 'draft',
  created_at timestamptz default now(),
  updated_at timestamptz default now()
);

create table h2h_players (
  id uuid primary key default gen_random_uuid(),
  external_player_id text,
  full_name text not null,
  short_name text not null,
  team text not null,
  position text,
  headshot_url text,
  created_at timestamptz default now(),
  updated_at timestamptz default now()
);

create table h2h_matchups (
  id uuid primary key default gen_random_uuid(),
  game_id uuid not null references h2h_games(id) on delete cascade,
  player_a_id uuid not null references h2h_players(id),
  player_b_id uuid not null references h2h_players(id),
  title text not null,
  description text,
  created_at timestamptz default now(),
  updated_at timestamptz default now()
);

create table h2h_races (
  id uuid primary key default gen_random_uuid(),
  game_id uuid not null references h2h_games(id) on delete cascade,
  matchup_id uuid not null references h2h_matchups(id) on delete cascade,
  race_number int not null,
  player_a_stat text not null,
  player_a_threshold numeric not null,
  player_b_stat text not null,
  player_b_threshold numeric not null,
  display_text_a text not null,
  display_text_b text not null,
  result text,
  result_note text,
  resolved_at timestamptz,
  created_at timestamptz default now(),
  updated_at timestamptz default now(),
  unique(game_id, race_number)
);

create table h2h_users (
  id uuid primary key default gen_random_uuid(),
  anonymous_id text unique not null,
  display_name text not null,
  created_at timestamptz default now(),
  updated_at timestamptz default now()
);

create table h2h_predictions (
  id uuid primary key default gen_random_uuid(),
  game_id uuid not null references h2h_games(id) on delete cascade,
  race_id uuid not null references h2h_races(id) on delete cascade,
  user_id uuid not null references h2h_users(id) on delete cascade,
  choice text not null,
  submitted_at timestamptz default now(),
  created_at timestamptz default now(),
  unique(game_id, race_id, user_id)
);

create table h2h_game_results (
  id uuid primary key default gen_random_uuid(),
  game_id uuid not null references h2h_games(id) on delete cascade,
  user_id uuid not null references h2h_users(id) on delete cascade,
  correct_count int not null default 0,
  valid_race_count int not null default 0,
  accuracy numeric not null default 0,
  created_at timestamptz default now(),
  updated_at timestamptz default now(),
  unique(game_id, user_id)
);
```

## Required Enums As TypeScript Types

Use TypeScript union types.

```ts
export type GameStatus = "draft" | "open" | "locked" | "live" | "final";

export type RaceResult = "PLAYER_A" | "PLAYER_B" | "NEITHER" | "VOID" | null;

export type PredictionChoice = "PLAYER_A" | "PLAYER_B" | "NEITHER";

export type H2HStat =
  | "PTS"
  | "REB"
  | "AST"
  | "BLK"
  | "STL"
  | "TOV"
  | "PF"
  | "FGM"
  | "FGA"
  | "FG3M"
  | "FG3A"
  | "FTM"
  | "FTA"
  | "STOCKS"
  | "PTS_AST"
  | "PTS_REB"
  | "REB_AST";
```

## Seed Data For Game 1

Use seed data so the page works before a full admin CMS exists.

```txt
Series: 2026 NBA Finals
Game: Game 1
Away Team: New York Knicks
Home Team: San Antonio Spurs
Feature: Victor Wembanyama vs Jalen Brunson
```

Players:

```txt
Victor Wembanyama
Short name: Wemby
Team: San Antonio Spurs

Jalen Brunson
Short name: Brunson
Team: New York Knicks
```

Initial races:

```txt
Race 1:
Wemby reaches 3 blocks
Brunson reaches 30 points

Race 2:
Wemby reaches 12 rebounds
Brunson reaches 8 assists

Race 3:
Wemby makes 2 threes
Brunson makes 4 threes

Race 4:
Wemby reaches 20 points
Brunson reaches 10 made field goals

Race 5:
Wemby reaches 4 stocks
Brunson reaches 10 free throw attempts
```

Result options for each race:

```txt
PLAYER_A
PLAYER_B
NEITHER
VOID
```

## Anonymous User Logic

Create a browser anonymous ID.

In `localStorage`, store:

```txt
hoopminds_h2h_anonymous_id
```

If missing, generate with `crypto.randomUUID()`.

Send this ID with prediction submission.

The backend should:

1. Check if user exists by anonymous ID.
2. Create user if missing.
3. Update display name if needed.
4. Insert predictions if game is not locked.
5. Reject duplicate submission for the same game/user.

## Prediction Submission Rules

Backend endpoint:

```txt
POST /api/h2h/submit-predictions
```

Request body:

```ts
{
  gameId: string;
  anonymousId: string;
  displayName: string;
  predictions: {
    raceId: string;
    choice: "PLAYER_A" | "PLAYER_B" | "NEITHER";
  }[];
}
```

Validation:

* game exists
* current time is before `locks_at`
* game status is `open`
* display name is 1-20 characters
* exactly one prediction per race
* all five races answered
* choices are valid
* user has not already submitted for this game

## Result Calculation

When admin finalizes game:

1. For every user who submitted predictions for the game:
2. Load all predictions.
3. Load all race results.
4. Ignore races where result is `VOID` or `null`.
5. Count correct predictions.
6. Store in `h2h_game_results`.

Pseudo-code:

```ts
validRaces = races.filter(race => race.result !== null && race.result !== "VOID")

correct = predictions.filter(prediction => {
  const race = validRaces.find(r => r.id === prediction.race_id)
  return race && race.result === prediction.choice
}).length

accuracy = validRaces.length === 0 ? 0 : correct / validRaces.length
```

## Leaderboard Query

Global leaderboard should aggregate across all finalized games.

Columns:

```txt
display_name
correct_total
valid_total
accuracy
games_played
```

Sort:

```txt
correct_total desc
accuracy desc
valid_total desc
earliest_submission asc
```

## Admin MVP

Admin route:

```txt
/admin/h2h
```

Use simple password protection.

Admin can:

* see active game
* see all five races
* set each race result
* set result note
* finalize game

No need for a polished CMS for MVP.

## UI Component Requirements

### MatchupHeader

Shows:

* series name
* game number
* teams
* lock status
* Player A vs Player B
* short matchup description

### RaceCard

Props:

```ts
race
playerA
playerB
selectedChoice
onSelect
disabled
```

Shows:

```txt
Who gets there first?
[Player A milestone]
[Player B milestone]
[Neither]
```

### PredictionForm

Handles:

* display name
* all selected choices
* submit
* validation errors
* disabled state after lock

### ResultsSummary

Shows:

* score
* accuracy
* race-by-race breakdown
* leaderboard CTA

### LeaderboardTable

Shows:

* rank
* name
* score
* accuracy
* games played

## Page States

### Open

Before lock.

User can submit.

### Already Submitted

Show locked picks. Do not allow editing.

### Locked

After lock time.

No submissions accepted.

### Final

Results visible.

## Copy Examples

Hero:

```txt
HoopMinds H2H
Five player races. One Finals game.
Make your calls before tipoff.
```

Race prompt:

```txt
Who gets there first?
```

Submit:

```txt
Submit Calls
```

Locked:

```txt
Your calls are in. Come back after the game to see your score.
```

Postgame:

```txt
You went 4/5.
Series accuracy: 80%.
```

Footer disclaimer:

```txt
HoopMinds H2H is a free basketball prediction challenge. No betting, no odds, no paid entry, and no cash prizes.
```

## Build Order

### Step 1

Set up Next.js project structure and Supabase helpers.

### Step 2

Create Supabase schema and seed Game 1 data.

### Step 3

Build `/h2h` page with static/database-loaded game data.

### Step 4

Build prediction form and local anonymous ID.

### Step 5

Build submit predictions API route.

### Step 6

Build locked/already-submitted state.

### Step 7

Build admin result entry route/page.

### Step 8

Build result calculation.

### Step 9

Build `/h2h/results`.

### Step 10

Build `/h2h/leaderboard`.

### Step 11

Polish mobile UI and copy.

## MVP Cut List

If time is short, cut:

1. player headshots
2. live progress
3. share image cards
4. friends leaderboard
5. regional leaderboard
6. automated NBA API fetching

Do not cut:

1. five race cards
2. prediction submission
3. lock logic
4. database persistence
5. admin result entry
6. results calculation
7. leaderboard

## Done Checklist

* `/h2h` renders active game
* all five races render
* user can choose one option per race
* display name works
* anonymous ID persists
* backend rejects incomplete predictions
* backend rejects after lock
* backend prevents duplicate submissions
* admin can set race results
* final scores calculate correctly
* leaderboard aggregates scores correctly
* UI has no gambling language
* footer disclaimer exists
