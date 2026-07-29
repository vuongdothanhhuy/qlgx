# Offline Sync and Access Importer PoC Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a production-shaped PoC under `WebApp/` that proves fabricated offline editing and conflict synchronization, plus a private, read-only, idempotent Access-to-PostgreSQL import path.

**Architecture:** A pnpm TypeScript monorepo lives entirely under `WebApp/`: Angular stores a base replica and durable mutation queue in IndexedDB, NestJS exposes browse and pull/push APIs, PostgreSQL owns authoritative data and RLS, and a separate CLI extracts Access rows into a neutral contract before deterministic loading and reconciliation. The sync and importer tracks share domain, contracts, database, privacy controls, and final acceptance evidence, so they remain one plan while retaining separate packages and review gates.

**Tech Stack:** Node.js 24.18.0 LTS, pnpm 11.10.0, TypeScript 6.0.3, Angular 22.0.8 with Angular CLI/build 22.0.9, NestJS 11.1.28, PostgreSQL 18.4, Drizzle ORM 0.45.2, Drizzle Kit 0.31.10, `pg` 8.22.0, Zod 4.4.3, `idb` 8.0.3, Vitest 4.1.10, Playwright 1.62.0, and `mdbtools` 1.0.1.

## Global Constraints

- Put every new web application source, test, generated artifact, local runtime file, and web-specific document under `WebApp/`.
- Do not modify legacy behavior or assets under `Source/`, `BIN/`, or `Release/`.
- Keep `giaoxu.mdb`, `QuanLyGiaoXu/`, every `.env` file, imported staging, Playwright traces from restricted mode, and all real parish values out of Git.
- Use the tracked blank schema database only at `Source/ChuongTrinh/Resources/giaoxu.mdb`; never replace it or add another tracked database.
- Use fabricated data for every committed seed, fixture, assertion, screenshot, trace, video, and demonstration.
- Keep the optional aggregate-driven private synthetic generator outside this PoC; the deterministic fabricated seed is sufficient for committed tests and demos.
- `demo` mode permits the approved person edit slice; `restricted-import` mode rejects every domain mutation at the API boundary.
- Preserve partial dates as year, optional month, optional day, precision, and raw legacy text; never invent a complete date.
- Every parish-owned table carries `parish_id`; PostgreSQL RLS and two-parish integration tests are mandatory.
- Every accepted sync mutation atomically writes the entity version, change feed, optional field conflicts, audit event, and idempotency result.
- Access extraction is read-only, neutral-contract based, resumable, deterministic, idempotent, and reconciled by `source = loaded + merged + skipped + unresolved`.
- Runtime package versions are exact in `package.json`; `pnpm-lock.yaml` is committed.
- Use test-driven development: observe the focused test fail, implement the smallest behavior, observe it pass, then run the task-level regression command.
- Each task ends with a focused commit whose subject describes the behavior without an AI attribution or conventional-commit prefix.
- At each task's commit step, stage only the exact paths in that task's **Files** list, inspect `git diff --cached --name-status`, then run `pnpm security:staged`, `git diff --cached --check`, and the task's verification commands.

## Resume Protocol

The plan is intentionally safe across token limits, context compaction, or a new Codex session:

1. At implementation start, create an isolated worktree using `superpowers:using-git-worktrees`.
2. Read this plan, `WebApp/docs/implementation-progress.md`, `git status --short`, and the last five commits before editing.
3. Treat this plan's ordered checkboxes as the command sequence and `implementation-progress.md` as the durable completion state; do not rely on conversational memory or edit this committed plan merely to tick boxes.
4. Keep red/green/refactor steps between two and five minutes. At a forced stop, finish only the current safe command, then record the exact task, step, working-tree state, last command/result, and next command in `implementation-progress.md`.
5. Do not commit a known failing state. Each completed task commit updates `implementation-progress.md` with the completed task number, green commands, and next task.
6. A resumed agent reruns the recorded last green command before continuing. If the worktree differs from the progress record, inspect and preserve the worktree; never reset it blindly.
7. After each commit, verify `git status --short` is empty and record the new short SHA in the progress file at the beginning of the next task.

Initial progress-file content:

```markdown
# Implementation Progress

- Last completed task: 0
- Last verified commit: planning baseline, replaced in Task 1 with `git rev-parse --short HEAD`
- Current task and step: Task 1, Step 1
- Last green commands: documentation checks only
- Working-tree expectation: clean
- Next command: create the isolated implementation worktree
- Blockers: none
```

---

## File and Responsibility Map

```text
WebApp/
  package.json                         workspace scripts and exact tool pins
  pnpm-workspace.yaml                 workspace package discovery
  pnpm-lock.yaml                      reproducible dependency graph
  .node-version                       Node 24.18.0
  .npmrc                              exact-save and workspace behavior
  .gitignore                          web-only generated and restricted data
  tsconfig.base.json                  strict shared TypeScript options
  eslint.config.mjs                   shared lint rules
  prettier.config.mjs                 deterministic formatting
  vitest.config.ts                    Vitest 4 package test projects
  README.md                           clean-checkout and private-smoke commands
  docs/
    implementation-progress.md        resumable execution checkpoint
    privacy-runbook.md                real-data handling and artifact rules
  scripts/
    verify-workspace.mjs              runtime and workspace preflight
    check-sensitive-staging.mjs       commit-time privacy guard
  apps/
    api/
      src/config/                      validated process configuration
      src/browse/                      read-only people and household HTTP API
      src/sync/                        pull/push HTTP API
      src/main.ts                      NestJS bootstrap
    web/
      src/app/core/                    data mode, API transport, IndexedDB, sync
      src/app/people/                  people list, detail, and demo editor
      src/app/households/              read-only household detail
      src/app/sync/                    status and conflict inbox
      e2e/                             fabricated Playwright scenarios
  packages/
    domain/src/                        framework-free domain transitions
    contracts/src/                     Zod wire and neutral-import contracts
    database/
      migrations/                     reviewed SQL migrations
      src/client/                      pool and transaction context
      src/repositories/                browse, sync, seed, and import persistence
    importer/
      src/extract/                     mdbtools adapter and safe staging
      src/profile/                     aggregate classifications
      src/transform/                   legacy-table transforms
      src/load/                        idempotent PostgreSQL load
      src/reconcile/                   balanced run summaries
      src/cli.ts                       extract/profile/load/verify command
    testing/src/                       fabricated builders and DB helpers
  infra/compose/compose.yaml           PostgreSQL 18.4 local services
```

## Task 1: Create the Isolated `WebApp/` Workspace and Resume Guardrails

**Files:**
- Create: `WebApp/package.json`
- Create: `WebApp/pnpm-workspace.yaml`
- Create: `WebApp/.node-version`
- Create: `WebApp/.npmrc`
- Create: `WebApp/.gitignore`
- Create: `WebApp/tsconfig.base.json`
- Create: `WebApp/eslint.config.mjs`
- Create: `WebApp/prettier.config.mjs`
- Create: `WebApp/vitest.config.ts`
- Create: `WebApp/apps/.gitkeep`
- Create: `WebApp/packages/.gitkeep`
- Create: `WebApp/infra/.gitkeep`
- Create: `WebApp/scripts/verify-workspace.mjs`
- Create: `WebApp/scripts/check-sensitive-staging.mjs`
- Create: `WebApp/docs/implementation-progress.md`
- Create: `WebApp/README.md`

**Interfaces:**
- Consumes: design commit `049bdfc` and the repository `.gitignore`.
- Produces: `pnpm verify:workspace`, `pnpm security:staged`, strict TypeScript defaults, and the durable resume record used by all later tasks.

- [ ] **Step 1: Create the worktree and record its location**

Invoke `superpowers:using-git-worktrees`, create a branch named `poc/offline-sync-importer`, then verify:

```bash
git branch --show-current
git status --short
```

Expected: branch `poc/offline-sync-importer`; no changed files before Task 1 edits.

- [ ] **Step 2: Write the workspace preflight first**

Create `WebApp/scripts/verify-workspace.mjs` so it exits non-zero unless:

```js
const required = {
  node: '24.18.0',
  pnpm: '11.10.0',
  directories: ['apps', 'packages', 'infra', 'docs', 'scripts'],
};
```

