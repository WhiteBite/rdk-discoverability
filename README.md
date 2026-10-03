# rdk-discoverability

**English** | [Русский](README.ru.md)

GitHub Action wrapper for [repo-aeo](https://github.com/WhiteBite/repo-aeo) — the Repo Discoverability Kit. It runs a discoverability audit on every pull request, posts the score and findings as a PR comment, gates the run on a minimum score, and can open an autofix PR when the audited repository opts in.

## Usage

```yaml
name: rdk-audit
on:
  pull_request:
  schedule:
    - cron: '0 6 * * 1'

permissions:
  contents: read
  pull-requests: write

jobs:
  audit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: WhiteBite/rdk-discoverability@v0.3.7
        with:
          min_score: ${{ vars.RDK_MIN_SCORE || 0 }}
```

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `min_score` | `0` | Fail the run when the Discoverability Score is below this value |
| `online` | `true` | Probe outbound links and read live GitHub metadata |
| `cli` | `npx --yes repo-aeo@^0.3.0` | Command that invokes the CLI (an in-repo copy works too) |
| `comment` | `true` | Publish/update the PR comment with the report |

The audit is read-only; the only write is the PR comment. Autofix PRs open only when the audited repository sets `safety.allow_autofix: true` in its `.discoverability/project.yml`.

## Versions

The action is a thin runner over the `repo-aeo` CLI, and its tags track the engine version. `WhiteBite/repo-aeo/action@vX` — the in-repo composite action — stays supported for existing consumers.

## License

MIT
