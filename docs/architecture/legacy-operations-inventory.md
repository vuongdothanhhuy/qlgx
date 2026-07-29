# Legacy Import, Merge, Backup, and Statistics Inventory

## Purpose

This document completes the repository-only command inventory for LEG-006.
It identifies the compiled entry points for imports, database merging,
backup/restore, and `frmThongKeChung`, then gives every command an explicit
preserve, replace, or retire decision.

“Preserve” refers to an approved user outcome, never to reuse of WinForms,
Access, Office automation, background threads, ZIP handling, or raw SQL.
Statistics formulas remain hypotheses for STA-001 and domain review.

## Method and scope

The inventory follows call sites from `Source/ChuongTrinh/frmMain.cs` into:

- `Source/ChuongTrinh/frmImport.cs`;
- `Source/GXControl/ImportData.cs`;
- `Source/GXControl/ImportDataMGC.cs`;
- `Source/ChuongTrinh/frmMergeData.cs`;
- `Source/GXControl/MergeData.cs`;
- `Source/ChuongTrinh/frmRestore.cs`;
- `Source/ChuongTrinh/GxCheckVersion.cs`;
- `Source/ChuongTrinh/frmThongKeChung.cs`;
- `Source/GXControl/GxThongKeChung.cs`.

All are compiled by the primary solution. UTF-16 LE files were decoded for
inspection. No database was opened and no credential-bearing diff or value was
printed.

## Decision vocabulary

| Decision | Meaning |
|---|---|
| **Preserve outcome / replace implementation** | The capability or result remains a candidate requirement, implemented through controlled modern workflows |
| **Replace** | The operational need remains, but the legacy command and storage mechanism do not |
| **Retire** | The behavior is prohibited or has no target equivalent |

## Import commands

| ID | Legacy command and evidence | Observed behavior | Decision |
|---|---|---|---|
| OPS-IMP-01 | MGC Access import: `frmMain.cs:615-619`, `frmImport.cs:263-267`, `ImportDataMGC.cs` | Opens `.mdb`/`.accdb`; validates an MGC-shaped database; permits parish selection for multi-parish sources; imports/reconciles sub-parishes, households, people, household members, marriages, and baptism/First Communion/confirmation data; creates sacrament batches; emits progress/errors | **Preserve outcome / replace implementation.** Build the restricted, one-time migration importer with explicit source version, parish selection, dry run, stable legacy keys, per-row disposition, reconciliation, and rerun safety. Do not put live files in the web client or repository |
| OPS-IMP-02 | General Excel import: `frmMain.cs:815-819`, `frmImport.cs:268-271`, `ImportData.cs:230-912` | Opens `.xls`/`.xlsx` sheet `Table1`; imports people, households, membership, marriage, sub-parish, identifiers, and partial/blank dates; generates identity/household codes in some cases | **Replace.** Preserve the accepted template contract only after field mapping and examples are approved. Implement server-side staged validation/preview/commit with synthetic fixtures; do not preserve Jet/Office connection-string fallbacks |
| OPS-IMP-03 | Catechism import path: `ImportData.cs:56-76` and `ImportData.cs:76-229` | The shared import worker can route to `ImportGiaoLy`, add students to a class, and report an imported count | **Preserve outcome / replace implementation.** Treat as a catechism-specific staged import with class selection, person matching, duplicate handling, preview, audit, and row-level results |
| OPS-IMP-04 | Pause/resume/cancel import: `frmImport.cs:39-90` | Runs a mutable worker thread; pause/resume uses deprecated thread suspension; cancel warns that stopping mid-process can corrupt data and sets a shared flag | **Retire the mechanism; preserve safe cancellation.** Each modern import is a durable job with transaction/staging boundaries, idempotent steps, explicit cancel states, and no partially committed domain graph |
| OPS-IMP-05 | Import log/status: `frmImport.cs:99-196`, `ImportData.cs`, `ImportDataMGC.cs` | Displays progress and errors and appends row/process details to `ImportLog.txt` in the install directory | **Replace.** Store a protected run manifest, sanitized operator summary, and row-level dispositions in restricted storage; never log credentials or unrestricted parish values |

The target has one shared pipeline—`inspect → map → stage → validate → preview
→ approve → commit → verify`—with source adapters for legacy Access, MGC, and
any approved spreadsheet template. Adapters do not write domain tables
directly.

## Merge command

