# QLGX Security Boundary

## Purpose

This document defines what may enter the QLGX repository, what must remain in restricted storage, how every commit is checked, and how the known legacy Access credential is handled.

The boundary applies to developers, parish staff, migration operators, CI systems, screenshots, issue trackers, pull requests, logs, backups, and AI or external-tool consultations.

## Non-negotiable rules

- Never commit passwords, API keys, access tokens, private keys, `.env` files, parish databases, production exports, photos, or restricted migration work.
- Never paste production parish data or credential values into AI tools, issue trackers, chat, email, screenshots, test fixtures, or documentation.
- Never use production parish data in automated tests, demos, visual baselines, or local sample datasets.
- Never use `git add -f` to bypass a repository ignore rule for a restricted file.
- Treat every secret exposed in a commit, log, screenshot, or external tool as compromised until the responsible owner decides otherwise.
- Use synthetic data for tests. Sanitized aggregates are allowed only after a second person confirms that individuals cannot be re-identified.

## Data classification

| Class | Examples | Repository | Approved handling |
|---|---|---|---|
| Public project material | Source code without secrets, architecture documents, synthetic fixtures, `.env.example` containing names only | Allowed | Normal review and CI |
| Internal legacy evidence | Form behavior, SQL structure, schema names, report layouts, changelog entries | Allowed with care | Do not reproduce credential values or production records |
| Restricted parish data | Live `.mdb`/`.accdb`, photos, configured `CauHinh` values, deployed templates containing parish customization, extracts, staging rows, reconciliation details | Forbidden | Authorized local storage only; least privilege; no cloud sync |
| Secrets | Passwords, tokens, keys, connection strings containing credentials, recovery codes | Forbidden | Approved secret manager or protected runtime input; never logged |
| Generated sensitive output | PDFs, XLSX/CSV/JSON exports, screenshots, import reports containing row details | Forbidden | Restricted output directory; delete according to the intake record |
| Sanitized evidence | Aggregate counts and synthetic examples derived from observed value classes | Allowed after review | Record reviewer and sanitization method; no direct values or stable identifiers |

## Repository controls

The root `.gitignore` is the first defense, not proof that a commit is safe. It blocks:

- `.env` and `.env.*`, while allowing `.env.example`;
- Access data and lock files;
- restricted data, legacy-install, migration-work, and photo directories;
- generated output, logs, temporary files, and test artifacts;
- Node/Angular dependencies and project-local C# build output.

The tracked root `BIN/` and `Release/` directories are legacy evidence and are intentionally not ignored. Review changes there explicitly. The tracked blank seed at `Source/ChuongTrinh/Resources/giaoxu.mdb` is a historical exception; do not replace it with a live parish file or add another database.

## Restricted data intake

Before accepting a parish package, record:

1. the authorizing person and parish;
2. the operator receiving the package;
3. the complete file list: live database, install/photo tree, configured settings, and deployed templates;
4. the source checksum and collection time;
5. the restricted local directory and its access list;
6. the retention deadline and deletion owner.

Store the package under `.restricted/<intake-id>/` or an equivalent encrypted location outside the repository. The directory must be accessible only to named operators. Do not place it in iCloud, OneDrive, Dropbox, Google Drive, a shared home directory, or an automatically backed-up developer folder unless that service and account were explicitly approved for parish data.

Mount or copy the original as read-only. Perform extraction against a working copy. Keep the database, relative photo tree, settings, and deployed templates together so provenance is not lost.

Only sanitized aggregate results may leave restricted storage. Before publication, a second person checks that the output contains no names, addresses, dates tied to people, free text, filenames derived from people, stable legacy keys, or small groups that enable re-identification.

At the retention deadline, the deletion owner removes working copies and records the date, scope, method, and any approved retained backup.

## Commit procedure

Run these checks before every commit from the repository root. They intentionally report only a blocking status for secret-like content; they do not print the matching value.

### 1. Inspect the exact staged scope

```bash
git status --short
git diff --cached --name-only
```

Stage explicit paths. Do not use `git add .` for work that touched restricted directories.

### 2. Block restricted filenames

```bash
if git diff --cached --name-only |
  rg -q '(^|/)(\.env($|\.)|.*\.(mdb|accdb|ldb|laccdb|p12|pfx|pem|key)$|parish-data/|restricted-data/|legacy-install/|migration-work/|parish-photos/|legacy-photos/|photos/)'
then
  printf 'BLOCK: restricted file class is staged\n'
  exit 1
fi
```

The existing blank seed is not permission to stage a database change. Any intentional maintenance of that historical artifact requires a separate security review.

### 3. Protect files containing the accepted legacy exposure

The following tracked files contain or may render the known legacy Access credential:

- `Source/DBAccess/GxConstants.cs`
- `Source/Giaoly/app.config`
- `Source/Giaoly/Properties/Settings.settings`
- `Source/Giaoly/Properties/Settings.Designer.cs`
- `Source/ChuongTrinh/frmMergeData.cs`
- `BIN/Giaoly.dll.config`

If any is staged, stop normal review. Inspect locally with zero context so an unchanged credential is not printed accidentally:

