# rdk-discoverability

**English** | [Русский](README.ru.md)

GitHub Action wrapper for [repo-aeo](https://github.com/WhiteBite/repo-aeo) — the Repo Discoverability Kit. It runs a discoverability audit on every pull request, posts the score and findings as a PR comment, gates the run on a minimum score, and can open an autofix PR when the audited repository opts in.

## Why

A one-time discoverability audit decays. Six months later a contributor's
PR "tidies up" the README, the quickstart sinks below line 60, the tests
stay green — and the repository quietly disappears from search results and
AI answers. This action turns the audit into a CI gate: every pull request
gets the score and the findings as a PR comment, and the run fails when the
score drops below `min_score`.

```markdown
<!-- rdk-discoverability-audit -->
## Discoverability audit — 71/100 (grade D)

`quickchart` · 38/44 checks passed · 1 error · 2 warnings

### Top findings

🔴 **No install/run commands in the first 60 lines**
   - why: Readers (and agents summarising the repo) decide within seconds
     whether the project works for them.
   - fix: Move a copy-pasteable install + run block above the fold. Use the
     quickstart section of .discoverability/project.yml as the source of truth.
```

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
      - uses: WhiteBite/rdk-discoverability@v1
        with:
          min_score: ${{ vars.RDK_MIN_SCORE || 0 }}
```

That is the whole setup: GitHub downloads the action, the action pulls the
engine from npm. Nothing to fork, nothing to install — and exactly two files
ever appear in your repository: this workflow and the generated
`.discoverability/project.yml` (created by `npx repo-aeo init`).

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `min_score` | `0` | Fail the run when the Discoverability Score is below this value |
| `online` | `true` | Probe outbound links and read live GitHub metadata |
| `cli` | `npx --yes repo-aeo@^1.1.0` | Command that invokes the CLI (an in-repo copy works too) |
| `comment` | `true` | Publish/update the PR comment with the report |

The audit is read-only; the only write is the PR comment. Autofix PRs open only when the audited repository sets `safety.allow_autofix: true` in its `.discoverability/project.yml`.

## Versions

The action is a thin runner over the `repo-aeo` CLI, and its tags track the
engine version: the `sync-engine` workflow watches repo-aeo releases and
bumps the pinned caret spec, tags the engine version and moves the major tag
automatically — no manual sync. Use `WhiteBite/rdk-discoverability@v1` to
stay on the current major, or pin an exact version tag.

## License

MIT