| ID | Legacy command and evidence | Observed behavior | Decision |
|---|---|---|---|
| OPS-MRG-01 | Merge another QLGX database: `frmMain.cs:621-625`, `frmMergeData.cs`, `MergeData.cs` | Upgrades/validates the selected source; independently enables normal/forced overwrite for people, households, marriages, sacraments, and sub-parishes; imports transfers; compares update dates; classifies duplicate/not-updated, newer-updated, forced-updated, new, not-found, and same-name/different-members outcomes; exports person/household result grids | **Replace.** Do not retain database-to-live-database merge. The migration pipeline proposes identity matches and discrepancies in staging; an authorized person approves every merge/overwrite with a reason. Both legacy keys survive, changes are audited, and source/load/merge/skip/unresolved counts reconcile |
| OPS-MRG-02 | Pause/resume/cancel merge: `frmMergeData.cs:92-155` | Uses the same mutable-thread pattern and warns of mid-process corruption | **Retire the mechanism.** Use durable, resumable, idempotent jobs with atomic commit boundaries |
| OPS-MRG-03 | Forced overwrite choices: `frmMergeData.cs:108-120`, `MergeData.cs:53-125` | Operator can force person, household, marriage, and sacrament replacement | **Retire unrestricted force-overwrite.** Replacement is an explicit field-level conflict decision with before/after values, authorization, reason, and audit; immutable/source identifiers are never silently overwritten |
| OPS-MRG-04 | Merge result export: `frmMergeData.cs:42-82`, `MergeData.cs:630-636`, `1044-1050` | Exports person and household outcome grids to spreadsheet files and writes `ImportLog.txt` | **Replace.** Produce a restricted reconciliation artifact keyed by import run and legacy identifiers, with no automatic publication or repository storage |

## Backup and restore commands

| ID | Legacy command and evidence | Observed behavior | Decision |
|---|---|---|---|
| OPS-BKR-01 | Manual “Sao lưu” from `frmMain.cs:682-729` | ZIPs only `giaoxu.mdb` to a temporary file, asks for a destination, copies it, then deletes the temporary archive | **Replace.** Database backups are encrypted, automated, access-controlled PostgreSQL backups with retention, monitoring, checksums, and restore drills. A separately authorized user export is not a database backup |
| OPS-BKR-02 | Automatic local backup from `GxCheckVersion.cs:183-190`, `374-427` | On startup/version processing, writes a dated local ZIP, keeps approximately 40 ZIPs, and suppresses some operational context | **Replace.** Scheduled server-side backup policy is independent of user login/startup and exposes health/failure alerts to operators |
| OPS-BKR-03 | List local backup snapshots: `frmRestore.cs:19-38` | Enumerates ZIP files from the install-directory `backup` folder | **Replace.** Authorized operators see managed recovery points with timestamps, status, scope, retention, and restore eligibility |
| OPS-BKR-04 | Create backup from restore screen: `frmRestore.cs:40-50` | Delegates to the main form's manual ZIP backup | **Replace.** Same target as OPS-BKR-01; avoid duplicate client-side backup paths |
| OPS-BKR-05 | Delete local snapshot: `frmRestore.cs:53-77` | Permanently deletes the selected ZIP after confirmation | **Retire direct deletion.** Retention is policy-controlled; exceptional deletion requires authorization and an audit record |
| OPS-BKR-06 | Restore listed snapshot: `frmRestore.cs:79-108` | Confirms, extracts an `.mdb`, overwrites the live database, and restarts the application | **Replace.** Restore is an operator-run, audited procedure into an isolated target first, followed by integrity verification and an explicit promotion/cutover |
| OPS-BKR-07 | Restore arbitrary `.mdb` or ZIP: `frmRestore.cs:110-177` | Accepts a selected database/archive, copies the first matching `.mdb` over the live file, and restarts | **Retire arbitrary overwrite.** Legacy files enter only the restricted migration intake and validation pipeline; they never overwrite production storage |
| OPS-BKR-08 | Dormant remote ZIP upload: `GxCheckVersion.cs:422`, `485-527` | Upload code exists, but its call is commented; it would transmit a database archive to a legacy HTTP service | **Retire and prohibit.** Covered by NET-012 in `legacy-network-inventory.md`; clients never upload database backups |

The modern acceptance test is a timed restore from an encrypted backup into an
isolated environment, with recorded recovery time, recovery point, integrity
checks, and promotion decision. Merely producing an archive is insufficient.

## Statistics commands

`GxThongKeChung.SetCombobox` defines 15 visible statistic types. Results render
through person, household, or marriage lists; row counts are displayed; a
sub-parish filter can include its children; date-based queries can optionally
include missing dates and archived records. Result lists can be printed and
filtered again.

