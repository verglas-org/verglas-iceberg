# Verglas fork of apache/iceberg-rust

This repository is [verglas-org/iceberg-rust](https://github.com/verglas-org/iceberg-rust),
a fork of [apache/iceberg-rust](https://github.com/apache/iceberg-rust).

## Branching

| Branch | Base | Purpose |
|--------|------|---------|
| `verglas/v0.10.1` | upstream tag `v0.10.1` | Patches carried while Verglas stays on the 0.10.x stack |
| `verglas/v0.9.1` | upstream tag `v0.9.1` | Retired; kept for history |

Pin Verglas (and any consumer) to a **commit SHA** on `verglas/v0.10.1`, not a floating branch tip.

## Patches on `verglas/v0.10.1`

1. **`TableCommit::from_parts`** (`crates/iceberg/src/catalog/mod.rs`)
   Upstream keeps `TableCommit` construction private so clients must use
   `Transaction`. In 0.9.1 that API cannot express replace or
   `RemoveSnapshots`. Verglas compaction (and post-compact snapshot expiry)
   builds those commits at the manifest/metadata layer and needs a public
   constructor to hand them to `Catalog::update_table`.

0.10.1 shipped `Transaction::expire_snapshots` and `update_statistics`, which
retired the RemoveSnapshots half of the need; `from_parts` remains only for the
replace/overwrite commit, which still has no public transaction action. Drop
the patch when upstream grows one (or accepts `from_parts`). Prefer upstreaming
over growing this fork.
