# Renovate CI gate

`renovate-ci-gate.yml` decides whether a project CI workflow should run.

For the configured base branch, it returns `run_ci=false` for:

- push events where the normalized head commit matches the configured release
  commit prefix followed by a semver version
- push events where the commit is associated with a Renovate pull request

It returns `run_ci=true` for pull requests and other events.

It also returns `reason` to make the decision visible to caller workflows:

- `release_commit`
- `renovate_pr`
- `pull_request`
- `non_base_ref`
- `default`

## Usage

In a project workflow:

```yaml
jobs:
  gate:
    uses: rhapsodic-dev/renovate-ci-gate/.github/workflows/renovate-ci-gate.yml@v1
    with:
      base_branch: master
      release_commit_prefix: "chore: release v"

  test:
    needs: gate
    if: needs.gate.outputs.run_ci == 'true'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - run: pnpm ci
      - run: pnpm run typecheck
      - run: pnpm run build
```

The caller workflow must grant enough permissions for the gate to read commits
and associated pull requests:

```yaml
permissions:
  contents: read
  pull-requests: read
```

`base_branch` is optional and defaults to `master`. For projects that use
`main`, pass:

```yaml
jobs:
  gate:
    uses: rhapsodic-dev/renovate-ci-gate/.github/workflows/renovate-ci-gate.yml@v1
    with:
      base_branch: main
```

`release_commit_prefix` is optional and defaults to `chore: release v`. The
gate trims leading whitespace from the head commit message, then checks that the
message starts with the configured prefix followed by a semver version, such as:

- `chore: release v0.20.2`
- `chore: release v0.20.2-beta.1`
- `chore: release v0.20.2+build.1`

## License

[MIT License](./LICENSE)
