# vantage-grid-pipelines

Monorepo for Vantage Grid Analytics' scheduled data pipelines.

Every pipeline is a directory under `pipelines/`, named after its `pipeline_key`, and described by
exactly one manifest file, `pipeline.json`. The manifest is the source of truth for the runtime pin,
the declared test suites, the retry policy and the upstream dependencies of that pipeline.

Scheduling still runs on the legacy cron fleet (`cron-fleet-01` … `cron-fleet-06`). The move to the
Orchestra scheduler is being delivered in waves; see `docs/orchestra_migration_notes.md`.

## Layout

```
pipelines/
  <pipeline_key>/
    pipeline.json
docs/
  orchestra_migration_notes.md
```

## Conventions

* `pipeline_key` is unique and lowercase-kebab-case.
* `depends_on` lists pipeline keys, never dataset names.
* `tests.integration` counts the integration specs registered in the pipeline's `tests/integration`
  folder on the scheduler image; it is the number the migration gate looks at.
