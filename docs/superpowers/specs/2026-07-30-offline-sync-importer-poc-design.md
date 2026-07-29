# Offline Sync and Access Importer PoC Design

**Status:** Approved conversational design, awaiting review of this written specification
**Pilot context:** Phu Long Parish, Phu Cuong Diocese
**Date:** 2026-07-30

## 1. Purpose

This proof of concept validates the two riskiest parts of the legacy modernization:

1. A browser can edit fabricated parishioner data while offline, preserve the work across a reload, synchronize after reconnecting, and expose concurrent-edit conflicts without losing either user's intent.
2. A local migration tool can read a copied Microsoft Access parish database, load a deliberately limited data slice into PostgreSQL, reconcile every source row, and present the imported data read-only without exposing it through source control, logs, screenshots, fixtures, or public demos.

The PoC is not a partial rewrite of the Windows application. It creates the smallest production-shaped foundation that can prove offline synchronization and establish an importer that can later grow into the final migration utility.

## 2. Success Outcomes

### 2.1 Primary outcome: offline edit and synchronization

Using only fabricated seed data:

- Two independent browser contexts represent two fixed demo actors.
- Actor A can go offline, edit one person, reload the page, and still see the local edit and pending mutation.
- Actor B can edit the same person and synchronize first.
- When Actor A reconnects, Actor A's stale mutation is applied, both browsers converge on the same server state, and a visible conflict record preserves the before, competing, and resolved values.
- Replaying the same mutation produces the original result without a duplicate data change, conflict, change-feed entry, or audit event.

### 2.2 Secondary outcome: private local Access import

Using an ignored local copy of the real database:

- The importer reads the initial legacy slice: `GiaoDan`, `GiaDinh`, `ThanhVienGiaDinh`, `GiaoHo`, and `VaiTro`.
- Imported people and households are searchable and viewable in restricted read-only mode.
- A second identical import produces the same reconciliation result and no duplicate target records.
- Source totals reconcile to loaded, merged, intentionally skipped, and unresolved totals.
- No source field value is emitted to terminal output, application logs, test output, generated reports, or tracked files.

## 3. Scope Boundaries

### 3.1 Included

- An Angular browser application with an IndexedDB replica and durable mutation queue.
- A NestJS monolith API.
- PostgreSQL with explicit SQL migrations and Drizzle-based application access.
- Framework-free domain rules and transport contracts shared where appropriate.
- Fabricated deterministic demo data.
- People editing in demo mode.
- People, household, membership, parish-section, and relationship browsing.
- Pull/push synchronization, mutation idempotency, audit events, and visible conflict handling.
- A re-runnable command-line Access importer with reconciliation output.
- A current macOS/Linux extraction adapter based on `mdbtools`.
- A documented adapter boundary for a later Windows ODBC extractor.

### 3.2 Excluded

- Production deployment and live parish cutover.
- Dual writes between Access and PostgreSQL.
- Authentication, password management, and production authorization roles.
- Editing imported real data.
- Household or household-membership editing.
- Reports, report-layout parity, photos, sacraments, accounting, education, and the remaining legacy modules.
- A committed dataset derived row by row from real people, even if names or identifiers are replaced.
- A runtime connection from the web application to Access.
- Modifying or writing to either copied Access database.

## 4. Architecture

The PoC uses a monorepo with these bounded units:

```text
apps/
  web/          Angular application and IndexedDB synchronization client
  api/          NestJS HTTP API and synchronization orchestration
packages/
  domain/       Framework-free validation and domain transitions
  contracts/    Versioned API and importer-neutral contracts
  database/     SQL migrations, Drizzle schema, repositories, and RLS setup
  importer/     Extract, profile, transform, load, and reconcile commands
  testing/      Fabricated builders and cross-package test utilities
infra/
  compose/      Local PostgreSQL configuration
```

Dependencies point inward:

- `domain` has no Angular, NestJS, database, filesystem, or Access dependency.
- `contracts` contains serializable request, response, sync, and neutral-import shapes.
- `database` implements persistence without containing UI or Access extraction logic.
- `importer` depends on neutral contracts and database ports, not on the API's HTTP layer.
- `web` and `api` consume domain behavior rather than redefining validation independently.

Package versions will be selected from verified current releases and pinned during implementation planning. Runtime dependencies must not use floating version ranges.

### 4.1 Runtime topology

For the PoC, the browser talks only to the NestJS API. The API talks only to PostgreSQL. The importer is a separate local command and never runs inside an HTTP request.

```text
Angular + IndexedDB  <---- HTTP ---->  NestJS  <---->  PostgreSQL
                                                   ^
                                                   |
                                      local importer command
                                                   ^
                                                   |
                                         copied Access files
```