```bash
git diff --cached --unified=0 -- \
  Source/DBAccess/GxConstants.cs \
  Source/Giaoly/app.config \
  Source/Giaoly/Properties/Settings.settings \
  Source/Giaoly/Properties/Settings.Designer.cs \
  Source/ChuongTrinh/frmMergeData.cs \
  BIN/Giaoly.dll.config
```

Do not paste that diff into an external reviewer. A change to a credential-bearing line requires approval from the security owner.

### 4. Scan added lines without printing matches

```bash
if git diff --cached --no-ext-diff --unified=0 |
  sed -n '/^+++ /d; /^+/p' |
  rg -q '(?i)(password|passwd|pwd|secret|api[_-]?key|access[_-]?token|private[_-]?key)[[:space:]]*[:=][[:space:]]*[^[:space:]#<]{4,}'
then
  printf 'BLOCK: potential secret assignment in staged additions\n'
  exit 1
fi
```

This pattern is a minimum safety net, not an entropy scanner. Before application CI is enabled, adopt an approved dedicated secret scanner, pin its version, store only its configuration in git, and prove it detects synthetic test patterns without printing their values.

### 5. Finish staged review

```bash
git diff --cached --check
git diff --cached --stat
git diff --cached
```

Do not run the final full diff command when step 3 identified a legacy credential-bearing file; use the zero-context local review instead. Confirm `.env` remains ignored:

```bash
git check-ignore --no-index -q .env
git check-ignore --no-index -q parish-data/example/giaoxu.mdb
! git check-ignore --no-index -q .env.example
```

## History procedure

Commit `208e298` is the legacy-exposure baseline for this boundary. It does **not** certify earlier history as secret-free. It records the point after which new content must satisfy this procedure.

Before pushing a branch, scan additions since the baseline without printing matches:

```bash
if git diff 208e298..HEAD --no-ext-diff --unified=0 |
  sed -n '/^+++ /d; /^+/p' |
  rg -q '(?i)(password|passwd|pwd|secret|api[_-]?key|access[_-]?token|private[_-]?key)[[:space:]]*[:=][[:space:]]*[^[:space:]#<]{4,}'
then
  printf 'BLOCK: potential secret assignment exists after the security baseline\n'
  exit 1
fi
```

Check object names for newly committed restricted files:

```bash
git rev-list --objects 208e298..HEAD |
  cut -d' ' -f2- |
  rg -q '(^|/)(\.env($|\.)|.*\.(mdb|accdb|ldb|laccdb|p12|pfx|pem|key)$|parish-data/|restricted-data/|legacy-install/|migration-work/|photos/)'
```

No output and exit status `1` means no path matched. Exit status `0` blocks the push. Investigate locally with filenames only; do not print file content.

The full history is expected to trigger a credential-content scan because of the accepted legacy exposure below. Do not label the repository “secret-free.” If an unrelated secret is suspected in old history, run an approved scanner locally, report findings without values, rotate the secret first, and obtain owner approval before rewriting shared history.

## Accepted permanent legacy exposure

The Access credential embedded in the legacy application is accepted as permanent legacy exposure because:

- it has already been distributed in compiled desktop installations and committed in multiple tracked files;
- every existing Access database depends on it, so rotation would require reissuing the legacy product and database files;
- it protects a local file already controlled by the operator and does not provide a meaningful modern confidentiality boundary;
- rewriting git history would not retract copies already distributed.

Acceptance does not permit reuse. Never reproduce the value in new documentation, configuration, tests, logs, screenshots, issues, or application code. The importer receives any required database credential at runtime through protected operator input or an approved secret mechanism. PostgreSQL, application, backup, and deployment credentials must be new and independently managed.

The old value must not influence password policy, encryption keys, session secrets, or generated defaults. The modern system treats possession of a legacy `.mdb` as possession of sensitive parish data regardless of the file password.

## AI and external-tool boundary

- Production parish data, photos, exports, settings values, and credentials never enter AI or external review tools.
- Do not submit diffs from the six credential-bearing legacy files to an external reviewer.
- Use repository-only structural facts or a sanitized architecture brief when seeking external review.
- Synthetic examples must be invented independently, not lightly edited production rows.
- Tool output must not print matching secret values. Prefer boolean detection, counts, and filenames.

## Incident response

If a restricted file or secret is found before commit:

1. stop the commit;
2. unstage the file with `git restore --staged <path>`;
3. move restricted data to approved storage;
4. remove generated copies and logs;
5. notify the owner if the value appeared in a tool, terminal recording, screenshot, or shared location;
6. rotate any modern secret that may have been exposed.

If exposure is committed but not pushed, stop and notify the owner. Do not amend, reset, or rewrite history until the exact exposure and recovery action are approved.

If exposure is pushed or otherwise shared, assume compromise. Revoke or rotate first, preserve evidence, notify the responsible owner, then coordinate history cleanup. History rewriting is containment hygiene, not a substitute for rotation.

The accepted legacy Access credential follows its documented exception. Any other credential, including a reused copy of that value in new code, follows the normal incident process.

## Review and ownership

The project owner approves this boundary and any exception. Re-review it:

- before the first real parish package is accepted;
- before application CI or deployment credentials exist;
- after any secret or privacy incident;
- when storage, hosting, AI tooling, or the migration team changes.

Every exception records its owner, reason, affected data, start date, expiry date, and compensating controls. Permanent exceptions require an explicit rationale, as documented for the legacy Access credential above.
