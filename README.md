# Too Many Checks

A deliberately over-instrumented GitHub repository: a playground for seeing how many useful, distinct checks GitHub can run on every change.

The repository is language-neutral by default. Checks that need a particular artifact (Android APKs, Android App Bundles, shell scripts, Dockerfiles, Gradle projects) activate only when that input exists.

## Check catalogue

| Area | Distinct checks |
|---|---|
| Workflow security | CodeQL for Actions, zizmor, actionlint, SECURITY.md disclosure policy |
| Supply chain | OpenSSF Scorecard, OSV-Scanner, Dependabot, GitHub dependency review |
| Repository security | Gitleaks, Trivy filesystem / IaC / license scan |
| Android APKs | ZIP integrity, manifest/component exposure review, DEX inventory, signing schemes and certificates, zip alignment, APKID packer detection, APKLeaks secret scan, JADX decompilation and risky-API heuristics |
| Android App Bundles | Bundletool structural validation, module manifest inspection, JAR signature verification |
| Documentation | markdownlint-cli2, codespell, Lychee external-link validation |
| General configuration | yamllint, JSON/TOML validation, Prettier JSONC formatting, pre-commit hygiene, HTML5/XML/SVG parser validation |
| Python | Ruff lint/format, Bandit security rules, mypy typing, pip-audit dependency review (only when Python sources exist) |
| JavaScript / TypeScript | npm audit, declared lint/test scripts, TypeScript compiler validation (only with a root package manifest) |
| Infrastructure as code | Terraform fmt/validate and Checkov policy checks (only when IaC files exist) |
| Archives and binaries | ZIP/JAR/WAR/EAR/AAR/TAR/GZIP integrity, path traversal, duplicate-entry and decompression-bomb checks |
| Shell | ShellCheck and shfmt (only when shell scripts exist) |
| Containers | Hadolint (only when Dockerfiles exist) |
| CI compatibility | 24-way OS / Python / Node runtime smoke-test matrix |
| Pull requests | Semantic PR-title validation, dependency review, automatic labels |
| Project hygiene | License inventory, SPDX SBOM, stale issue/PR management |
| Releases | Source archive, SHA-256 checksums, build-provenance attestation |

The suite permits substantial overlap: two checks may inspect the same artifact or risk category and still count separately when they use different engines, rulesets, heuristics, validation stages, or data sources. The goal is distinct signal rather than artificially minimizing repeated coverage.

## Android artifact checks

Place APKs and AABs anywhere in the repository (preferably under artifacts/ or dist/). The corresponding workflow activates only when that artifact type is present.

APK validation checks:

- ZIP archive integrity and APK package metadata.
- Application ID, minimum SDK, target SDK, and declared permissions.
- APK signing certificate and signature validity.
- 4-byte ZIP alignment.
- Debuggable and cleartext-traffic manifest flags.
- APKID packer/compiler indicators.
- APKLeaks for embedded API keys and other hard-coded secrets.
- JADX decompilation with heuristic flags for risky WebView, TLS, cipher, file-mode, and plain-HTTP API patterns.
- Manifest review for exported components without component-level permissions, backup-enabled apps, and test-only builds.

AAB validation checks:

- Bundletool's App Bundle structure and module validation.
- Base module manifest extraction.
- JAR signing and signature verification.

Unsigned APKs and AABs fail signature verification by design. Do not commit production signing keys or passwords; use GitHub Actions secrets for signing workflows.

## Repository settings to enable

Some protections are configured in GitHub Settings rather than repository files:

1. Enable **Secret scanning**, **Push protection**, and **Dependabot alerts** (where available).
2. Enable **Dependency graph** so GitHub Dependency Review can inspect pull requests.
3. Protect main; require the checks you want as required status checks.
4. Enable signed commits / vigilant mode if appropriate for contributors.
5. Consider enabling merge queue, auto-merge, and stale-branch updates.

GitHub plan and repository visibility affect availability of some security features. A workflow can report a check, but it cannot switch on every account-level security feature.

## Local validation

Install actionlint, zizmor, and pre-commit, then run:

```sh
actionlint
zizmor .github/workflows
pre-commit run --all-files
```

## Design note

More checks are not automatically better. This project favors independent, explainable checks and keeps heavyweight or artifact-specific scans conditional. Third-party actions should be reviewed and pinned to immutable commit SHAs before use in a high-trust production environment.