It must compare `process.versions.node`, run `pnpm --version` with `execFileSync`, and check each directory relative to `WebApp/`, without reading any database.

- [ ] **Step 3: Run the preflight and observe the expected failure**

Run:

```bash
node WebApp/scripts/verify-workspace.mjs
```

Expected: FAIL because the local Node version is 24.13.0 and the workspace directories do not exist.

- [ ] **Step 4: Add the exact workspace manifests and directory skeleton**

Use this root package contract:

```json
{
  "name": "qlgx-webapp",
  "private": true,
  "packageManager": "pnpm@11.10.0",
  "engines": {
    "node": "24.18.0",
    "pnpm": "11.10.0"
  },
  "scripts": {
    "verify:workspace": "node scripts/verify-workspace.mjs",
    "security:staged": "node scripts/check-sensitive-staging.mjs",
    "build": "pnpm -r --if-present build",
    "lint": "pnpm -r --if-present lint",
    "test": "pnpm -r --if-present test",
    "test:integration": "pnpm -r --if-present test:integration",
    "test:e2e": "pnpm --filter @qlgx/web test:e2e"
  },
  "devDependencies": {
    "@eslint/js": "10.0.1",
    "@types/node": "24.13.3",
    "eslint": "10.8.0",
    "prettier": "3.9.6",
    "tsx": "4.23.1",
    "typescript": "6.0.3",
    "typescript-eslint": "8.65.0",
    "vitest": "4.1.10"
  }
}
```

`pnpm-workspace.yaml` includes `apps/*` and `packages/*`. `vitest.config.ts` uses Vitest 4's `test.projects` with `apps/*` and `packages/*`; the deprecated workspace configuration is not used. `.gitignore` must include:

```gitignore
node_modules/
dist/
coverage/
.angular/
test-results/
playwright-report/
blob-report/
.restricted/
.env
.env.*
```

No environment-file exception is permitted.

- [ ] **Step 5: Add the staged-data security guard**

`check-sensitive-staging.mjs` runs `git diff --cached --name-only -z`, then rejects:

```js
const forbidden = [
  /(^|\/)\.env($|\.)/i,
  /\.(mdb|accdb)$/i,
  /(^|\/)QuanLyGiaoXu(\/|$)/i,
  /(^|\/)\.restricted(\/|$)/i,
  /(^|\/)(playwright-report|test-results|blob-report)(\/|$)/i,
];
```

It also scans staged added lines for private-key markers and credential assignments while allowing words such as “password” in documentation prose.

- [ ] **Step 6: Install the required Node runtime and dependencies**

Install the Homebrew `node@24` formula after normal tool approval, prepend its binary directory for the session, and stop if it does not resolve to exactly 24.18.0:

```bash
brew install node@24
export PATH="/opt/homebrew/opt/node@24/bin:$PATH"
node --version
cd WebApp
corepack enable
corepack prepare pnpm@11.10.0 --activate
pnpm install --frozen-lockfile=false
pnpm verify:workspace
```

Expected: `verify:workspace` reports Node 24.18.0, pnpm 11.10.0, and all required directories.

- [ ] **Step 7: Verify, checkpoint, and commit**

Update `WebApp/docs/implementation-progress.md` to Task 1 complete and Task 2 next. Run:

```bash
cd WebApp
git add -- package.json pnpm-workspace.yaml pnpm-lock.yaml .node-version .npmrc .gitignore tsconfig.base.json eslint.config.mjs prettier.config.mjs vitest.config.ts apps/.gitkeep packages/.gitkeep infra/.gitkeep scripts docs README.md
git diff --cached --name-status
pnpm security:staged
pnpm verify:workspace
git diff --cached --check
git status --short
```

Commit only Task 1 files:

```bash
git commit -m "Create resumable web app workspace"
```

## Task 2: Add Framework-Free Partial-Date and Person Domain Rules

**Files:**
- Create: `WebApp/packages/domain/package.json`
- Create: `WebApp/packages/domain/tsconfig.json`
- Create: `WebApp/packages/domain/src/partial-date.ts`
- Create: `WebApp/packages/domain/src/person.ts`
- Create: `WebApp/packages/domain/src/identifiers.ts`
- Create: `WebApp/packages/domain/src/index.ts`
- Test: `WebApp/packages/domain/src/partial-date.spec.ts`
- Test: `WebApp/packages/domain/src/person.spec.ts`
- Modify: `WebApp/docs/implementation-progress.md`

**Interfaces:**
- Consumes: TypeScript 6.0.3 and Vitest workspace configuration.
- Produces: `PartialDate`, `parseLegacyPartialDate`, `PersonEditable`, `PersonPatch`, `validatePersonPatch`, `applyPersonPatch`, branded IDs, and `newUuidV7()`.

- [ ] **Step 1: Write failing partial-date tests**

Cover exact valid and invalid behavior:

```ts
expect(parseLegacyPartialDate('1980')).toEqual({
  year: 1980, month: null, day: null, precision: 'year', raw: '1980',
});
expect(parseLegacyPartialDate('03/1980')).toEqual({
  year: 1980, month: 3, day: null, precision: 'month', raw: '03/1980',
});
expect(parseLegacyPartialDate('31/02/1980')).toEqual({
  year: null, month: null, day: null, precision: 'unknown', raw: '31/02/1980',
});
```

- [ ] **Step 2: Run the focused test and verify red**

Run:

```bash
cd WebApp
pnpm --filter @qlgx/domain test -- partial-date.spec.ts
```

Expected: FAIL because the domain implementation does not exist.

- [ ] **Step 3: Implement partial dates without sentinel values**

Define:

```ts
export type DatePrecision = 'unknown' | 'year' | 'month' | 'day';
export interface PartialDate {
  year: number | null;
  month: number | null;
  day: number | null;
  precision: DatePrecision;
  raw: string | null;
}
```

Accept `YYYY`, `MM/YYYY`, and `DD/MM/YYYY`; validate calendar days; preserve trimmed raw input; return `unknown` rather than guessing.

- [ ] **Step 4: Write failing person-transition tests**

Prove that blank names, invalid sex/status values, impossible dates, and a patch with no changed fields are rejected. Prove a valid patch returns a new value without mutating the original:

```ts
const patch: PersonPatch = {
  displayName: { before: 'An Bình', after: 'An Minh' },
};
expect(applyPersonPatch(person, patch).displayName).toBe('An Minh');
expect(person.displayName).toBe('An Bình');
```

All names are fabricated.

- [ ] **Step 5: Implement person rules and UUIDv7 generation**

Use exact unions:

```ts
export type PersonSex = 'female' | 'male' | 'unknown';
export type PersonStatus = 'active' | 'inactive' | 'deceased' | 'unknown';
export type ChangedField<T> = { before: T; after: T };
export type PersonPatch = Partial<{
  displayName: ChangedField<string>;
  saintName: ChangedField<string | null>;
  sex: ChangedField<PersonSex>;
  birthDate: ChangedField<PartialDate>;
  parishSectionId: ChangedField<string | null>;
  status: ChangedField<PersonStatus>;
}>;
```

`newUuidV7()` wraps `uuidv7()` from package version 1.2.1 and validates the returned UUID version nibble.

- [ ] **Step 6: Run domain regression and commit**

Run:

```bash
cd WebApp
pnpm --filter @qlgx/domain test
pnpm --filter @qlgx/domain build
pnpm security:staged
git diff --cached --check
```

Update progress and commit:

```bash
git commit -m "Add parish person domain rules"
```

## Task 3: Define Versioned Browse, Sync, and Neutral-Import Contracts

**Files:**
- Create: `WebApp/packages/contracts/package.json`
- Create: `WebApp/packages/contracts/tsconfig.json`
- Create: `WebApp/packages/contracts/src/common.ts`
- Create: `WebApp/packages/contracts/src/browse.ts`
- Create: `WebApp/packages/contracts/src/sync.ts`
- Create: `WebApp/packages/contracts/src/import.ts`
- Create: `WebApp/packages/contracts/src/index.ts`
- Test: `WebApp/packages/contracts/src/contracts.spec.ts`
- Modify: `WebApp/docs/implementation-progress.md`

