# Orchestra migration notes

## Why we are moving

The legacy cron fleet has no dependency awareness: a pipeline starts at its cron tick whether or not
its upstream finished. During the January freeze incident three downstream jobs read a half-written
partition. Orchestra gives us declared dependencies, retries with backoff and per-run lineage, which is
why the platform group is migrating the fleet wave by wave.

## How a wave works

A wave is a fixed list of pipeline keys. The list is published by the platform group, not derived from
the repository, because a wave also has a staffing and a freeze-window meaning. Pipelines that are not
on the wave list are simply not part of that cutover, even if they look healthy in the repository.

## What the manifest is used for

The manifest is the only artefact the platform group trusts during a cutover review. Runtime pins,
integration-suite counts, retry settings and the `depends_on` graph are all read from it. If a manifest
disagrees with what is deployed, the manifest wins and the pipeline is corrected afterwards.

## Known rough edges

* `archive-indexer` is still on the old Python 3.7 image and is scheduled to be retired rather than
  migrated; it is deliberately left out of the wave lists.
* `billing-ledger-sync` is the oldest pipeline in the fleet and has been on the migration backlog since
  the first wave.
* Several pipelines declare dependencies on pipelines that belong to other waves. Those dependencies
  are handled by the wave owner, not by the pipeline owner.