### 4.2 Parish isolation

Every parish-owned row carries `parish_id`. PostgreSQL row-level security policies enforce the parish boundary, including in this one-parish PoC. API and importer transactions set a transaction-local parish context before accessing parish-owned tables. Automated tests create a second fabricated parish and prove that its rows cannot be read or changed through the pilot parish context.

The PoC uses two fixed fabricated users as audit actors and does not provide a login screen. This simplifies the demonstration without weakening the database's tenant boundary.

## 5. Data Model

### 5.1 Core tables

- `parishes`: parish identity and stable code.
- `parish_sections`: legacy `GiaoHo` mapping and browse metadata.
- `users`: the two fabricated demo actors and importer system actor.
- `people`: the person aggregate used by browse, edit, and sync.
- `households`: household identity and browse fields.
- `household_memberships`: person-to-household membership.
- `household_relationship_types`: normalized legacy `VaiTro` values.
- `sync_changes`: ordered parish change feed used by pull synchronization.
- `sync_mutation_results`: durable idempotency result keyed by mutation ID.
- `sync_conflicts`: field-level stale-edit evidence and its review state.
- `audit_events`: append-only actor, action, entity, and version metadata.

### 5.2 Shared row metadata

Synchronizable parish-owned records use:

- UUIDv7 primary key.
- `parish_id`.
- Integer `version`, incremented exactly once per accepted mutation.
- `created_at`, `created_by`, `updated_at`, and `updated_by`.
- Nullable `deleted_at` for tombstones visible to synchronization.
- Nullable `legacy_key` unique within its source table and parish.

UUIDv7 generation is centralized so browser-created IDs, server-created IDs, tests, and imports follow one contract.

### 5.3 Person fields

The demo editor covers only:

- display name;
- saint name;
- sex;
- birth date represented as year, optional month, optional day, precision, and preserved raw legacy value;
- parish section;
- record status.

Partial dates are never coerced into invented complete dates. The raw imported representation remains available for discrepancy review but is not exposed in logs.

Households, memberships, relationship types, and imported people are browse-only in this PoC.

## 6. Data Modes and Privacy Boundary

The application has two mutually exclusive modes selected at process start:

- `demo`: loads deterministic, entirely fabricated data; enables the person editor and two demo actors.
- `restricted-import`: loads privately imported data; all domain mutation endpoints reject writes and the UI displays a persistent restricted-data banner.

Read-only enforcement is server-side. Hiding edit controls is only an additional UI safeguard.

Automated tests, committed seeds, documentation examples, screenshots, and demonstrations use fabricated data only. The ignored real database and copied runtime directory remain outside Git. `.env` files remain ignored and must never be committed.

### 6.1 Synthetic data policy

The default demo seed is authored from fictional scenarios and has no row-level relationship to the real database.

An optional private generator may use coarse aggregate characteristics such as record counts, missing-field rates, date-precision distribution, and household-size distribution. It must:

- reject direct source identifiers and free-text fields as generator inputs;
- produce newly sampled fictional rows rather than transformed source rows;
- apply minimum cohort thresholds before using a statistic;
- write only to ignored local output;
- never be required for tests or the public demo.

Row-by-row pseudonymization is out of scope because linked attributes can still re-identify real people.

## 7. Offline Synchronization Design

### 7.1 Client storage

IndexedDB separates three concerns:

1. **Base replica:** the latest server-confirmed entities and sync cursor.
2. **Mutation queue:** ordered durable commands with mutation ID, entity ID, base version, before-and-after values for changed fields, actor, and timestamps.
3. **Local overlay:** the optimistic result obtained by applying pending mutations over the base replica.

This separation makes pending intent recoverable and allows a fresh server pull without erasing offline edits.

### 7.2 Local edit flow

1. The editor validates the proposed transition using shared domain rules.
2. The browser creates a UUIDv7 mutation with the entity's current server version as `baseVersion` and the original value of every changed field.
3. One IndexedDB transaction appends the mutation and updates the overlay.
4. The UI immediately renders the overlay and shows a pending-sync state.
5. A reload reconstructs the same visible state from the base replica and queued mutations.

### 7.3 Reconnect flow

Synchronization runs one cycle at a time per browser:

1. Pull server changes after the local cursor.
2. Apply the new base records and cursor atomically.
3. Reapply pending local mutations to rebuild the overlay.
4. Push queued mutations in original order.
5. Persist each acknowledged result and remove only its matching queue item.
6. Pull once more to converge on the authoritative state and any conflict records.

Failed network calls leave the queue intact. Reconnect, explicit retry, and bounded exponential backoff can trigger another cycle.

### 7.4 Server mutation transaction

Each accepted mutation executes in one PostgreSQL transaction:

1. Establish parish context.
2. Check `sync_mutation_results` for the mutation ID.
3. Lock and read the current entity.
4. Validate the requested field changes.
5. Compare `baseVersion` with the current version.
6. Apply the mutation and increment the entity version.
7. If stale, write conflict evidence for each changed field whose current server value differs from the mutation's original value.
8. Append one `sync_changes` entry and one `audit_events` entry.
9. Store the complete mutation result for idempotent replay.

The transaction commits all steps or none. A repeated mutation ID returns the stored result and performs no additional writes. A stale entity version caused only by changes to unrelated fields is accepted without creating a field conflict.

### 7.5 Conflict policy

A stale mutation is not silently rejected and is not silently last-write-wins. It is applied, while `sync_conflicts` records:

- entity and field;
- stale actor's base value;
- server value observed at application time;
- applied stale value;
- both actor and mutation identifiers;
- creation time;
- review status and optional restoring mutation.

The conflict inbox lets a demo actor keep the applied value, restore the competing value through a new normal mutation, or dismiss the conflict after review. Every action is audited. Both browsers converge before conflict review; conflict review changes state only through another synchronized mutation.

## 8. Access Importer as the Migration Spine

The importer is a production-shaped, re-runnable CLI rather than disposable PoC code.

```text
extract  Access -> restricted neutral staging
profile  encoding, dates, relationships, and aggregate anomalies
load     neutral staging -> PostgreSQL using stable legacy keys
verify   source = loaded + merged + skipped + unresolved
```

### 8.1 Adapter boundary

The extractor emits a versioned neutral stream containing table name, source key, typed known fields, preserved raw representations, and source ordinal. The rest of the pipeline does not depend on Access APIs.

- The PoC adapter uses `mdbtools` for the copied database.
- A later Windows adapter may use ODBC on a controlled migration workstation.
- Both adapters must pass the same contract tests against the tracked blank schema database and fabricated rows.

This keeps the transformer, loader, and reconciler reusable during final cutover.

### 8.2 Source handling

The CLI accepts an explicit source database and, when required, a separate copied settings database. It:

- opens sources read-only;
- rejects paths outside an explicitly allowed local import directory;
- fingerprints schema structure without printing row values;
- stops on unexpected tables, missing required columns, or incompatible types;
- writes raw staging and manifests only below ignored restricted directories;
- never modifies the Access files.

The current copied runtime directory is not active, so repeated PoC imports do not race the Windows application. At production cutover, the live Windows application must be stopped before the final source copy is taken.

### 8.3 Transformation

Transformers are deterministic and table-specific:

- `GiaoHo` becomes parish sections.
- `VaiTro` becomes household relationship types.
- `GiaDinh` becomes households.
- `GiaoDan` becomes people.
- `ThanhVienGiaDinh` becomes household memberships.

The load order follows those dependencies. Legacy keys are preserved as opaque strings, unique per parish and source table. Character encoding, blank-versus-null semantics, partial dates, unknown enum values, and broken relationships are classified before normalization.

Ambiguous values are not guessed. They become explicit unresolved discrepancies with reason codes and source keys only. Human-readable source values stay inside restricted staging.

### 8.4 Idempotent load and reconciliation

Loads use `(parish_id, source_table, legacy_key)` identity and deterministic mapping. Re-running the same source:

- updates an unchanged target zero times;
- updates a changed source mapping predictably;
- creates no duplicate entity or relationship;
- produces the same reconciliation totals when the source is unchanged.

For each table and the complete run:

```text
source rows = loaded + merged + intentionally skipped + unresolved
```

The importer exits unsuccessfully when reconciliation does not balance. Console output contains aggregate counts, reason codes, phase timings, and a run identifier only. Restricted manifests contain source keys and statuses but no names, contact details, notes, or free text.

## 9. User Interface

The PoC has five focused surfaces:

- **People:** search and browse people with sync-state indicators.
- **Person detail/editor:** edit the approved field slice in demo mode; view-only in restricted-import mode.
- **Household detail:** browse household members and relationship types.
- **Sync status:** connection state, last successful pull, pending mutation count, retry state, and validation failures.
- **Conflict inbox:** compare conflict values and keep, restore, or dismiss.

A persistent header identifies the active data mode. Restricted-import mode uses unmistakable wording and disables actor switching. Demo mode offers Actor A and Actor B solely to make the audit and conflict scenario observable.

The design must remain usable at desktop and tablet widths. Detailed visual styling and component choices belong to the implementation plan, after this behavior specification is approved.

## 10. Error Handling

### 10.1 Synchronization