**Interfaces:**
- Consumes: domain enums, partial dates, and branded ID string shapes.
- Produces: Zod schemas and inferred types for browse responses, `PullRequest`, `PullResponse`, `PushRequest`, `PushOutcome`, `NeutralRow`, `ImportDisposition`, and `ReconciliationSummary`.

- [ ] **Step 1: Write contract rejection tests**

Verify contracts reject unknown versions, negative cursors, mutation patches without before values, unknown tables, and unbalanced reconciliation:

```ts
expect(() => PushRequestSchema.parse({
  contractVersion: 1,
  parishId,
  actorId,
  mutations: [{ mutationId, entityType: 'person', entityId, baseVersion: 3,
    patch: { displayName: { after: 'Minh' } } }],
})).toThrow();
```

- [ ] **Step 2: Run the test and verify red**

Run:

```bash
cd WebApp
pnpm --filter @qlgx/contracts test
```

Expected: FAIL because the schemas do not exist.

- [ ] **Step 3: Implement exact sync contracts**

Use an opaque decimal-string cursor and per-mutation outcomes:

```ts
export const PullRequestSchema = z.object({
  contractVersion: z.literal(1),
  parishId: UuidSchema,
  cursor: z.string().regex(/^(0|[1-9]\d*)$/),
  limit: z.number().int().min(1).max(500).default(200),
});

export const PushOutcomeSchema = z.discriminatedUnion('status', [
  z.object({ mutationId: UuidSchema, status: z.literal('accepted'),
    entityVersion: z.number().int().positive(), conflictIds: z.array(UuidSchema) }),
  z.object({ mutationId: UuidSchema, status: z.literal('rejected'),
    code: z.enum(['VALIDATION', 'MODE_READ_ONLY', 'NOT_FOUND', 'PARISH_MISMATCH']),
    message: z.string().max(200) }),
]);
```

`PullResponse` contains ordered entity changes, conflict changes, `nextCursor`, and `hasMore`.

`PushRequest.mutations` is a discriminated union:

```ts
export const SyncMutationSchema = z.discriminatedUnion('entityType', [
  z.object({
    entityType: z.literal('person'),
    mutationId: UuidSchema,
    entityId: UuidSchema,
    baseVersion: z.number().int().positive(),
    createdAt: z.string().datetime(),
    patch: PersonPatchSchema,
  }),
  z.object({
    entityType: z.literal('conflict'),
    mutationId: UuidSchema,
    entityId: UuidSchema,
    baseVersion: z.number().int().positive(),
    createdAt: z.string().datetime(),
    action: z.enum(['keep', 'restore', 'dismiss']),
  }),
]);
```

- [ ] **Step 4: Implement exact neutral-import contracts**

Allow only the five PoC source tables:

```ts
export const LegacyTableSchema = z.enum([
  'GiaoHo', 'VaiTro', 'GiaDinh', 'GiaoDan', 'ThanhVienGiaDinh',
]);
export const NeutralRowSchema = z.object({
  contractVersion: z.literal(1),
  table: LegacyTableSchema,
  legacyKey: z.string().min(1).max(200),
  sourceOrdinal: z.number().int().nonnegative(),
  fields: z.record(z.string(), z.union([z.string(), z.number(), z.boolean(), z.null()])),
});
```

Reconciliation parsing must enforce `source === loaded + merged + skipped + unresolved`.

- [ ] **Step 5: Run, checkpoint, and commit**

Run:

```bash
cd WebApp
pnpm --filter @qlgx/contracts test
pnpm --filter @qlgx/contracts build
pnpm security:staged
git diff --cached --check
```

Commit:

```bash
git commit -m "Define versioned web app contracts"
```

## Task 4: Provision PostgreSQL and a Reviewed Migration Runner

**Files:**
- Create: `WebApp/infra/compose/compose.yaml`
- Create: `WebApp/packages/database/package.json`
- Create: `WebApp/packages/database/tsconfig.json`
- Create: `WebApp/packages/database/drizzle.config.ts`
- Create: `WebApp/packages/database/src/client/config.ts`
- Create: `WebApp/packages/database/src/client/pool.ts`
- Create: `WebApp/packages/database/src/client/migrate.ts`
- Create: `WebApp/packages/database/src/index.ts`
- Test: `WebApp/packages/database/src/client/config.spec.ts`
- Test: `WebApp/packages/database/src/client/migrate.integration.spec.ts`
- Modify: `WebApp/docs/implementation-progress.md`

**Interfaces:**
- Consumes: PostgreSQL 18.4 Docker image, `pg` 8.22.0, Drizzle ORM 0.45.2.
- Produces: `DatabaseConfig`, `createPool(config)`, `runMigrations(pool)`, and deterministic local/test PostgreSQL services.

- [ ] **Step 1: Write configuration tests**

Test explicit configuration rather than implicit secrets:

```ts
expect(parseDatabaseConfig({
  QLGX_DATABASE_HOST: '127.0.0.1',
  QLGX_DATABASE_PORT: '55432',
  QLGX_DATABASE_NAME: 'qlgx_demo',
  QLGX_DATABASE_USER: 'qlgx_app',
  QLGX_DATABASE_PASSWORD_FILE: '/tmp/qlgx-test-password',
})).toMatchObject({ port: 55432, database: 'qlgx_demo' });
```

The parser must reject an inline password variable. Tests create a temporary password file and never print its contents.

- [ ] **Step 2: Run the focused test and verify red**

Run:

```bash
cd WebApp
pnpm --filter @qlgx/database test -- config.spec.ts
```

Expected: FAIL because `parseDatabaseConfig` does not exist.

- [ ] **Step 3: Add PostgreSQL services and password-file handling**

Pin `postgres:18.4-bookworm`. Define `postgres-demo` on host port `55432` and `postgres-test` on `55433`, each with a health check using `pg_isready`. Keep password files below `WebApp/.restricted/runtime/`; never use a committed environment file.

- [ ] **Step 4: Implement pool and migration runner**

`createPool` reads the password file at process start, creates a bounded pool of five connections, sets `application_name = 'qlgx-webapp'`, and registers an error listener that logs only SQLSTATE and operation labels.

`runMigrations` acquires an advisory lock, applies ordered `.sql` files once, records SHA-256 in `schema_migrations`, and refuses a changed checksum.

- [ ] **Step 5: Prove migration idempotency against PostgreSQL**

Start only the test service:

```bash
cd WebApp
docker compose -f infra/compose/compose.yaml up -d postgres-test
pnpm --filter @qlgx/database test:integration -- migrate.integration.spec.ts
```

Expected: first run records migrations; second run applies zero; a checksum mismatch fixture fails closed.

- [ ] **Step 6: Run, checkpoint, and commit**

Run database unit/integration tests, `pnpm security:staged`, and `git diff --cached --check`. Commit:

```bash
git commit -m "Add PostgreSQL migration foundation"
```

## Task 5: Create the Core Schema, RLS Policies, and Fabricated Seed

**Files:**
- Create: `WebApp/packages/database/migrations/0001_core.sql`
- Create: `WebApp/packages/database/migrations/0002_rls.sql`
- Create: `WebApp/packages/database/src/schema/core.ts`
- Create: `WebApp/packages/database/src/client/parish-context.ts`
- Create: `WebApp/packages/database/src/repositories/seed-repository.ts`
- Create: `WebApp/packages/testing/package.json`
- Create: `WebApp/packages/testing/src/fabricated-seed.ts`
- Create: `WebApp/packages/testing/src/database.ts`
- Test: `WebApp/packages/database/src/schema/core.integration.spec.ts`
- Test: `WebApp/packages/database/src/client/parish-context.integration.spec.ts`
- Modify: `WebApp/docs/implementation-progress.md`

**Interfaces:**
- Consumes: migration runner and domain field types.
- Produces: all core tables, `withParishTransaction(pool, context, work)`, `seedFabricatedParish`, and two-parish test builders.

- [ ] **Step 1: Write failing schema and RLS integration tests**

Tests assert these tables exist:

```ts
const expected = [
  'parishes', 'parish_sections', 'users', 'people', 'households',
  'household_memberships', 'household_relationship_types',
  'sync_changes', 'sync_mutation_results', 'sync_conflicts', 'audit_events',
];
```

Create Parish A and Parish B, set Parish A context, and prove direct selects and updates cannot observe Parish B.

- [ ] **Step 2: Run tests and verify red**

Run:

```bash
cd WebApp
pnpm --filter @qlgx/database test:integration -- core.integration.spec.ts parish-context.integration.spec.ts
```

Expected: FAIL because the tables and policies do not exist.

- [ ] **Step 3: Write `0001_core.sql`**

Use PostgreSQL 18 `uuidv7()` defaults. All parish-owned records include `parish_id`, `version`, timestamps, actor references, tombstone, and source identity. Required uniqueness includes:

```sql
UNIQUE (parish_id, legacy_source_table, legacy_key)
```

for imported entities, and:

```sql
UNIQUE (parish_id, mutation_id)
```

for idempotency results. `sync_changes.sequence` is `bigint GENERATED ALWAYS AS IDENTITY`; pull cursors use this sequence, never timestamps.

- [ ] **Step 4: Write `0002_rls.sql` and transaction context**

Policies compare:

```sql
parish_id = current_setting('app.parish_id', true)::uuid
```

Force RLS on parish-owned tables. `withParishTransaction` begins a transaction, sets `SET LOCAL app.parish_id`, `SET LOCAL app.actor_id`, and `SET LOCAL app.data_mode`, invokes the callback, and commits or rolls back.

Use one context shape at every repository boundary:

```ts
export type ParishContext = {
  parishId: string;
  actorId: string;
  dataMode: 'demo' | 'restricted-import';
};
```

- [ ] **Step 5: Add deterministic fabricated seed**

Seed two parishes but return the Phu Long demo IDs. Use fictional names and stable UUIDs. Include:

- 8 people with all partial-date precisions;
- 3 households;
- 10 memberships;
- 2 parish sections;
- 4 relationship types;
- Actor A, Actor B, and importer system actor.

No seed value may be copied or transformed from the real database.

- [ ] **Step 6: Run, checkpoint, and commit**

Run:

```bash
cd WebApp
pnpm --filter @qlgx/database test
pnpm --filter @qlgx/database test:integration
pnpm --filter @qlgx/testing test
pnpm security:staged
git diff --cached --check
```

Commit:

```bash
git commit -m "Add tenant-isolated parish schema"
```

## Task 6: Add Read-Only People and Household Repositories

**Files:**
- Create: `WebApp/packages/database/src/repositories/people-repository.ts`
- Create: `WebApp/packages/database/src/repositories/households-repository.ts`
- Create: `WebApp/packages/database/src/repositories/repository-types.ts`
- Test: `WebApp/packages/database/src/repositories/browse.integration.spec.ts`
- Modify: `WebApp/docs/implementation-progress.md`

**Interfaces:**
- Consumes: `withParishTransaction`, core schema, browse contracts, fabricated seed.
- Produces: `searchPeople(context, query, page)`, `getPerson(context, id)`, and `getHousehold(context, id)`.

- [ ] **Step 1: Write failing browse tests**

Prove normalized case-insensitive search, stable `(display_name, id)` ordering, keyset pagination, household member ordering, missing-record behavior, tombstone exclusion, and Parish B invisibility.

- [ ] **Step 2: Run and verify red**

```bash
cd WebApp
pnpm --filter @qlgx/database test:integration -- browse.integration.spec.ts
```

Expected: FAIL because browse repositories do not exist.

- [ ] **Step 3: Implement people search**

`searchPeople` accepts:

```ts
type PeopleQuery = {
  search: string;
  sectionId: string | null;
  status: PersonStatus | null;
  after: { displayName: string; id: string } | null;
  limit: number;
};
```

Clamp the limit to 50, escape wildcard input, return only contract fields, and never log the search term in restricted mode.

- [ ] **Step 4: Implement household detail**

Return household metadata plus members joined to relationship type. Preserve missing optional values as `null`. Sort head/spouse/child/other rank, then display name and ID.

- [ ] **Step 5: Run, checkpoint, and commit**

Run database unit and integration suites plus staged security checks. Commit:

```bash
git commit -m "Add parish browse repositories"
```

## Task 7: Implement Atomic Sync Mutation, Idempotency, and Conflicts

**Files:**
- Create: `WebApp/packages/database/src/repositories/sync-repository.ts`
- Create: `WebApp/packages/database/src/repositories/conflict-repository.ts`
- Create: `WebApp/packages/database/src/repositories/audit-repository.ts`
- Create: `WebApp/packages/database/src/services/apply-mutation.ts`
- Create: `WebApp/packages/database/src/services/pull-changes.ts`
- Test: `WebApp/packages/database/src/services/apply-mutation.integration.spec.ts`
- Test: `WebApp/packages/database/src/services/pull-changes.integration.spec.ts`
- Modify: `WebApp/docs/implementation-progress.md`

**Interfaces:**
- Consumes: `PushRequest`, person domain transition, transaction context, sync tables.
- Produces: `applyMutation(context, mutation): Promise<PushOutcome>` and `pullChanges(context, cursor, limit): Promise<PullResponse>`.

- [ ] **Step 1: Write failing transaction tests**

Cover:

1. matching `baseVersion` increments once and writes one change/audit/result;
2. same mutation replay returns the stored outcome and changes no counts;
3. stale same-field mutation applies and creates one conflict;
4. stale unrelated-field mutation creates no conflict;
5. invalid patch writes nothing;
6. forced audit insert failure rolls back the entity update;
7. cross-parish entity ID returns `PARISH_MISMATCH`.

- [ ] **Step 2: Run and verify red**

```bash
cd WebApp
pnpm --filter @qlgx/database test:integration -- apply-mutation.integration.spec.ts
```

Expected: FAIL because `applyMutation` does not exist.

- [ ] **Step 3: Implement the atomic mutation path**

Within one parish transaction:

```ts
const replay = await findMutationResult(mutation.mutationId);
if (replay) return replay.outcome;
const current = await lockPersonForUpdate(mutation.entityId);
const conflicts = changedFields.filter(
  (field) => !deepEqual(current[field], mutation.patch[field].before),
);
const next = applyPersonPatch(current, mutation.patch);
```

Update the person, insert conflict rows for those fields, append one `sync_changes` row and one `audit_events` row, then insert the serialized result. Store JSON values without logging them.

- [ ] **Step 4: Implement ordered pull**

Select `sync_changes.sequence > cursor`, order ascending, limit `limit + 1`, return at most `limit`, and set `hasMore`. Include current conflict records referenced by returned changes. Cursor `0` hydrates the fabricated base replica.

- [ ] **Step 5: Add conflict-review mutations**

Support `keep`, `restore`, and `dismiss`:

- `keep` and `dismiss` change conflict review state only and write audit/change entries;
- `restore` creates a new normal person mutation using the current value as `before`;
- replay remains idempotent.

- [ ] **Step 6: Run, checkpoint, and commit**

Run all database suites, security checks, and diff checks. Commit:

```bash
git commit -m "Add atomic offline synchronization"
```

## Task 8: Bootstrap NestJS with Validated Data Modes

**Files:**
- Create: `WebApp/apps/api/package.json`
- Create: `WebApp/apps/api/tsconfig.json`
- Create: `WebApp/apps/api/src/config/app-config.ts`
- Create: `WebApp/apps/api/src/config/data-mode.guard.ts`
- Create: `WebApp/apps/api/src/health/health.controller.ts`
- Create: `WebApp/apps/api/src/app.module.ts`
- Create: `WebApp/apps/api/src/main.ts`
- Test: `WebApp/apps/api/src/config/app-config.spec.ts`
- Test: `WebApp/apps/api/src/health/health.e2e-spec.ts`
- Modify: `WebApp/docs/implementation-progress.md`

**Interfaces:**
- Consumes: database configuration, NestJS 11.1.28, Zod 4.4.3.
- Produces: validated `AppConfig`, `DataModeGuard`, `/api/v1/health`, and API bootstrap.

- [ ] **Step 1: Write failing configuration tests**

Require:

```ts
type DataMode = 'demo' | 'restricted-import';
type AppConfig = {
  dataMode: DataMode;
  parishId: string;
  port: number;
  database: DatabaseConfig;
};
```

Reject missing parish ID, unknown mode, inline DB password, wildcard CORS origin, and ports outside 1024–65535.

- [ ] **Step 2: Run and verify red**

```bash
cd WebApp
pnpm --filter @qlgx/api test -- app-config.spec.ts
```

