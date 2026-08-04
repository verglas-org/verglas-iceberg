# Verglas fork of apache/iceberg-rust

This repository is [verglas-org/verglas-iceberg](https://github.com/verglas-org/verglas-iceberg),
a fork of [apache/iceberg-rust](https://github.com/apache/iceberg-rust).

## Branching

| Branch | Base | Purpose |
|--------|------|---------|
| `verglas/v0.10.0` | [lakekeeper/iceberg-rust](https://github.com/lakekeeper/iceberg-rust) @ `2056f3e` | Current. The single `iceberg` source for the `verglas` repository |
| `verglas/v0.10.1` | upstream tag `v0.10.1` | Retired; superseded by `verglas/v0.10.0` |
| `verglas/v0.9.1` | upstream tag `v0.9.1` | Retired; kept for history |

Pin Verglas (and any consumer) to a **commit SHA** on `verglas/v0.10.0`, not a floating branch tip.

## Why the base changed

The `verglas` repository absorbed the Lakekeeper catalog into its own
workspace. Before that, the engine tree and the catalog tree each depended on a
crate named `iceberg` from a different source: the engine on `verglas/v0.10.1`
(upstream's 0.10.1 patch release), the catalog on `lakekeeper/iceberg-rust`.
One `[workspace.dependencies]` table admits one `iceberg` key and a patch table
cannot redirect a direct git dependency, so the two could not share a
workspace.

This branch resolves that by carrying the Verglas patch on the
`lakekeeper/iceberg-rust` base, so the repository resolves one `iceberg`.
Note that base is the Lakekeeper project's own fork of apache/iceberg-rust, a
separate upstream repository — unrelated to where the Verglas catalog code
lives.

Note the version reads *backwards*: this branch is `0.10.0` while
`verglas/v0.10.1` was `0.10.1`. It is still strictly newer. Upstream 0.10.1 is a
patch release off the 0.10.0 line; the Lakekeeper base instead tracks upstream
`main` past 0.10.0 (26 commits: Variant support, DataFusion 54, the encryption
and KMS work) and never took the 0.10.1 version bump. Consumers must therefore
patch `iceberg-catalog-rest`, `iceberg-datafusion`, and `iceberg-storage-opendal`
to this fork as well — the crates.io 0.10.1 copies of those require
`iceberg ^0.10.1` and will not accept this one.

## Patches on `verglas/lakekeeper`

1. **`TableCommit::from_parts`** (`crates/iceberg/src/catalog/mod.rs`)
   Apache upstream keeps `TableCommit` construction private so clients must go
   through `Transaction`, which has no public replace/overwrite action. Verglas
   compaction and post-compact snapshot expiry build those commits at the
   manifest/metadata layer and need a public constructor for
   `Catalog::update_table`.

   **This patch is now optional.** The Lakekeeper base independently removed the
   `#[builder(build_method(vis = "pub(crate)"))]` attribute from `TableCommit`,
   so `TableCommit::builder()` is already public on this branch. `from_parts`
   survives only as a named alias keeping the five Verglas call sites unchanged
   (`verglas-iceberg`: `src/compaction.rs`, `src/retention.rs`, and three in
   `tests/compaction.rs`). Porting those to the builder retires this fork's last
   delta and lets Verglas depend on `lakekeeper/iceberg-rust` directly.

Prefer upstreaming over growing this fork.
