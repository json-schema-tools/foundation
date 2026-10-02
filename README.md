# JSON Schema Tools foundation

This repository currently contains project scaffolding and a license. It has no
package, build, tests, or published releases. GitHub Actions checks Conventional
Commit messages on pull requests, replacing the unused CircleCI commands.

Package repositories use CI test matrices, Jest coverage thresholds with
`jest-it-up` and `npm run coverage:bump`, release-please release PRs, and npm
trusted publishing. See [traverse's release guide](https://github.com/json-schema-tools/traverse/blob/master/RELEASING.md)
when adding a package here. Configure trusted publishing for the new package
before enabling its release workflow.
