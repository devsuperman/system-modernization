# 08 — Data Migration & Coexistence

Data is usually **the hardest part** of a modernization. Code can be rewritten; data must be carried
over correctly, often while the system is in use.

## Typical legacy DB situation

- One **big shared database**, tables used by every form. Some "god tables" with 100+ columns.
- Rules in **triggers and stored procedures**.
- Weak constraints: no FKs, magic values (`'S'/'N'`, `0/1/2`), dates as strings, denormalized data, duplicates.
- Mixed encodings, inconsistent data accumulated over 20 years.
- Sometimes **one DB per customer** (on-prem installations) with schema drift between versions.

## Coexistence strategies (from simplest to most decoupled)

### 1. Shared database (both systems use the same DB)

- ✅ Easiest start; no sync needed; immediate consistency.
- ❌ New code coupled to legacy schema; schema changes can break Delphi; locking/transaction conflicts.
- Mitigation: new code accesses legacy tables **only through an ACL/repository adapter**; new tables
  go in a **new schema**; use **views** to give the new code a clean shape.

### 2. Shared DB with ownership transfer

- Per table/entity, define **who is the owner (writer)** at any given time. When a module migrates, the
  new system becomes the writer; the legacy only reads (convert Delphi screen to read-only or remove it).
- Rule: **one writer per piece of data at any moment.**

### 3. Separate databases + synchronization

- New module has its own DB/schema with a clean model.
- Sync via **CDC / outbox / events** (doc 07), through an ACL, **in one direction** with a clear owner.
- Legacy → new while legacy is the owner; flip to new → legacy when ownership moves (if legacy still
  needs to read it).
- ✅ Clean model, decoupled. ❌ Eventual consistency, sync infrastructure, reconciliation needed.

### 4. Database refactoring (evolve the legacy DB in place)

Patterns from *Refactoring Databases* (Ambler & Sadalage): add the new column/table, keep old and new
in sync during a **transition period** (triggers/views), migrate callers, then remove the old one
(**expand / migrate / contract**, a.k.a. parallel change).

## Migration techniques

| Technique | Description |
|---|---|
| **ETL / bulk migration** | Extract, transform (clean, map), load. One-off or per module at cut-over |
| **Trickle migration** | Continuous incremental sync, so cut-over is short |
| **Lazy migration** | Migrate a record when it's first accessed in the new system |
| **Expand/Contract** | Add new structure → dual write/sync → move readers/writers → remove old |
| **Reconciliation** | Jobs comparing counts, sums, checksums between old and new (e.g. total invoiced per day) |

## Data quality

- **Profile** the data early: nulls, invalid values, duplicates, orphans. Surprises here break estimates.
- Decide per issue: **fix at source**, **fix in transformation**, or **accept and handle in code**.
- Involve the business: some "bad data" is a legitimate business exception.

## Cut-over checklist (per module)

- [ ] Migration scripts tested with a **copy of production data** (anonymized if needed — LGPD/GDPR)
- [ ] Rehearsal runs measuring duration (does it fit the maintenance window?)
- [ ] Reconciliation reports approved by business users
- [ ] Rollback plan (and its limits: what if new data was created after cut-over?)
- [ ] Legacy screens for that module made read-only / removed
- [ ] Monitoring and support team ready ("hypercare" period)

## Tooling in .NET

- **EF Core migrations** (per module/schema) or **DbUp / FluentMigrator / Grate** for SQL-script-based migrations.
- **Dapper** for heavy, SQL-oriented reads and migration scripts.
- **SqlBulkCopy** / provider bulk APIs for large loads.
- **Testcontainers** to test migrations against real database engines in CI.
- DB engine options: keep the current engine first (less risk), migrate engine later — or move to SQL
  Server/PostgreSQL as part of the module migration. **Avoid changing everything at once.**

## Interview one-liner

> *"I'd keep a single writer per piece of data at any moment. Start with the shared database behind an
> anti-corruption layer for speed, put new tables in module-owned schemas, and transfer ownership module
> by module, syncing in one direction through outbox/CDC, always with reconciliation reports the
> business can validate."*