Expected: FAIL because API configuration does not exist.

- [ ] **Step 3: Implement API bootstrap**

Use URI versioning `/api/v1`, JSON body limit `256kb`, a single configured local web origin, graceful shutdown, and structured logs containing request ID, route template, status, duration, mode, and error code only. Do not log query values or bodies.

- [ ] **Step 4: Implement read-only mode guard**

`DataModeGuard.assertMutationAllowed()` throws HTTP 403 with:

```json
{
  "code": "MODE_READ_ONLY",
  "message": "Restricted imported data is read-only."
}
```

Call the guard from every mutation service, not only controllers.

- [ ] **Step 5: Run, checkpoint, and commit**

Run API unit/e2e tests, build, lint, and security checks. Commit:

```bash
git commit -m "Bootstrap mode-aware parish API"
```

## Task 9: Expose Browse and Pull/Push HTTP Endpoints

**Files:**
- Create: `WebApp/apps/api/src/common/zod-validation.pipe.ts`
- Create: `WebApp/apps/api/src/browse/browse.controller.ts`
- Create: `WebApp/apps/api/src/browse/browse.service.ts`
- Create: `WebApp/apps/api/src/browse/browse.module.ts`
- Create: `WebApp/apps/api/src/sync/sync.controller.ts`
- Create: `WebApp/apps/api/src/sync/sync.service.ts`
- Create: `WebApp/apps/api/src/sync/sync.module.ts`
- Test: `WebApp/apps/api/src/browse/browse.e2e-spec.ts`
- Test: `WebApp/apps/api/src/sync/sync.e2e-spec.ts`
- Modify: `WebApp/apps/api/src/app.module.ts`
- Modify: `WebApp/docs/implementation-progress.md`

**Interfaces:**
- Consumes: database browse/sync services and Zod contracts.
- Produces: `GET /api/v1/people`, `GET /api/v1/people/:id`, `GET /api/v1/households/:id`, `POST /api/v1/sync/pull`, and `POST /api/v1/sync/push`.

- [ ] **Step 1: Write failing browse endpoint tests**

Test fabricated search, pagination, person detail, household detail, 404, malformed UUID, and cross-parish invisibility. In restricted mode, capture logs and prove a search string is absent.

- [ ] **Step 2: Write failing sync endpoint tests**

Test initial pull, accepted push, replay, stale conflict, malformed contract, actor mismatch, and restricted-import 403. The restricted test must assert entity version and all sync table counts remain unchanged.

- [ ] **Step 3: Run and verify red**

```bash
cd WebApp
pnpm --filter @qlgx/api test:e2e -- browse.e2e-spec.ts sync.e2e-spec.ts
```

Expected: FAIL because the endpoints do not exist.

- [ ] **Step 4: Implement Zod validation and browse endpoints**

Map validation issues to stable `{ code: 'VALIDATION', issues: [{ path, message }] }` responses. The controller receives parish from validated process configuration and, in demo mode only, actor from the `x-demo-actor-id` header. It never accepts parish context from a query parameter; restricted mode always uses the configured importer-system actor.

- [ ] **Step 5: Implement pull/push endpoints**

Push accepts at most 100 mutations, processes them sequentially in request order, and returns one outcome per mutation. A validation failure for one well-formed mutation returns a rejected outcome; an invalid batch envelope returns HTTP 400.

- [ ] **Step 6: Run, checkpoint, and commit**

Run API and database suites, builds, lint, security scan, and diff check. Commit:

```bash
git commit -m "Expose parish browse and sync APIs"
```

## Task 10: Scaffold the Angular Shell and Read-Only Browse UI

**Files:**
- Create: `WebApp/apps/web/package.json`
- Create: `WebApp/apps/web/angular.json`
- Create: `WebApp/apps/web/tsconfig*.json`
- Create: `WebApp/apps/web/src/index.html`
- Create: `WebApp/apps/web/src/styles.css`
- Create: `WebApp/apps/web/src/main.ts`
- Create: `WebApp/apps/web/src/app/app.config.ts`
- Create: `WebApp/apps/web/src/app/app.routes.ts`
- Create: `WebApp/apps/web/src/app/app.ts`
- Create: `WebApp/apps/web/src/app/core/data-mode.service.ts`
- Create: `WebApp/apps/web/src/app/core/api-client.ts`
- Create: `WebApp/apps/web/src/app/people/people-list.ts`
- Create: `WebApp/apps/web/src/app/people/person-detail.ts`
- Create: `WebApp/apps/web/src/app/households/household-detail.ts`
- Test: matching `*.spec.ts` files
- Modify: `WebApp/docs/implementation-progress.md`

**Interfaces:**
- Consumes: browse API and contracts.
- Produces: desktop/tablet application shell, persistent data-mode banner, people search/detail, and household detail routes.

- [ ] **Step 1: Invoke the frontend-design skill**

Before UI code, invoke `frontend-design:frontend-design` and constrain it to the approved surfaces, fabricated data, desktop/tablet widths, Vietnamese parish-office clarity, and accessibility. Record chosen typography, spacing, color tokens, and interaction states in `WebApp/apps/web/src/styles.css`.

- [ ] **Step 2: Write failing shell and banner tests**

Prove the header renders the parish label and exactly one of:

```ts
'Dữ liệu minh họa'
'Dữ liệu nhập riêng tư — chỉ đọc'
```

In restricted mode, prove no actor switcher or edit button is in the DOM.

- [ ] **Step 3: Run and verify red**

```bash
cd WebApp
pnpm --filter @qlgx/web test -- app.spec.ts data-mode.service.spec.ts
```

Expected: FAIL because the Angular application does not exist.

- [ ] **Step 4: Scaffold Angular with exact compatible pins**

Pin Angular packages to 22.0.8, CLI/build to 22.0.9, TypeScript 6.0.3, RxJS 7.8.2, Zone.js 0.16.2, and use standalone components with signals and native control flow.

- [ ] **Step 5: Add browse components test-first**

People search uses a debounced signal, preserves query in the URL, renders loading/empty/error states, and never stores the query in telemetry. Detail pages distinguish partial-date precision and link household membership.

- [ ] **Step 6: Verify responsive and accessible rendering**

Run component tests and use Playwright at 1440×900 and 1024×768. Check keyboard navigation, visible focus, landmarks, labels, WCAG AA contrast, browser console errors, and horizontal overflow.

- [ ] **Step 7: Run, checkpoint, and commit**

Run Angular test/build/lint plus global security checks. Commit:

```bash
git commit -m "Add parish browse web interface"
```

## Task 11: Build the IndexedDB Base Replica, Queue, and Overlay

**Files:**
- Create: `WebApp/apps/web/src/app/core/storage/database.ts`
- Create: `WebApp/apps/web/src/app/core/storage/replica-store.ts`
- Create: `WebApp/apps/web/src/app/core/storage/mutation-store.ts`
- Create: `WebApp/apps/web/src/app/core/storage/overlay.ts`
- Create: `WebApp/apps/web/src/app/core/storage/storage-types.ts`
- Test: `WebApp/apps/web/src/app/core/storage/replica-store.spec.ts`
- Test: `WebApp/apps/web/src/app/core/storage/overlay.spec.ts`
- Modify: `WebApp/docs/implementation-progress.md`

**Interfaces:**
- Consumes: `idb` 8.0.3, `fake-indexeddb` 6.2.5, sync contracts, domain patch logic.
- Produces: `openReplicaDatabase(identity)`, `ReplicaStore`, `MutationStore`, and `buildPersonOverlay(base, pending)`.

- [ ] **Step 1: Write failing persistence tests**

Using `fake-indexeddb`, prove:

- base entities and cursor persist after closing/reopening;
- mutation order persists;
- an atomic queue+overlay write survives reopen;
- acknowledging one mutation removes only that mutation;
- Actor A and Actor B use distinct database names;
- restricted-import and demo stores cannot share a database.

- [ ] **Step 2: Run and verify red**

```bash
cd WebApp
pnpm --filter @qlgx/web test -- replica-store.spec.ts overlay.spec.ts
```

Expected: FAIL because storage does not exist.

- [ ] **Step 3: Implement the IndexedDB schema**

Use database name:

```ts
`qlgx:${dataMode}:${parishId}:${actorId ?? 'restricted'}:v1`
```

