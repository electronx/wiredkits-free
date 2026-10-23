---
name: safe-migration
description: Write or review a database migration so it deploys without locking tables, breaking running code or losing data. Works with any SQL database (Postgres, MySQL, SQLite) and any migration tool (Prisma, Drizzle, Rails, Django, Alembic, Knex, Flyway, goose and others). Use when asked to "add a column", "write a migration", "rename this field", "add an index", "is this migration safe?", or before merging a schema change.
---

# Safe database migration

You write and review schema changes the way a careful DBA would: in the project's own migration tool, split into steps that never lock a busy table or break the code that is still running during the deploy. You never run anything against a production database.

## 1. Learn the setup

Find out before writing anything:

- **Database and version**: `docker-compose.yml`, `.env.example`, `DATABASE_URL`, CI service images, `schema.prisma` `provider`. Version matters: Postgres 11+ adds a column with a constant default without a rewrite; MySQL 8.0+ can add columns `INSTANT`.
- **Migration tool**: `prisma/migrations`, `drizzle.config.*`, `db/migrate`, `*/migrations/` (Django), `alembic.ini`, `knexfile.*`, Flyway/Liquibase/goose/sqlx folders, EF Core `Migrations/`. Read the last three migrations to copy naming and style.
- **How migrations run on deploy**: before or after the new code starts. If you can't tell, say so: it changes which steps are safe.

Always create migrations with the tool's generator, then edit the result. Never edit a migration that may already have run anywhere: add a new one.

## 2. Check every operation

Look at the actual SQL, not just the ORM call (`prisma migrate diff --script`, `python manage.py sqlmigrate`, `alembic upgrade --sql`, Rails `structure.sql` diff). Flag each risky step:

| Operation | Risk | Safe version |
|---|---|---|
| `CREATE INDEX` | blocks writes for the whole build | Postgres `CREATE INDEX CONCURRENTLY` (outside a transaction); MySQL `ALGORITHM=INPLACE, LOCK=NONE` |
| `ADD COLUMN ... NOT NULL` with no default | fails on a table with rows | add nullable, backfill, then add the constraint |
| `SET NOT NULL` (Postgres) | full scan under an exclusive lock | `ADD CONSTRAINT ... CHECK (col IS NOT NULL) NOT VALID`, `VALIDATE CONSTRAINT`, then `SET NOT NULL` (12+) |
| Add foreign key or check | scans and locks both tables | Postgres `NOT VALID`, then `VALIDATE CONSTRAINT` in a later migration |
| Change column type | usually rewrites the table | new column, backfill, switch, drop later |
| Rename or drop column/table | old code still running breaks | expand and contract (step 3) |
| Volatile default (`now()`, `uuid()`) on an existing table | rewrites the table | add without default, set default, backfill |
| Backfill `UPDATE` of a big table | long lock, huge transaction | batches by primary key in a separate script or migration |

Also: on Postgres, start risky migrations with `SET lock_timeout = '5s'` so a blocked `ALTER` fails fast instead of queueing every query behind it. On MySQL, name `ALGORITHM` and `LOCK` explicitly so the database refuses instead of silently copying the table, and remember DDL is not transactional: a failed migration can be half applied. SQLite tools usually recreate the table; fine for small tables, check foreign keys survive.

If you don't know a table's size, ask, or say the step is only safe for small tables. Concurrent index builds can't run in a transaction: use the tool's switch (Rails `disable_ddl_transaction!`, Django `atomic = False`, Alembic `autocommit_block()`, Prisma a migration file holding only that statement; for other tools, check their docs).

## 3. Expand and contract

For renames, type changes and drops, split the change across deploys so old and new code both work at every moment:

1. **Expand**: add the new column or table. Code writes to both.
2. **Backfill**: copy old data in batches; make it safe to re-run.
3. **Switch**: code reads the new one only.
4. **Contract**: a later migration drops the old one, after the switch is deployed everywhere.

Say which deploy each step belongs to.

## 4. Test it

If a local or test database is available: migrate up, down, and up again, then run the test suite. Check for drift between the schema file and the migrations if the tool supports it (`prisma migrate diff`, `manage.py makemigrations --check`, `alembic check`). Write the down migration, or say plainly that it is irreversible and why (dropped data can't come back).

## 5. Report

Show the migration files, then a short table: each step, the lock or rewrite it takes, and the deploy it ships in. List what you didn't verify (table sizes, the real database version, how deploys run). Don't apply migrations to shared or production databases; give the command and let the user run it.
