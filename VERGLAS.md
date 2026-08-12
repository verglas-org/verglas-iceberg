# Verglas fork of apache/iceberg-rust

This private repository is
[`verglas-org/verglas-iceberg`](https://github.com/verglas-org/verglas-iceberg),
maintained from the open-source
[`apache/iceberg-rust`](https://github.com/apache/iceberg-rust) project.

## Branching

| Branch | Base | Purpose |
|--------|------|---------|
| `main` | upstream `main` | Current upstream plus the minimal Verglas compatibility patch |

Pin Verglas consumers to a commit SHA, not a floating branch tip. Keep the
open-source repository configured as the local `upstream` remote.

## Carried patch

1. **`TableCommit::from_parts`** (`crates/iceberg/src/catalog/mod.rs`)
   Upstream keeps `TableCommit` construction private so clients must use
   `Transaction`. Its current action set still cannot express the replace
   commit required by Verglas compaction, which builds the commit at the
   manifest and metadata layer before handing it to `Catalog::update_table`.

Drop this patch when upstream exposes an equivalent overwrite or replace action.
Prefer upstreaming over growing this fork.