Object stores: `meta`, `people`, `households`, `conflicts`, `mutations`, and `failedMutations`. A single upgrade function owns schema version 1.

- [ ] **Step 4: Implement deterministic overlay reconstruction**

Sort pending mutations by `createdAt`, apply only accepted local entity types, and leave the base object immutable. If a pending patch's `before` no longer matches the prior overlay, retain it and mark local state `needs-review` rather than discarding intent.

- [ ] **Step 5: Run, checkpoint, and commit**

Run web unit tests, build, lint, security checks, and commit:

```bash
git commit -m "Add durable browser replica storage"
```

## Task 12: Implement the Reconnect Synchronization Engine

**Files:**
- Create: `WebApp/apps/web/src/app/core/sync/sync-transport.ts`
- Create: `WebApp/apps/web/src/app/core/sync/sync-engine.ts`
- Create: `WebApp/apps/web/src/app/core/sync/sync-state.ts`
- Create: `WebApp/apps/web/src/app/core/sync/retry-policy.ts`
- Test: `WebApp/apps/web/src/app/core/sync/sync-engine.spec.ts`
- Test: `WebApp/apps/web/src/app/core/sync/retry-policy.spec.ts`
- Modify: `WebApp/docs/implementation-progress.md`

**Interfaces:**
- Consumes: replica/mutation stores and pull/push APIs.
- Produces: `SyncEngine.runOnce()`, `SyncEngine.start()`, `SyncEngine.stop()`, and a readonly `SyncState` signal.

- [ ] **Step 1: Write failing sync-cycle tests**

Use a deterministic fake transport and prove:

1. pull pages until `hasMore` is false;
2. update base and cursor atomically;
3. rebuild overlay before push;
4. push pending mutations in creation order;
5. acknowledge only returned mutation IDs;
6. final pull converges conflicts;
7. two concurrent `runOnce()` calls share one in-flight promise;
8. network failure leaves the queue intact;
9. validation rejection moves the mutation to `failedMutations`.

- [ ] **Step 2: Run and verify red**

```bash
cd WebApp
pnpm --filter @qlgx/web test -- sync-engine.spec.ts retry-policy.spec.ts
```

Expected: FAIL because the engine does not exist.

- [ ] **Step 3: Implement the exact cycle**

```ts
async runCycle(): Promise<void> {
  await this.pullAll();
  await this.rebuildOverlay();
  await this.pushInOrder();
  await this.pullAll();
}
```

Parse every response with the contracts package before persisting it.

- [ ] **Step 4: Implement bounded retry**

Use delays `1s, 2s, 4s, 8s, 15s`, add ±20% jitter, pause when `navigator.onLine` is false, reset after a successful cycle, and expose the next retry time. Validation and configuration failures never auto-retry.

- [ ] **Step 5: Run, checkpoint, and commit**

Run all web unit tests and build, then security checks. Commit:

```bash
git commit -m "Add browser synchronization engine"
```

## Task 13: Add Demo Editing and Sync Status

**Files:**
- Create: `WebApp/apps/web/src/app/people/person-editor.ts`
- Create: `WebApp/apps/web/src/app/people/person-form.ts`
- Create: `WebApp/apps/web/src/app/sync/sync-status.ts`
- Create: `WebApp/apps/web/src/app/sync/failed-mutations.ts`
- Test: matching `*.spec.ts`
- Modify: `WebApp/apps/web/src/app/people/person-detail.ts`
- Modify: `WebApp/apps/web/src/app/app.routes.ts`
- Modify: `WebApp/docs/implementation-progress.md`

**Interfaces:**
- Consumes: person domain transitions, mutation store, sync state, data mode.
- Produces: demo-only editor, optimistic queueing, pending badges, retry controls, and validation-failure review.

- [ ] **Step 1: Write failing editor tests**

Prove the editor:

- is absent in restricted mode;
- initializes from the current overlay;
- preserves year/month/day precision;
- creates a UUIDv7 mutation with `before`, `after`, and `baseVersion`;
- performs queue+overlay persistence before navigation;
- shows validation without queueing;
- displays a pending badge after save.

- [ ] **Step 2: Run and verify red**

```bash
cd WebApp
pnpm --filter @qlgx/web test -- person-editor.spec.ts sync-status.spec.ts
```

Expected: FAIL because editor/status components do not exist.

- [ ] **Step 3: Implement editor and offline save**

Use Angular typed reactive forms for this PoC. The save handler calls `validatePersonPatch`, then one storage transaction queues the mutation and updates the overlay.

- [ ] **Step 4: Implement sync status and failed-mutation review**

Render connection, last pull, queue count, retry time, and a manual retry button. Failed mutations show field names and safe validation text, never real field values in restricted mode.

- [ ] **Step 5: Run, visually verify, and commit**

Run web tests/build/lint. In a browser, set offline mode, save a fabricated edit, reload, and verify pending state. Capture screenshots only in demo mode. Commit:

```bash
git commit -m "Add offline person editing"
```

## Task 14: Add the Conflict Inbox and Resolution Flow

**Files:**
- Create: `WebApp/apps/web/src/app/sync/conflict-inbox.ts`
- Create: `WebApp/apps/web/src/app/sync/conflict-detail.ts`
- Create: `WebApp/apps/web/src/app/sync/conflict-actions.ts`
- Test: matching `*.spec.ts`
- Modify: `WebApp/apps/web/src/app/app.routes.ts`
- Modify: `WebApp/docs/implementation-progress.md`

**Interfaces:**
- Consumes: synchronized conflict records and mutation queue.
- Produces: keep, restore, and dismiss actions represented as idempotent queued mutations.

- [ ] **Step 1: Write failing conflict tests**

Prove list ordering, unread count, safe field labels, before/competing/applied comparison, and these actions:

```ts
keepConflict(conflictId)
restoreCompetingValue(conflictId, currentEntityVersion)
dismissConflict(conflictId)
```

Restore must enqueue a normal person mutation whose `before` is the current applied value and whose `after` is the competing value.

- [ ] **Step 2: Run and verify red**

```bash
cd WebApp
pnpm --filter @qlgx/web test -- conflict-inbox.spec.ts conflict-actions.spec.ts
```

Expected: FAIL because conflict UI/actions do not exist.

- [ ] **Step 3: Implement the inbox and actions**

Provide keyboard-operable comparison cards, explicit consequence copy, and confirmation for restore. Keep/dismiss do not mutate the person. All actions enter the durable queue and work offline.

- [ ] **Step 4: Verify mode and privacy boundaries**

Restricted mode may display imported records but has no conflict actions. No conflict component logs compared values. Demo screenshots use fabricated names only.

- [ ] **Step 5: Run, checkpoint, and commit**

Run web unit tests, build, lint, and security checks. Commit:

```bash
git commit -m "Add synchronization conflict review"
```

## Task 15: Build Neutral Import Profiling and Deterministic Transforms

**Files:**
- Create: `WebApp/packages/importer/package.json`
- Create: `WebApp/packages/importer/tsconfig.json`
- Create: `WebApp/packages/importer/src/profile/profile-row.ts`
- Create: `WebApp/packages/importer/src/profile/profile-summary.ts`
- Create: `WebApp/packages/importer/src/transform/giao-ho.ts`
- Create: `WebApp/packages/importer/src/transform/vai-tro.ts`
- Create: `WebApp/packages/importer/src/transform/gia-dinh.ts`
- Create: `WebApp/packages/importer/src/transform/giao-dan.ts`
- Create: `WebApp/packages/importer/src/transform/thanh-vien-gia-dinh.ts`
- Create: `WebApp/packages/importer/src/transform/transform-row.ts`
- Create: `WebApp/packages/importer/src/import-types.ts`
- Test: `WebApp/packages/importer/src/transform/*.spec.ts`
- Modify: `WebApp/docs/implementation-progress.md`

**Interfaces:**
- Consumes: `NeutralRow`, partial-date parser, database import target types.
- Produces: `profileRow`, `transformRow`, typed target records, and discrepancy reason codes.

- [ ] **Step 1: Write fabricated neutral-row tests**

For each of the five tables, cover valid mapping, blank/null distinction, invalid encoding marker, ambiguous partial date, unknown enum, missing parent key, and duplicate legacy key. Use only invented Vietnamese names and keys such as `TEST-PERSON-001`.

