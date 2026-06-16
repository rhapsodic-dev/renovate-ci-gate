# Renovate CI gate

`renovate-ci-gate.yml` decides whether a project CI workflow should run.

For the configured base branch, it returns `run_ci=false` for:

- push events where the head commit starts with `chore: release v`
- push events where the commit is associated with a Renovate pull request

It returns `run_ci=true` for pull requests and other events.

## Usage

In a project workflow:

```yaml
jobs:
  gate:
    uses: rhapsodic-dev/renovate-ci-gate/.github/workflows/renovate-ci-gate.yml@v1
    with:
      base_branch: master

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

## License

[MIT License](./LICENSE)
