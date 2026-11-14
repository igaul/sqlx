# Temporary SQLite security backport for Cryer

Base: upstream SQLx `v0.9.0`, commit
`75bc0487eb661da811bb7a3c5d158f1bd463fef4`.

The sole functional change widens `sqlx-sqlite`'s `libsqlite3-sys` upper bound
from `<0.38.0` to `<0.40.0`, allowing the patched 0.39 series. It backports the
result of upstream commits `94aafe3a68884d923b0798a767c8d7f6cfda89d2` and
`10462fa4414c6c7f1ed1d39bb31678e3b726878c` without their intervening changes.
SQLx runtime source is unchanged from the release.

Reason: [RUSTSEC-2026-0459](https://rustsec.org/advisories/RUSTSEC-2026-0459.html)
affects bundled SQLite in libsqlite3-sys before 0.39.0. Consumers must also update
their lockfiles to a patched version (Cryer targets 0.39.1); widening this range
alone does not upgrade an existing lockfile or make its older versions safe.

Use an exact Git revision in a root `[patch.crates-io]` override. SQLx's workspace
crates must resolve consistently to avoid duplicate sqlx-core types. Independently
rooted client/example workspaces need their own overrides when they build Cryer
through development dependencies. No crates.io publication is intended.

Validation and deployment status belong to the consuming application's PR and
gate evidence. The fork adds no workflows and does not authorize hosted CI.

Removal: once a published SQLx release supports patched libsqlite3-sys, remove
the Git overrides, update all affected lockfiles, verify that no fork dependency
remains, and rerun the advisory check, real-server acceptance and full Cryer gate.
Do not delete the fork or branch until the owner approves; historical revision
pins must stay available for reproducible older builds.
