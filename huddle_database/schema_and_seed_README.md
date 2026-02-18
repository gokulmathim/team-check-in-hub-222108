# Huddle Database: Schema + Minimal Seed Data

This container uses PostgreSQL. The connection command is stored in:

- `db_connection.txt`

## How the schema was created

Per project rules, all DDL/DML was executed via CLI using one statement at a time:

```bash
# From this directory:
cd team-check-in-hub-222108/huddle_database

# Connect:
$(cat db_connection.txt)

# Run one statement at a time, e.g.
psql "$(cat db_connection.txt | tr -d '\n')" -c "SELECT 1;"
```

### Extensions enabled

- `pgcrypto` (for `gen_random_uuid()` UUID primary keys)
- `citext` (case-insensitive email)

### Tables created

- `users`
- `teams`
- `team_memberships`
- `team_invites`
- `questions`
- `checkins`
- `checkin_answers`
- `feed_events`
- `notifications`
- `daily_team_metrics`
- `analytics_events`

### Seed data inserted (minimal, dev-only)

Inserted (idempotently where applicable):

- Admin user: `admin@example.com` (id `00000000-0000-0000-0000-000000000001`)
- Member user: `member@example.com` (id `00000000-0000-0000-0000-000000000002`)
- Demo team: `Demo Team` with invite code `DEMO1234` (id `00000000-0000-0000-0000-000000000101`)
- Memberships for both users
- Three default questions
- One sample check-in + answers for `CURRENT_DATE`
- One feed event
- One welcome notification
- One `daily_team_metrics` row for `CURRENT_DATE`

> Notes:
> - Seed passwords are stored as the literal string `dev_only` in `password_hash` as a placeholder for local dev.
> - Backend should replace this with real hashing and auth logic.

## Quick verification queries

```bash
# List tables
psql "$(cat db_connection.txt | tr -d '\n')" -c "SELECT table_name FROM information_schema.tables WHERE table_schema='public' ORDER BY table_name;"

# See demo team
psql "$(cat db_connection.txt | tr -d '\n')" -c "SELECT id, name, invite_code FROM teams;"

# See today's check-ins
psql "$(cat db_connection.txt | tr -d '\n')" -c "SELECT team_id, user_id, checkin_date, summary FROM checkins ORDER BY created_at DESC LIMIT 10;"
```
