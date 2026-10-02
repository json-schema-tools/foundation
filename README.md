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

## Versioning and releases

Foundation uses semantic versioning and release-please's simple strategy (version.txt and CHANGELOG.md), without an npm package. The first release is v1.0.0. After successful master CI, Foundation release opens or updates a release PR. Merge that PR to create the version tag and GitHub release. Release PR CI is dispatched explicitly because PR events created by GITHUB_TOKEN do not start CI automatically. No extra secrets or publishing environment are needed.

Use conventional commits: fix: for compatible fixes, feat: for compatible additions, and a ! or BREAKING CHANGE footer for incompatible workflow inputs, permissions, or behavior. Consumers remain pinned to full commit SHAs; adopting a new release requires a consumer PR updating both CI and release workflow pins to the commit referenced by its version tag. Include a comment identifying the release, for example @<full SHA> # v1.0.0. Never move an existing version tag. There are no floating v1 tags or automatic consumer updates.

The reusable release.yml serves consuming repositories. The separate release-foundation.yml versions foundation itself and does not run package publication or deployment steps. Existing consumers can continue using their current reviewed commit while new versions are released.
