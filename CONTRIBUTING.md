# Contributing

Thanks for helping improve this checks showcase.

## Before opening a pull request

- Keep checks focused: do not add another tool when an existing job already tests the same thing.
- Prefer conditional workflows for optional artifacts and language-specific projects.
- Use least-privilege workflow permissions.
- Pin third-party actions to reviewed immutable commit SHAs when preparing changes for high-trust use.
- Never commit API keys, signing keys, keystores, passwords, or private test data.
- For APK and AAB examples, use synthetic or freely redistributable test artifacts only.

## Pull request titles

Use Conventional Commit-style titles:

- feat: add a distinct check
- fix: repair an existing workflow
- docs: improve documentation
- ci: change automation
- security: improve security controls
- chore: maintain dependencies or repository configuration

Optional scopes are welcome, for example: feat(android): validate APK alignment.

## Validate locally

Run the checks relevant to the files you changed:

```sh
actionlint
zizmor .github/workflows
pre-commit run --all-files
```

Markdown changes are checked with markdownlint-cli2, codespell, Prettier, and Lychee in GitHub Actions. Shell scripts are checked with ShellCheck and shfmt. Dockerfiles are checked with Hadolint.

## Review expectations

Explain what a new check detects, why it is not redundant, when it runs, and whether it requires repository settings or secrets. A check that cannot run without a credential or artifact should skip safely and document that requirement.
