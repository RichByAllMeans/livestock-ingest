# livestock-ingest

ETL/ELT entry point for onboarding **external ranches** (the subscription model)
and importing their existing records into the platform, plus bulk loads (e.g.
initial herd, historical health logs).

## Flow

```
source file/API  →  staging tables  →  validation  →  load into operational schema
```

- Raw data lands in a `staging` schema (never touches production tables directly).
- Validation rejects/queues bad rows instead of aborting the whole batch.
- Successful rows are upserted into `livestock-db` tables.

## Required animal fields

New animal records must supply `tag_number`, `species` and `sex`, plus at least
one of `date_of_birth` or `acquisition_date`. Imports that cannot provide the
new required fields are loaded with `legacy_defaults` and flagged
`is_legacy = true` (see `mappings/animals.example.json`), so quality reports can
exclude or separate them.

## Layout

```
staging/    DDL for the staging schema (toward livestock-db, kept here while WIP)
mappings/   field mappings per source (CSV/JSON) → platform columns
samples/    example input files
```

## Status

Scaffold only — the loader implementation (TypeScript, matching the agreed
stack) lands once onboarding sources are confirmed with the client.

## Notes

- Everything is **tenant-scoped**: an import targets exactly one `tenant_id`.
- Keep raw dumps out of git (they contain real animal/financial data).
