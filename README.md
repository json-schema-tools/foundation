# Foundation

Reusable GitHub Actions workflows for json-schema-tools repositories.

- `node-ci.yml`: Node 20 and 22, npm ci, repository build/test commands, optional lint/docs and Rust tests.
- `commitlint.yml`: conventional commit checks on pull requests.
- `release.yml`: release-please, explicit CI dispatch for release PRs, npm trusted publishing, optional Pages, Rust crate publishing, generated assets, and AWS schema deployment.

Consumers keep `ci.yml` and `release.yml` as small entry points and pin `uses` to a reviewed foundation commit. Package coverage baselines, `npm run coverage:bump` (jest-it-up), scripts, release-please configuration and manifests stay in each package repository.

The caller owns the `release` environment, permissions and release concurrency. The shared release job uses that caller's environment and built-in GitHub token. npm trusted publishing continues to trust the caller repository's `release.yml`; no npm token or inherited secret is needed. AWS continues to trust the caller's repository/environment identity.

Merge foundation changes first, then consumer updates. To roll out future shared changes, update consumer commit pins in reviewed PRs. Required checks for consumers are `test / test (20.x)` and `test / test (22.x)`; verify their displayed names before changing branch protection.

## Inputs

CI accepts `lint-command`, `build-command`, `test-command`, `docs-command`, `post-build-command`, `node-options`, and `rust-tests`. Defaults build and test with npm, and skip optional steps. Node versions are centrally maintained.

Release accepts `build-command`, `test-command`, `docs-command`, `node-options`, `rust-tests`, `publish-npm` (default true), `pages-path` (empty disables Pages), and `release-assets` (JSON array). Schema deployments additionally enable `deploy-schema` and set `schema-destination`, `cloudfront-distribution`, and optionally `schema-source` and `aws-region`. The caller's release environment must contain `AWS_RELEASE_ROLE_ARN`.

Only trusted repository maintainers should configure command inputs; commands run with the caller's permissions. Releases run after successful master push CI, never pull request CI. Protect the caller's release environment to master and configure package trusted publishers before merging release changes.
