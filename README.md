# rust-crate-template

[![GitHub repo](https://img.shields.io/badge/github-nyurik/rust--crate--template-8da0cb?logo=github)](https://github.com/nyurik/rust-crate-template)
[![crates.io version](https://img.shields.io/crates/v/rust-crate-template)](https://crates.io/crates/rust-crate-template)
[![crate usage](https://img.shields.io/crates/d/rust-crate-template)](https://crates.io/crates/rust-crate-template)
[![docs.rs status](https://img.shields.io/docsrs/rust-crate-template)](https://docs.rs/rust-crate-template)
[![crates.io license](https://img.shields.io/crates/l/rust-crate-template)](https://github.com/nyurik/rust-crate-template/blob/main/LICENSE-APACHE)
[![CI build status](https://github.com/nyurik/rust-crate-template/actions/workflows/ci.yml/badge.svg)](https://github.com/nyurik/rust-crate-template/actions)
[![Codecov](https://img.shields.io/codecov/c/github/nyurik/rust-crate-template)](https://app.codecov.io/gh/nyurik/rust-crate-template)

A template repository for new Rust library crates. It bundles the tooling and
CI shared across these crates so a new library starts with everything wired up.

## Using this template

* Create a new repository from this template (GitHub's *Use this template*
  button, or copy the files).
* Search/Replace all cases of `rust-crate-template`, `rust--crate--template`, and `rust_crate_template`.
* Update the `description`, `keywords`, `categories`, and `rust-version` in [`Cargo.toml`](Cargo.toml).
* In GitHub project settings, modify:
  * `General Page`
     * `Enable release immutability Loading` - set
     * `Wiki` - unset (rarely useful)
     * `Projects` - unset (rarely useful)
     * `Pull Requests`
       * disallow `merge` and `rebase` - keeping history cleaner
       * `squash` - default message to `Pull request title and description`
     * `Always suggest updating pull request branches` - set
     * `Allow auto-merge` - set
     * `Automatically delete head branches` - set
* Set up releases, see [Setting up releases](#setting-up-releases) below
* Update this file

For a **binary** crate instead of a library, rename `src/lib.rs` to
`src/main.rs` (or add one alongside), and remove it from `.gitignore`

### Setting up releases

Releases are automated by [release-plz](https://release-plz.dev) in the `release-plz` job of
[`ci.yml`](.github/workflows/ci.yml). On every push to `main` that passes CI it publishes the crate if
`Cargo.toml` has a newer version than crates.io, and otherwise opens or updates a release PR with the next version
and changelog. Merging that PR publishes the release. One-time setup:

* In `ci.yml`, change the `github.repository_owner == 'nyurik'` guard if the repository lives under another owner.
  The guard keeps forks from attempting to release.
* **crates.io:** the job authenticates with
  [trusted publishing](https://crates.io/docs/trusted-publishing) (`id-token: write`), so no crates.io token is needed.
  Add the repository and the `ci.yml` workflow as a trusted publisher in the crate's settings on crates.io.
  As of writing, a trusted publisher can only be added to a crate that already exists, so publish the first version
  manually with `cargo publish`.
* **`RELEASE_PLZ_TOKEN`:** a GitHub token is also needed, because pull requests, tags, and merges made with the default
  `GITHUB_TOKEN` do not trigger other workflows. Without it CI does not run on the release PR (blocking the merge if CI
  is a required check), and a merged Dependabot PR does not start a release run.
  * Create a [fine-grained personal access token](https://github.com/settings/personal-access-tokens/new):
    * *Resource owner*: the user or organization that owns the repository. Organizations can require owner approval for
      fine-grained tokens, or disable them.
    * *Repository access*: `Only select repositories`, choosing just this repository.
    * *Repository permissions*: `Contents` and `Pull requests` set to `Read and write`. `Metadata` (read-only) is added
      automatically, and nothing else is needed.
    * Pick an expiration, and put the renewal date in a calendar. An expired token silently stops release PRs.
  * Save it under the name `RELEASE_PLZ_TOKEN` in **both** *Settings → Secrets and variables → Actions* and
    *Settings → Secrets and variables → Dependabot*. Dependabot-triggered runs, such as the auto-merge workflow in
    [`dependabot.yml`](.github/workflows/dependabot.yml), cannot read the Actions secrets.
  * A [GitHub App](https://release-plz.dev/docs/github/token) token (e.g. via `actions/create-github-app-token`) works
    with the same permissions, does not expire, and is not tied to a person, at the cost of a more involved setup.

### What's included

* **CI** ([`.github/workflows/ci.yml`](.github/workflows/ci.yml)) — build,
  test, MSRV check with minimal dependency versions, code coverage, and
  automated releases via [release-plz](https://release-plz.dev).
* **[`justfile`](./justfile)** — common developer tasks (`test`, `clippy`,
  `fmt`, `coverage`, `msrv`, `semver`, `cpd`, …).
* **Lints** — shared `clippy` and `rustc` lint configuration in
  [`Cargo.toml`](Cargo.toml) and [`clippy.toml`](clippy.toml).
* **Formatting** — [`.editorconfig`](.editorconfig),
  [`tomlfmt.toml`](tomlfmt.toml), and a
  [`.pre-commit-config.yaml`](.pre-commit-config.yaml).
* **Dependabot** — grouped dependency and GitHub Actions updates with
  auto-merge for patch releases.
* **Copy/paste detection** — [jscpd](https://github.com/kucherenko/jscpd),
  configured in [`.jscpd.json`](.jscpd.json). Run `just cpd` locally; on pull
  requests, CI posts a summary comment highlighting newly added clones.
* **Dual licensing** — MIT and Apache-2.0.

## Development

* This project is easier to develop with [just](https://github.com/casey/just#readme), a modern alternative to `make`.
  Install it with `cargo install just`.
* To get a list of available commands, run `just`.
* To run tests, use `just test`.

## License

Licensed under either of

* Apache License, Version 2.0 ([LICENSE-APACHE](LICENSE-APACHE) or <https://www.apache.org/licenses/LICENSE-2.0>)
* MIT license ([LICENSE-MIT](LICENSE-MIT) or <https://opensource.org/licenses/MIT>)
  at your option.

### Contribution

Unless you explicitly state otherwise, any contribution intentionally
submitted for inclusion in the work by you, as defined in the
Apache-2.0 license, shall be dual-licensed as above, without any
additional terms or conditions.
