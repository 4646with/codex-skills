---
name: codex-macos-gh-tls
description: Diagnose and repair GitHub CLI TLS failures that occur in Codex on macOS, especially Go x509 or OSStatus errors when the same command works in Terminal.
metadata:
  short-description: Fix gh TLS errors in Codex on macOS
---

# Diagnose `gh` TLS failures in Codex on macOS

Use this skill when `gh` fails certificate verification in Codex on macOS, particularly when the same command succeeds in a regular Terminal. Separate TLS trust-path failures from GitHub authentication failures before changing settings.

## Diagnose

1. Capture the exact error from a harmless authenticated read such as `gh api user --jq .login`. Do not print tokens or credential-store contents.
2. Check `gh auth status` separately. A TLS error happens before GitHub can validate the login; do not ask the user to log in again unless auth status shows an actual auth problem.
3. Identify the `gh` binary (`command -v gh`) and whether the failure is specific to Codex. Errors such as `x509: OSStatus -26276` can indicate that a Go TLS client in a macOS sandbox cannot use the system trust service. This is an evidence-based possibility, not proof that the Mac has duplicate certificates.
4. Locate an existing, readable CA bundle appropriate for the machine. On Homebrew installations, inspect `brew --prefix` and whether the `ca-certificates` bundle exists under that prefix. Do not assume a path or create an empty bundle.
5. Test a candidate bundle for one command only:

   ```sh
   SSL_CERT_FILE="/absolute/path/to/ca-bundle.pem" gh api user --jq .login
   ```

   If this succeeds while the unmodified command fails, it supports a CA discovery or sandbox trust-path problem. If it fails, do not persist the setting; continue investigating the actual TLS error, proxy/interception, bundle validity, or network path.

## Repair when the user requested a fix

When the one-command test succeeds, configure the verified absolute bundle path for Codex-launched commands by merging this value into the existing `[shell_environment_policy.set]` table in the active Codex config (`$CODEX_HOME/config.toml` when `CODEX_HOME` is set, otherwise `~/.codex/config.toml`):

```toml
SSL_CERT_FILE = "/absolute/path/to/ca-bundle.pem"
```

Preserve all other settings and avoid creating a duplicate TOML table. Validate the TOML and confirm the file exists and is readable. Then fully restart Codex so new command processes inherit the setting.

After restart, verify in a fresh Codex command environment:

```sh
printf 'SSL_CERT_FILE=%s\n' "${SSL_CERT_FILE:-unset}"
gh api user --jq .login
gh auth status
```

The repair is complete only when the variable points to the verified bundle, the API read succeeds, and auth status is healthy.

## Boundaries

- Keep TLS verification enabled. Never recommend `GIT_SSL_NO_VERIFY`, insecure flags, or disabling certificate checks as a fix.
- Prefer selecting a valid CA bundle for Codex-launched commands over changing macOS-wide trust or weakening sandbox policy.
- Do not delete certificates, import a project certificate into the system keychain, or modify unrelated project-specific CA settings to fix GitHub CLI access.
- If a candidate CA bundle does not fix the problem, stop before persistent changes. Broader sandbox changes such as allowing access to `trustd` affect the security boundary and require separate evidence and explicit user authorization.
- Community reports describe related Go TLS/macOS sandbox failures: [sandbox-runtime issue #125](https://github.com/anthropics/sandbox-runtime/issues/125) and [forgectl issue #1253](https://github.com/cameronsjo/forgectl/issues/1253). Treat them as reports, not proof of the current machine's root cause.
- Codex's configuration reference documents environment settings: [Config Reference](https://developers.openai.com/codex/config-reference/).