- [ ] **Step 2: Run and verify red**

```bash
cd WebApp
pnpm --filter @qlgx/importer test -- transform
```

Expected: FAIL because transforms do not exist.

- [ ] **Step 3: Implement explicit transform results**

Every row returns:

```ts
type TransformResult<T> =
  | { disposition: 'loaded'; value: T }
  | { disposition: 'merged'; value: T; reason: 'DUPLICATE_CANONICAL_KEY' }
  | { disposition: 'skipped'; reason: 'EMPTY_OPTIONAL_REFERENCE' }
  | { disposition: 'unresolved'; reason: DiscrepancyReason; legacyKey: string };
```

No transform silently substitutes an enum, relationship, date component, or identifier.

- [ ] **Step 4: Implement aggregate-only profiling**

Track counts for null/blank, partial-date precision, household size bands, unknown enum codes, broken references, and decoding status. Do not retain sample values. Apply minimum cohort size 10 before an aggregate may feed any private synthetic generator.

- [ ] **Step 5: Run, checkpoint, and commit**

Run importer/domain/contracts suites, build, lint, security checks, and commit:

```bash
git commit -m "Add deterministic Access transforms"
```

## Task 16: Add the Safe `mdbtools` Extractor and Restricted Staging

**Files:**
- Create: `WebApp/packages/importer/src/extract/source-policy.ts`
- Create: `WebApp/packages/importer/src/extract/mdbtools-runner.ts`
- Create: `WebApp/packages/importer/src/extract/mdbtools-extractor.ts`
- Create: `WebApp/packages/importer/src/extract/staging-writer.ts`
- Create: `WebApp/packages/importer/src/extract/schema-fingerprint.ts`
- Create: `WebApp/packages/importer/src/logging/safe-logger.ts`
- Test: matching `*.spec.ts`
- Test: `WebApp/packages/importer/src/extract/blank-seed.contract.spec.ts`
- Modify: `WebApp/docs/privacy-runbook.md`
- Modify: `WebApp/docs/implementation-progress.md`

**Interfaces:**
- Consumes: `mdbtools` 1.0.1 executable and neutral import contract.
- Produces: `assertAllowedSource`, `extractTables`, schema fingerprint, ignored JSONL staging, and value-safe run logs.

- [ ] **Step 1: Write failing source-policy and logger tests**

Reject symlinks, relative traversal, writable source handles, wrong extensions, files outside `WebApp/.restricted/import/`, and any staged output outside `WebApp/.restricted/staging/`. Capture logs and prove invented source values never appear.

- [ ] **Step 2: Run and verify red**

```bash
cd WebApp
pnpm --filter @qlgx/importer test -- source-policy.spec.ts safe-logger.spec.ts
```

Expected: FAIL because source policy and safe logger do not exist.

- [ ] **Step 3: Implement process execution without a shell**

Use `spawn` with `shell: false`, explicit argument arrays, a 60-second timeout per table, bounded stderr capture, and exit-code checks. Never place passwords in command arguments. Parse CSV as a stream and validate each row into `NeutralRow`.

- [ ] **Step 4: Contract-test the tracked blank seed**

The test may run schema commands against:

```text
Source/ChuongTrinh/Resources/giaoxu.mdb
```

Assert the five required tables/columns and a stable structural fingerprint. Do not copy the seed into `WebApp/`, assert row values, or modify it.

- [ ] **Step 5: Implement resume-safe staging**

Write one ignored JSONL file per table using a temporary name and atomic rename. Manifest fields are run ID, source structural fingerprint, table, aggregate row count, staging checksum, phase state, and diagnostic reference. Legacy keys remain inside restricted staging and never enter console output.

- [ ] **Step 6: Run, checkpoint, and commit**

Run importer contract/unit tests, inspect `git status --ignored --short` to ensure staging is ignored, run security checks, and commit:

```bash
git commit -m "Add safe Access extraction"
```

## Task 17: Implement Idempotent Import Loading and Reconciliation

**Files:**
- Create: `WebApp/packages/database/src/repositories/import-repository.ts`
- Create: `WebApp/packages/importer/src/load/load-run.ts`
- Create: `WebApp/packages/importer/src/load/load-table.ts`
- Create: `WebApp/packages/importer/src/reconcile/reconcile-run.ts`
- Create: `WebApp/packages/importer/src/reconcile/reconciliation-manifest.ts`
- Create: `WebApp/packages/importer/src/cli.ts`
- Test: `WebApp/packages/importer/src/load/load-run.integration.spec.ts`
- Test: `WebApp/packages/importer/src/reconcile/reconcile-run.spec.ts`
- Modify: `WebApp/packages/importer/package.json`
- Modify: `WebApp/docs/implementation-progress.md`

**Interfaces:**
- Consumes: staged neutral rows, transforms, parish transaction, stable legacy identities.
- Produces: `extract`, `profile`, `load`, `verify`, and `run` CLI commands with exit codes 0 for balanced success and non-zero for fail-closed outcomes.

- [ ] **Step 1: Write failing idempotency tests**

Using fabricated staging:

- first run inserts expected entities;
- second identical run performs zero entity updates;
- changed fabricated source updates one mapped target;
- relationships load after parents;
- duplicate source identities are unresolved;
- transaction failure resumes without duplicates;
- unbalanced totals exit unsuccessfully.

- [ ] **Step 2: Run and verify red**

```bash
cd WebApp
pnpm --filter @qlgx/importer test:integration -- load-run.integration.spec.ts
```

Expected: FAIL because loading and reconciliation do not exist.

- [ ] **Step 3: Implement dependency-ordered loads**

Order:

```ts
const loadOrder = [
  'GiaoHo', 'VaiTro', 'GiaDinh', 'GiaoDan', 'ThanhVienGiaDinh',
] as const;
```

Upsert by `(parish_id, legacy_source_table, legacy_key)`. Compare normalized mapped fields before updating so identical re-runs perform no update/version bump.

- [ ] **Step 4: Implement reconciliation**

For every table and complete run, calculate:

```ts
const balanced =
  source === loaded + merged + skipped + unresolved;
```

Write detailed source-key statuses only to the ignored manifest. Print run ID, table, four disposition counts, duration, and balanced status only.

- [ ] **Step 5: Implement the CLI**

Commands accept `--source`, `--input-root`, `--staging-root`, `--parish-id`, and `--settings-source` when explicitly needed. Resolve real paths and enforce containment. `run` executes extract, profile, load, and verify; any failed phase prevents subsequent phases.

- [ ] **Step 6: Run, checkpoint, and commit**

Run all importer/database tests, repeat the fabricated import twice, run security checks, and commit:

```bash
git commit -m "Add reconciled Access import loading"
```

## Task 18: Wire Restricted-Import Browse Mode End to End

**Files:**
- Modify: `WebApp/apps/api/src/config/data-mode.guard.ts`
- Modify: `WebApp/apps/api/src/browse/browse.service.ts`
- Modify: `WebApp/apps/api/src/sync/sync.service.ts`
- Modify: `WebApp/apps/web/src/app/core/data-mode.service.ts`
- Modify: `WebApp/apps/web/src/app/people/person-detail.ts`
- Modify: `WebApp/apps/web/src/app/households/household-detail.ts`
- Create: `WebApp/apps/api/src/restricted/restricted-mode.e2e-spec.ts`
- Create: `WebApp/apps/web/src/app/core/restricted-mode.spec.ts`
- Modify: `WebApp/docs/privacy-runbook.md`
- Modify: `WebApp/docs/implementation-progress.md`

**Interfaces:**
- Consumes: imported PostgreSQL rows, API mode guard, browse UI.
- Produces: searchable read-only imported people/households and server-enforced mutation rejection.

- [ ] **Step 1: Write failing restricted-mode tests**

Start API in `restricted-import`, seed fabricated imported rows, and prove:

- browse works;
- push, conflict action, and any person mutation return 403;
- entity and sync-table counts do not change;
- response/logs do not contain configured fabricated sentinel values beyond requested browse payload;
- web UI shows the persistent read-only banner and no mutation controls.

- [ ] **Step 2: Run and verify red**

