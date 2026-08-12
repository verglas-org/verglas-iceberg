<!--
  Licensed to the Apache Software Foundation (ASF) under one
  or more contributor license agreements.  See the NOTICE file
  distributed with this work for additional information
  regarding copyright ownership.  The ASF licenses this file
  to you under the Apache License, Version 2.0 (the
  "License"); you may not use this file except in compliance
  with the License.  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing,
  software distributed under the License is distributed on an
  "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
  KIND, either express or implied.  See the License for the
  specific language governing permissions and limitations
  under the License.
-->

# Apache Iceberg Rust — Agent Instructions

This file provides repository-specific guidance for automated agents working
in this repository.

## Repository identity

- Treat this as the private Verglas Iceberg integration: https://github.com/verglas-org/verglas-iceberg.
- Use https://github.com/apache/iceberg-rust as the canonical open-source origin.
- Use https://github.com/verglas-org/iceberg-rust only as the sanitized public mirror; never push Verglas product changes there.
- Read `VERGLAS.md` for the complete private patch inventory and removal criteria.
- Keep the repository homepage pointed at the canonical open-source origin.

## Remotes

- `origin`: private `verglas-org/verglas-iceberg` repository.
- `upstream`: open-source `apache/iceberg-rust` repository.
- `public`: sanitized public `verglas-org/iceberg-rust` mirror.
- Base private changes on `origin/main`, which tracks current upstream plus the minimal Verglas compatibility patch.

## Repository structure

- `crates/iceberg/`: core Iceberg metadata, catalog, scan, transaction, reader, and writer APIs.
- `crates/integrations/`: DataFusion, cache, playground, and related integrations.
- `crates/catalog/`: REST, SQL, Glue, Hive Metastore, S3 Tables, and catalog loaders.
- `crates/storage/`: object-storage implementations.
- `crates/integration_tests/`, `crates/test_utils/`, `crates/sqllogictest/`: test infrastructure and end-to-end coverage.
- `bindings/`: language bindings.
- `dev/`, `docs/`, `website/`: development tooling and open-source documentation.
- `.github/workflows/weekly-upstream-sync.yml`: weekly upstream PR creation; conflicts remain open for manual resolution.
- `VERGLAS.md`: private fork identity, upstream relationship, and carried patch inventory.

## Patch discipline

- Carry only `TableCommit::from_parts` until upstream exposes an equivalent overwrite or replace action.
- Do not restore the obsolete `v0.9.1` maintenance stack or the retired DataFusion scan experiment.
- Keep generic Iceberg fixes separate and contribute them to the canonical upstream.
- Preserve Apache licensing headers and generated-file rules.

## Validation

- Run `cargo fmt --all --check`, the narrowest affected tests, and `git diff --check`.
- Run `cargo check -p iceberg` when changing the carried catalog patch.

## Security Model

When assessing potential vulnerabilities or calibrating automated security
findings, use [`SECURITY-THREAT-MODEL.md`](SECURITY-THREAT-MODEL.md) as the
authoritative detailed description of this repository's security boundaries,
trust assumptions, and non-boundaries.
