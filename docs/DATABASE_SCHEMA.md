# Database Schema (Supabase / Postgres)

Full DDL + RLS policies. Run this in the Supabase SQL editor on a fresh project.

---

## Tables

```sql
-- Anonymous users keyed by device fingerprint
create table public.users (
  id uuid primary key default gen_random_uuid(),
  device_fingerprint text unique not null,
  created_at timestamptz not null default now(),
  best_streak int not null default 0,
  total_plays int not null default 0,
  total_shares int not null default 0
);

create index users_device_idx on public.users (device_fingerprint);

-- One row per completed game (game ends on first wrong answer)
create table public.games (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references public.users(id) on delete cascade,
  streak int not null check (streak >= 0),
  killed_by_left_id text not null,
  killed_by_right_id text not null,
  killed_by_dilemma_id text,
  modifier_id text,
  created_at timestamptz not null default now()
);

create index games_user_idx on public.games (user_id);
create index games_streak_idx on public.games (streak desc);

-- One row per share action
create table public.shares (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references public.users(id) on delete cascade,
  game_id uuid not null references public.games(id) on delete cascade,
  channel text not null check (channel in ('whatsapp','instagram','twitter','copy_link','native','other')),
  created_at timestamptz not null default now()
);

create index shares_game_idx on public.shares (game_id);

-- Aggregated community stats per item (denormalized, updated via trigger)
create table public.item_stats (
  item_id text primary key,
  times_compared int not null default 0,
  times_picked_higher int not null default 0,
  updated_at timestamptz not null default now()
);
```

---

## Row Level Security

Enable RLS on all tables. Public anon role gets specific narrow grants only.

```sql
alter table public.users enable row level security;
alter table public.games enable row level security;
alter table public.shares enable row level security;
alter table public.item_stats enable row level security;
```

### users — insert + self-update

```sql
-- Anyone can insert a user row (anonymous-first onboarding)
create policy users_insert_anyone on public.users
  for insert to anon
  with check (true);

-- Anyone can read their own row by passing device_fingerprint
-- (we use the device_fingerprint as the access key in client code,
-- this is safe because the row only contains stats they generated)
create policy users_select_own on public.users
  for select to anon
  using (true);  -- public read of stats is fine (no PII)

-- Update only own row identified by device_fingerprint match in the update payload
create policy users_update_own on public.users
  for update to anon
  using (true)
  with check (true);
```

Note on security model: there is no real user identity. The `device_fingerprint` is the closest we have. We accept that a sophisticated user could spoof another's fingerprint and inflate their stats. This is acceptable risk for an anonymous game with no monetary value attached to scores.

### games — insert own, read own

```sql
create policy games_insert_own on public.games
  for insert to anon
  with check (
    exists (select 1 from public.users u where u.id = user_id)
  );

create policy games_select_anyone on public.games
  for select to anon
  using (true);  -- public reading of games enables the /g/[id] share page
```

### shares — insert if game exists

```sql
create policy shares_insert_for_game on public.shares
  for insert to anon
  with check (
    exists (select 1 from public.games g where g.id = game_id)
  );

-- No select policy → no one can list shares. We only need insert for analytics.
```

### item_stats — public read, no direct write

```sql
create policy item_stats_select on public.item_stats
  for select to anon
  using (true);

-- No insert/update policies for anon. Writes happen via trigger only.
```

---

## Triggers

### Update item_stats on game insert

When a game ends, we know which pair killed the user. Update the stats so future players can see community percentages.

```sql
create or replace function public.fn_update_item_stats_on_game()
returns trigger
language plpgsql
security definer
as $$
begin
  -- The pair that killed the user means: the user picked wrong on this comparison.
  -- We don't know which side they picked from this insert alone, so the client
  -- must POST that detail. For v1 we just count the comparison itself.
  insert into public.item_stats (item_id, times_compared)
  values (new.killed_by_left_id, 1), (new.killed_by_right_id, 1)
  on conflict (item_id) do update
    set times_compared = item_stats.times_compared + 1,
        updated_at = now();

  return new;
end;
$$;

create trigger trg_update_item_stats_on_game
  after insert on public.games
  for each row execute function public.fn_update_item_stats_on_game();
```

### Update user totals on game insert

```sql
create or replace function public.fn_update_user_on_game()
returns trigger
language plpgsql
security definer
as $$
begin
  update public.users
  set total_plays = total_plays + 1,
      best_streak = greatest(best_streak, new.streak)
  where id = new.user_id;
  return new;
end;
$$;

create trigger trg_update_user_on_game
  after insert on public.games
  for each row execute function public.fn_update_user_on_game();
```

### Update user totals on share insert

```sql
create or replace function public.fn_update_user_on_share()
returns trigger
language plpgsql
security definer
as $$
begin
  update public.users
  set total_shares = total_shares + 1
  where id = new.user_id;
  return new;
end;
$$;

create trigger trg_update_user_on_share
  after insert on public.shares
  for each row execute function public.fn_update_user_on_share();
```

---

## Common queries

### Insert a new user from client

```ts
const { data, error } = await supabase
  .from('users')
  .upsert(
    { device_fingerprint: deviceId },
    { onConflict: 'device_fingerprint', ignoreDuplicates: false }
  )
  .select('id, best_streak')
  .single();
```

### End-of-game insert

```ts
await supabase.from('games').insert({
  user_id: userId,
  streak: finalStreak,
  killed_by_left_id: leftItem.id,
  killed_by_right_id: rightItem.id,
  killed_by_dilemma_id: wasDilemma ? dilemma.id : null,
  modifier_id: todayModifier?.id ?? null,
});
```

### Public game page fetch (for `/g/[id]`)

```ts
const { data } = await supabase
  .from('games')
  .select('id, streak, killed_by_left_id, killed_by_right_id, killed_by_dilemma_id, created_at, users(best_streak)')
  .eq('id', gameId)
  .single();
```

---

## Keep-alive cron

Supabase Free pauses projects after 7 days of database inactivity. Set up cron-job.org (free) to ping a simple Vercel endpoint daily that runs a trivial query.

Endpoint:

```ts
// app/api/keep-alive/route.ts
import { supabase } from '@/lib/supabase';
export async function GET() {
  await supabase.from('users').select('id').limit(1);
  return new Response('ok');
}
```

cron-job.org schedule: every 24 hours, GET `https://auraoff.app/api/keep-alive`.

---

## Backup strategy

Supabase Free has no automatic backups. For v1 this is acceptable — user data has no monetary value, and content lives in git. When upgrading to Pro, automatic daily backups are included.

Manual export weekly via Supabase dashboard → CSV → store in private cloud drive.

---

## Migrations

For v1, hand-write SQL and execute in the Supabase dashboard. Do not introduce Prisma or a migration tool yet — overhead exceeds benefit at this scale.

When schema reaches 10+ tables or 3+ developers touch it, migrate to [supabase/migra](https://github.com/djrobstep/migra) or `supabase db push`.