```bash
cd WebApp
pnpm --filter @qlgx/api test:e2e -- restricted-mode.e2e-spec.ts
pnpm --filter @qlgx/web test -- restricted-mode.spec.ts
```

Expected: at least one mutation path is not yet guarded or the mode UI is incomplete.

- [ ] **Step 3: Close every mutation path**

Call `assertMutationAllowed()` inside the sync service before repository access. Treat actor switching, conflict review, seed reset, and demo-only endpoints as mutations. Return the same stable 403 response from all paths.

- [ ] **Step 4: Complete the restricted UI**

Keep people search/detail and household detail. Remove edit, actor switch, sync queue action, conflict action, and reset controls. Do not register service-worker/background sync for restricted mode.

- [ ] **Step 5: Run, checkpoint, and commit**

Run API/web/database/importer suites and security checks. Commit:

```bash
git commit -m "Enforce restricted import browsing"
```

## Task 19: Prove Offline Conflict Convergence in Two Browsers

**Files:**
- Create: `WebApp/apps/web/playwright.config.ts`
- Create: `WebApp/apps/web/e2e/fixtures/demo-app.ts`
- Create: `WebApp/apps/web/e2e/offline-conflict.spec.ts`
- Create: `WebApp/apps/web/e2e/idempotent-replay.spec.ts`
- Create: `WebApp/apps/web/e2e/restricted-read-only.spec.ts`
- Modify: `WebApp/apps/web/package.json`
- Modify: `WebApp/docs/implementation-progress.md`

**Interfaces:**
- Consumes: complete demo API/web stack and fabricated seed.
- Produces: browser-visible proof of persistence, conflict convergence, replay idempotency, and restricted rejection.

- [ ] **Step 1: Write the failing two-context scenario**

The test creates Browser Context A and B:

```ts
const actorA = await browser.newContext();
const actorB = await browser.newContext();
```

It selects the same fabricated person, takes A offline, edits/reloads A, edits/syncs B, reconnects A, waits for both to reach the same version/value, and verifies one conflict with the three expected fabricated values.

- [ ] **Step 2: Run and verify red**

```bash
cd WebApp
pnpm --filter @qlgx/web test:e2e -- offline-conflict.spec.ts
```

Expected: FAIL at the first unimplemented or incorrectly wired browser behavior.

- [ ] **Step 3: Fix only behavior exposed by the E2E test**

Use systematic debugging for each failure. Preserve the unit/integration contracts; do not add test-only production shortcuts. Rerun the focused scenario after each fix.

- [ ] **Step 4: Add replay and restricted scenarios**

Replay the acknowledged mutation ID through the API and assert entity version, change count, conflict count, and audit count remain stable. In restricted mode, verify UI controls are absent and a direct push returns 403.

- [ ] **Step 5: Inspect rendered behavior**

Run Playwright headed for the fabricated demo, inspect both contexts at desktop and tablet widths, and confirm:

- no console or page errors;
- pending status survives reload;
- conflict language distinguishes applied and competing values;
- keyboard focus remains visible;
- no restricted trace/video/screenshot is retained.

- [ ] **Step 6: Run, checkpoint, and commit**

Run:

```bash
cd WebApp
pnpm test
pnpm build
pnpm test:integration
pnpm test:e2e
pnpm security:staged
git diff --cached --check
```

Commit:

```bash
git commit -m "Prove offline conflict convergence"
```

## Task 20: Run the Private Import Smoke Test and Clean-Checkout Acceptance

**Files:**
- Modify: `WebApp/README.md`
- Modify: `WebApp/docs/privacy-runbook.md`
- Create: `WebApp/scripts/private-import-smoke.mjs`
- Create: `WebApp/scripts/verify-clean-checkout.mjs`
- Test: `WebApp/scripts/private-import-smoke.spec.mjs`
- Test: `WebApp/scripts/verify-clean-checkout.spec.mjs`
- Modify: `WebApp/package.json`
- Modify: `WebApp/docs/implementation-progress.md`

**Interfaces:**
- Consumes: ignored copied real database for private smoke, all synthetic automated suites.
- Produces: aggregate-only private verification and a reproducible clean-checkout acceptance command.

- [ ] **Step 1: Write failing script tests with fabricated inputs**

Test that the private smoke wrapper:

- refuses a source outside `WebApp/.restricted/import/`;
- refuses tracked or staged database files;
- invokes the importer twice;
- compares aggregate reconciliation JSON;
- checks duplicate counts and HTTP mutation rejection;
- deletes or suppresses browser traces, screenshots, and videos;
- redacts child-process output on failure.

- [ ] **Step 2: Run and verify red**

```bash
cd WebApp
node --test scripts/private-import-smoke.spec.mjs scripts/verify-clean-checkout.spec.mjs
```

Expected: FAIL because acceptance scripts do not exist.

- [ ] **Step 3: Implement aggregate-only private smoke**

The script accepts `--source` and verifies realpath containment. Its success JSON contains only:

```json
{
  "schemaCompatible": true,
  "runsBalanced": true,
  "secondRunDuplicateCount": 0,
  "reconciliationTotalsEqual": true,
  "readOnlyHttpVerified": true
}
```

On failure it prints a run ID and safe reason code, then points to the ignored restricted manifest.

Add the root script:

```json
{
  "scripts": {
    "importer:private-smoke": "node scripts/private-import-smoke.mjs"
  }
}
```

- [ ] **Step 4: Run the private test without recording real values**

From `WebApp/`, prepare the ignored input:

```bash
mkdir -p .restricted/import
cp -p ../giaoxu.mdb .restricted/import/phu-long-source.mdb
pnpm importer:private-smoke --source .restricted/import/phu-long-source.mdb
```

Run twice and compare aggregate-only results. Do not open developer tools on real pages, capture screenshots, retain Playwright artifacts, or paste row-level failures into chat. If discrepancies occur, report counts and reason codes only and leave details in the ignored manifest.

- [ ] **Step 5: Verify from an isolated clean checkout**

Create a temporary clone without copied real data, follow only `WebApp/README.md`, start PostgreSQL/API/web on confirmed unique ports, seed fabricated data, and run:

```bash
cd WebApp
pnpm verify:workspace
pnpm test
pnpm build
pnpm test:integration
pnpm test:e2e
```

Confirm the owning processes for all ports and that the rendered demo has no console/page errors.

- [ ] **Step 6: Perform final security and scope audit**

Run:

```bash
git status --short --ignored
git ls-files '*.mdb' '*.accdb' '.env' '.env.*'
git log --all -p -- WebApp | rg -i 'password|secret|token|private key'
cd WebApp
pnpm security:staged
```

Expected tracked database result: only the historical blank seed outside `WebApp/`. Review every textual match manually; no credential value, real parish row, restricted artifact, or new database may be tracked.

- [ ] **Step 7: Update progress, request code review, and commit**

Set progress to Task 20 complete with every green command. Invoke `superpowers:requesting-code-review`, address verified findings, rerun affected tests, then invoke `superpowers:verification-before-completion`.

Commit documentation and scripts:

```bash
git commit -m "Document PoC acceptance workflow"
```

## Final Acceptance Checklist

- [ ] Every new web application file is below `WebApp/`.
- [ ] Legacy `Source/`, `BIN/`, and `Release/` are unchanged by the PoC branch.
- [ ] Node, pnpm, TypeScript, Angular, NestJS, PostgreSQL image, and all package versions are exact.
- [ ] `.env`, real databases, runtime copies, restricted staging, and restricted browser artifacts are ignored and untracked.
- [ ] All committed data and captured browser evidence are fabricated.
- [ ] Two-parish RLS tests pass.
- [ ] Offline edit survives reload.
- [ ] Reconnect performs pull, overlay rebuild, ordered push, and final pull.
- [ ] Same-field stale edits apply and create visible conflicts.
- [ ] Unrelated-field stale edits do not create false conflicts.
- [ ] Mutation replay produces no duplicate side effect.
- [ ] Restricted-import API rejects every mutation path.
- [ ] Five-table Access import is deterministic and balanced.
- [ ] Second import creates no duplicate and produces identical reconciliation totals.
- [ ] Private smoke output contains aggregate results only.
- [ ] Clean-checkout test/build/integration/E2E commands pass.
- [ ] Final worktree is clean and `implementation-progress.md` records completion.
