# Too Many Checks

A deliberately over-instrumented GitHub repository: a playground for seeing how many useful checks GitHub can run on every change.

The repository starts language-neutral and validates its own automation, documentation, dependencies, and security posture. It combines focused GitHub Actions workflows with broad linters and supply-chain scanners.

## Checks included

| Area | Checks |
|---|---|
| Workflow correctness | actionlint, zizmor, workflow YAML validation |
| Repository hygiene | EditorConfig, Prettier, markdownlint, codespell |
| Security | CodeQL, Gitleaks, Trivy, OpenSSF Scorecard |
| Dependencies | Dependabot, dependency review on pull requests |
| Broad linting | MegaLinter (YAML, Markdown, JSON, repository hygiene) |
| CI matrix | Ubuntu, Windows, macOS; multiple Python and Node versions |
| GitHub-native | Independent workflow jobs, concurrency cancellation, PR checks |

## Repository settings to enable

Some protections are configured in GitHub Settings rather than repository files:

1. Enable **Secret scanning**, **Push protection**, and **Dependabot alerts** (where available).
2. Enable **Code security** and **Dependency graph**.
3. Protect `main`; require pull requests and select the workflow jobs you want as required checks.
4. Enable signed commits / vigilant mode if appropriate for contributors.
5. Consider enabling merge queue, auto-merge, and stale-branch updates.

GitHub plan and repository visibility affect availability of some security features. A workflow can report a check, but it cannot switch on every account-level security feature.

## Local validation

Install [actionlint](https://github.com/rhysd/actionlint), [zizmor](https://github.com/zizmorcore/zizmor), and [pre-commit](https://pre-commit.com/), then run:

```sh
actionlint
zizmor .github/workflows
pre-commit run --all-files
```

## Design note

More checks are not automatically better. This project favors independent, explainable checks and keeps scheduled or heavyweight scans separate from fast pull-request feedback. Third-party actions should be reviewed and pinned to immutable commit SHAs before use in a high-trust production environment.