- Network and service failures preserve queued mutations and expose retry state.
- Validation failures move the mutation to a reviewable failed state and are not automatically retried.
- Authentication errors are not applicable to this PoC; an unexpected actor or parish context is treated as a configuration failure.
- Database transaction failure produces no partial entity, change-feed, conflict, idempotency, or audit write.
- Cursor corruption or an unknown contract version stops synchronization and requires a fresh fabricated demo reset; restricted imported data is never automatically deleted.

### 10.2 Import

- Missing extraction tools, unsafe source paths, unreadable files, schema drift, encoding ambiguity outside supported classifications, and reconciliation imbalance fail closed.
- Each phase records an ignored local manifest so interrupted runs can resume safely.
- A resumed or repeated load relies on stable legacy identities, not on last processed row position.
- Console error output uses table names, reason codes, aggregate counts, and a diagnostic run identifier. Source keys and source values appear only in the ignored restricted manifest addressed by that run identifier.

## 11. Testing Strategy

Implementation follows test-driven development.

### 11.1 Unit and contract tests

- Domain validation and partial-date behavior run in Node and browser-compatible environments.
- Sync merge, overlay reconstruction, conflict classification, and idempotent result handling use fabricated records.
- Import transformers consume fabricated neutral rows.
- Both extraction adapters must satisfy the same neutral-contract tests when the Windows adapter is added.

### 11.2 Integration tests

- SQL migrations and repositories run against a real local PostgreSQL instance.
- Tests cover row-level parish isolation, mutation transactions, ordered change feeds, stale mutation application, conflict creation, and replay idempotency.
- Import tests cover dependency order, stable legacy identities, repeated loads, discrepancies, and reconciliation failure.

### 11.3 Browser tests

Playwright uses two isolated browser contexts and synthetic data to prove:

- initial replica hydration;
- offline editing;
- persistence across reload;
- Actor B synchronizing a competing edit;
- Actor A reconnecting with a stale mutation;
- convergence in both contexts;
- conflict review;
- mutation replay without duplicate effects;
- restricted-import mutation rejection at both UI and API layers.

### 11.4 Real-data smoke test

The user runs the private real-data smoke test locally. Assertions inspect only schema compatibility, aggregate reconciliation, duplicate counts, unresolved reason counts, and read-only HTTP behavior. No automated snapshot, screenshot, fixture, trace, video, or failure artifact may contain real row values.

## 12. Acceptance Demonstration

The PoC is accepted when a clean checkout can perform this sequence:

1. Start PostgreSQL, API, and web application using documented commands.
2. Apply SQL migrations and load the fabricated demo seed.
3. Open two isolated browser contexts as Actor A and Actor B.
4. Put Actor A offline, edit a person, reload, and observe the same pending edit.
5. Edit the same field as Actor B and synchronize.
6. Reconnect Actor A and observe the stale mutation applied with a conflict.
7. Confirm both contexts converge to the same version and values.
8. Restore or dismiss the competing value through the conflict inbox and confirm the action is audited.
9. Replay an acknowledged mutation and confirm entity version, change count, conflict count, and audit count do not increase.
10. In restricted-import mode, import the copied database twice, obtain identical balanced reconciliation totals, browse imported people and households, and confirm all mutation endpoints reject writes.

The committed demonstration and its captured evidence use fabricated data. Step 10 is private local verification and is not recorded in repository artifacts.

## 13. Delivery Sequence

The implementation plan will decompose work in this order:

1. Workspace and dependency pinning.
2. Domain and contract foundations.
3. PostgreSQL schema, migrations, row-level security, and fabricated seed.
4. API browse and synchronization endpoints.
5. IndexedDB replica, mutation queue, and reconnect loop.
6. Demo people and household UI.
7. Conflict UI and two-context browser proof.
8. Neutral importer contracts and fabricated transformation tests.
9. `mdbtools` extraction, idempotent load, and reconciliation.
10. Restricted-import browse mode and privacy verification.
11. Clean-checkout documentation and final acceptance run.

Each step must have independently runnable tests and a focused commit. Before every commit, staged files must be checked for `.env` files, databases, restricted import artifacts, credentials, and unrelated generated output.

## 14. Evolution to Production Migration

The PoC importer is intended to become the final migration tool by extension, not by replacement. Later phases add remaining legacy tables, photo manifests, user/account mapping, report prerequisites, richer discrepancy review, and the Windows ODBC extractor while preserving:

- the neutral extraction contract;
- deterministic legacy-key mapping;
- read-only source handling;
- idempotent load behavior;
- balanced reconciliation;
- value-safe logging;
- staged rehearsal and final cutover commands.

This approach does not imply a live coexistence period. Rehearsals use database copies. The final migration uses a freshly copied source after the legacy application is stopped, followed by reconciliation and explicit go/no-go review.