All 16 outcomes are **preserved provisionally and reimplemented**. STA-001 must
approve formulas, date-boundary semantics, archive/transfer/death inclusion,
age thresholds, marriage status definitions, and partial-date behavior before
any becomes authoritative.

| ID | Visible statistic | Observed dimensions | Decision |
|---|---|---|---|
| OPS-STA-01 | Sinh ra (births) | From/to date, missing-date option, archive option, sub-parish | **Preserve outcome / replace implementation** |
| OPS-STA-02 | Rửa tội (baptisms) | From/to date, missing-date option, archive option, sub-parish | **Preserve outcome / replace implementation** |
| OPS-STA-03 | Xưng tội - rước lễ lần đầu (First Confession/Communion) | From/to date, missing-date option, archive option, sub-parish | **Preserve outcome / replace implementation** |
| OPS-STA-04 | Thêm sức (confirmation) | From/to date, missing-date option, archive option, sub-parish | **Preserve outcome / replace implementation** |
| OPS-STA-05 | Hôn phối (marriages) | From/to date, missing-date option, sub-parish, status: unclassified / regular / convalidated / civil-lawful / divorced / separated | **Preserve outcome / replace implementation** |
| OPS-STA-06 | Qua đời (deaths) | From/to date, missing-date option, archive option, sub-parish | **Preserve outcome / replace implementation** |
| OPS-STA-07 | Tổng số giáo dân (total parishioners) | As-of date/year, sub-parish hierarchy; active/archive semantics require review | **Preserve outcome / replace implementation** |
| OPS-STA-08 | Tổng số gia đình (total households) | Not year-dependent in the UI; archive/transfer and sub-parish filters | **Preserve outcome / replace implementation** |
| OPS-STA-09 | Tân tòng (converts by baptism date) | Baptism date range, missing-date option, archive option, sub-parish | **Preserve outcome / replace implementation** |
| OPS-STA-10 | Chủ hộ (household heads) | Age range, missing-age/date option, archive option, sub-parish | **Preserve outcome / replace implementation** |
| OPS-STA-11 | Gia trưởng | Age range, missing-age/date option, archive option, sub-parish; domain definition unknown | **Preserve outcome / replace implementation** |
| OPS-STA-12 | Hiền mẫu | Age range, missing-age/date option, archive option, sub-parish; domain definition unknown | **Preserve outcome / replace implementation** |
| OPS-STA-13 | Cao niên (seniors) | Configured lower age, missing-age/date option, archive option, sub-parish | **Preserve outcome / replace implementation** |
| OPS-STA-14 | Giới trẻ (youth) | Configured age range, missing-age/date option, archive option, sub-parish | **Preserve outcome / replace implementation** |
| OPS-STA-15 | Thiếu nhi (children) | Configured age range, missing-age/date option, archive option, sub-parish | **Preserve outcome / replace implementation** |
| OPS-STA-16 | In / lọc lại kết quả (print/refilter result) | Acts on whichever person/household/marriage result list is visible | **Preserve outcome / replace implementation.** Use permission-aware export/print with an audit event and the approved result contract |

## Cross-cutting replacement requirements

- Imports, merges, exports, and restores are online-only administrative
  operations with explicit roles and immutable audit events.
- Every migration/import run records source checksum, adapter version, operator,
  parish selection, start/end state, counts, warnings, errors, and approval.
- A run balances `source = loaded + merged + skipped + unresolved`; critical
  unresolved people, household, sacrament, and marriage rows must be zero at
  cutover.
- Duplicate matching proposes candidates; it never silently merges.
- Reported errors and downloadable artifacts remain restricted and expire
  under a documented retention policy.
- Statistics query normalized PostgreSQL/domain projections. They do not copy
  concatenated Access SQL or infer unknown dates.
- Print/export does not broaden data access: the export contains only rows and
  fields the requesting role may view.

## Completion matrix

| Requirement | Evidence |
|---|---|
| Import commands classified | OPS-IMP-01–05 cover MGC, general Excel, catechism, cancellation, and results |
| Merge commands classified | OPS-MRG-01–04 cover merge scope, cancellation, overwrite, and reconciliation output |
| Backup/restore commands classified | OPS-BKR-01–08 cover manual/automatic backup, listing, deletion, restore, arbitrary files, and dormant upload |
| `frmThongKeChung` commands classified | OPS-STA-01–15 reconcile to all 15 visible combo choices; OPS-STA-16 covers result print/refilter |
| Every command has a decision | 33 command rows each state preserve/replace/retire; no row is undecided |
| Security boundary preserved | No database or credential content was inspected; target artifacts remain restricted |
