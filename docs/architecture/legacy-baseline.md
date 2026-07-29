# Legacy Solution Baseline

## Purpose

This document establishes the static baseline for the legacy QLGX desktop
application before detailed workflow, rule, report, and data inventories begin.
It classifies every project built by `Source/GiaoXu - VS2015.sln`, identifies
entry points and dependencies, and records what is evidence for the
modernization rather than production code to reuse.

This is a repository-only baseline. It does not claim that the solution has
been built or that runtime behavior has been verified on a parish database.
Those checks require a supported Windows environment, the proprietary
dependencies, and representative data.

## Baseline method

The inventory was derived from:

- the seven `Project` entries and build mappings in
  `Source/GiaoXu - VS2015.sln`;
- output type, target framework, platform, source membership, references,
  project references, and output paths in each included `.csproj`;
- the executable `Program.cs` files;
- tracked source, runtime assets, report templates, and release packages;
- static searches that included decoding UTF-16 LE source where necessary.

No database content or credential value was opened, copied, or reproduced.
The tracked blank seed database is recorded only by path and project role.

## Solution identity

| Property | Repository evidence |
|---|---|
| Primary solution | `Source/GiaoXu - VS2015.sln` |
| Solution format | Visual Studio solution format 12.00, labeled Visual Studio 2013 despite the filename |
| Included projects | 7 |
| Configurations | `Debug|Any CPU` and `Release|Any CPU` |
| Actual project platform | All included projects target `x86` |
| Target runtime | All included projects target .NET Framework 2.0 |
| Shared output | Every included project writes to root `BIN/` |
| Primary executable | `BIN/GiaoXu.exe` from the `GiaoXu` project |
| Secondary executable | `BIN/AutoUpdate.exe` from the `AutoUpdate` project |
| Primary storage | Microsoft Access through `System.Data.OleDb`; the bootstrap database is embedded at `Source/ChuongTrinh/Resources/giaoxu.mdb` |

The solution's `Any CPU` configuration name is therefore misleading: each
project explicitly selects x86. A supported legacy verification environment
must honor x86 and must not infer cross-platform compatibility from the
solution configuration label.

## Project classification

| Project | Project file | Output and entry point | Direct project dependencies | Legacy responsibility | Modernization classification |
|---|---|---|---|---|---|
| `GiaoXu` | `Source/ChuongTrinh/GiaoXu.csproj` | WinExe `GiaoXu`; `Source/ChuongTrinh/Program.cs:24` creates the blank database when absent, loads configuration, then opens `frmLogin` at line 49 | `vnConvert`, `GXGlobal`, `ExcelReport`, `GiaoLy`, `GXControl` | Main shell, login, household/person workflows, sacraments, marriage/banns, parish hierarchy, import/merge/restore, statistics, account administration, and legacy update integration | **Behavioral reference.** Preserve validated user outcomes and rules; redesign the desktop shell, forms, session model, and orchestration for Angular/NestJS |
| `GXGlobal` | `Source/DBAccess/GXGlobal.csproj` | Library `GXGlobal`; no entry point | `vnConvert` | Access/OLE DB execution, shared memory/configuration, SQL constants, DTOs, validation, Excel/Word automation, and common utilities | **Schema and rule evidence.** Extract query intent, validation, types, and transformations; replace raw Access data access and global mutable state |
| `GXControl` | `Source/GXControl/GXControl.csproj` | Library `GXControl`; no entry point | `vnConvert`, `GXGlobal`, `ExcelReport` | Reusable WinForms controls plus much of the domain UI: people, households, sacraments, marriage, transfers, associations, clergy, import/merge, reports, and statistics | **Workflow and rule evidence.** Preserve validated semantics and acceptance examples; do not port Janus/WinForms control structure |
| `ExcelReport` | `Source/ExcelReport/ExcelReport.csproj` | Library `ExcelReport`; no entry point | `GXGlobal` | Excel/Word report population, charts, certificates, family book, marriage documents, and grid export | **Report contract evidence.** Preserve required fields, calculations, layout, and print outcomes; replace Office COM automation with server/browser-safe generation |
| `GiaoLy` | `Source/Giaoly/GiaoLy.csproj` | Library `Giaoly`; no entry point | `GXGlobal`, `ExcelReport`, `GXControl` | Catechism students, grades, classes, transfers, imports, and related notes | **Domain-module evidence.** Preserve validated catechism behavior inside the new application; do not create a separate service merely because it is a separate legacy assembly |
| `AutoUpdate` | `Source/AutoUpdate/AutoUpdate.csproj` | WinExe `AutoUpdate`; `Source/AutoUpdate/Program.cs:13` opens `frmProcess` | None | Downloads a manifest and update payload, overwrites local files, and relaunches the desktop application | **Retire.** Do not port or execute this updater; replace it with controlled web deployment as specified in `docs/architecture/legacy-network-inventory.md` |
| `vnConvert` | `Source/ConvertFont/vnConvert.csproj` | Library `vnConvert`; no entry point | None | Converts legacy Vietnamese encodings, including VNI, to Unicode | **Algorithmic evidence.** Reimplement only the proven mappings needed by import, with fixture-based tests; do not reuse the .NET binary in production |

All seven solution projects have an explicit disposition. No legacy assembly is
approved as a production dependency of the modern application.

## Internal dependency graph

```text
GiaoXu
├── GiaoLy
│   ├── GXControl
│   │   ├── ExcelReport
│   │   │   └── GXGlobal
│   │   │       └── vnConvert
│   │   ├── GXGlobal
│   │   └── vnConvert
│   ├── ExcelReport
│   └── GXGlobal
├── GXControl
├── ExcelReport
├── GXGlobal
└── vnConvert

AutoUpdate
└── no project dependency
```

The graph is acyclic, but responsibilities are not cleanly layered.
`GXControl` contains domain behavior and reports as well as reusable UI, while
`GXGlobal` combines infrastructure, mutable runtime state, validation, Office
automation, and domain DTOs. The new architecture must classify behavior by
domain responsibility rather than mirror these assembly boundaries.

## Startup and storage behavior

The primary application starts at `Source/ChuongTrinh/Program.cs`:

1. enable WinForms visual styles;
2. check for the configured database filename in the application directory;
3. if absent, copy the embedded `GiaoXu.Resources.giaoxu.mdb` stream to disk;
4. load shared configuration into `Memory`;
5. run `frmLogin`.

This confirms a local-install, single-process desktop model. It does not prove
the schema or content of a deployed parish database. The embedded seed is a
tracked historical exception under `docs/architecture/security-boundary.md`
and must not be replaced with real data.

`GXGlobal` uses `System.Data.OleDb` for Access commands and adapters. Other
code also uses the Jet OLE DB provider for legacy Excel import. These APIs,
connection assumptions, and SQL dialect are extraction evidence; PostgreSQL
schema, queries, transactions, and migrations must be designed explicitly.

## External runtime dependencies

| Dependency family | Observed use | Baseline consequence |
|---|---|---|
| Janus WinForms v3.5 | Grid, ribbon, calendar, button bar, explorer, filter, schedule, timeline, and common UI assemblies in `BIN/` | Required to render/build the legacy UI; not a modern web dependency |
| Microsoft Office Interop | Excel, Word, Office, VBE, and `stdole` assemblies; version 11 references in UI/report projects and version 14 references in `GXGlobal` | Legacy report verification needs compatible Office automation; replacement reports cannot assume Office is installed |
| ICSharpCode.SharpZipLib 0.85.5 | Backup/update archive handling | Preserve only validated archive/backup outcomes; retire updater behavior and use maintained platform libraries |
| Newtonsoft.Json 11.0.2 for .NET 2.0 | Referenced by `GiaoXu` from `Source/packages/` | Legacy implementation detail; new API contracts use the target stack's maintained serializer and validation |
| AutoComplete and TabStrip | Prebuilt UI assemblies referenced by `GiaoXu`/`GXControl` | Bundled UI implementation evidence only |
| Microsoft Jet/OLE DB | Access database and Excel access | Requires Windows/x86-compatible legacy verification; replaced by explicit PostgreSQL access in the target |

The repository tracks 178 files under `BIN/`, including proprietary/runtime
assemblies and application assets. Their presence is not a license grant and
does not prove that a new machine can build or run the solution.

## Source and asset profile

- The repository contains 378 tracked C# files under `Source/`: 167 are
  detected as UTF-16, 96 as UTF-8 with BOM, and 115 as ASCII.
- There are 146 tracked `Designer.cs` files. Designer-generated code and
  paired `.resx` files must be treated as layout evidence and must not receive
  bulk reformatting or encoding conversion.
- The seven included project directories contain 328 C# files and 109 `.resx`
  files. Other tracked source belongs to excluded, alternate, or bundled
  projects.
- There are 69 tracked Office document/spreadsheet files: 63 under
  `BIN/Template`, two under `BIN/Resources`, three under
  `Source/ChuongTrinh`, and one under `Release`.
- Root `Release/` contains 11 tracked legacy package artifacts; it is evidence
  of old distribution, not the target delivery mechanism.

Mixed encodings are a material inspection hazard. Tools that assume UTF-8 can
miss source or render it as binary. Any automated inventory must count project
membership and decode without rewriting the original files.

## Included versus repository-adjacent projects

The repository contains 13 `.csproj` files, but only the seven projects above
are included in the primary solution. The following are outside its build:

| Area | Status |
|---|---|
| `Source/AutoCompleteTextBox/` | Bundled third-party/sample source; the solution consumes a prebuilt `AutoComplete.dll` |
| `Source/TabStrip_src/` | Bundled control source and `TabStrip.Test` demonstration harness; the solution consumes a prebuilt `TabStrip.dll` |
| `Source/GxTranslation/GxTranslation.csproj` | Standalone translation utility, not included by the primary solution |
| `Source/ChuongTrinh/ChuongTrinh.csproj` | Older/alternate project file; not included by the primary solution |

These areas are excluded from application behavior inventories unless a
specific compiled call or migration requirement points to them. `TabStrip.Test`
is not an automated application test suite.

## Build, test, and release constraints

The repository currently provides no executable automated application test
suite. The required legacy verification path is:

1. use a Windows Developer Command Prompt with the required .NET Framework,
   x86 tooling, Janus components, Jet/OLE DB provider, and compatible Office
   dependencies;
2. restore packages;
3. build `Source\GiaoXu - VS2015.sln`;
4. confirm outputs in `BIN\`;
5. launch `BIN\GiaoXu.exe`;
6. exercise the affected workflow using approved representative data and
   record layout, validation, persistence, errors, and generated reports.

The current inspection host is macOS arm64 and has no `msbuild`, `nuget`, or
`dotnet` command available. Therefore this baseline is statically verified,
not build-verified. A future green modern build cannot substitute for legacy
behavior comparison.

## Preservation boundary

The modernization preserves only evidence that has been understood and
validated:

- domain terms, relationships, and proven business rules;
- supported partial/ambiguous data states found in real parish data;
- user-visible workflow outcomes and permissions;
- report fields, calculations, layouts, and print semantics;
- encoding mappings proven by sanitized or synthetic fixtures;
- reconciliation requirements and traceability to legacy identifiers.

It does not preserve:

- WinForms, Janus, Office COM, OLE DB, or .NET Framework 2.0 as target
  technologies;
- assembly boundaries, global mutable state, form inheritance, or raw SQL
  placement;
- the desktop updater, legacy network endpoints, embedded browser, or silent
  telemetry;
- binaries, credentials, live data, or historical packaging as reusable
  production assets.

## Completion matrix

| Requirement | Evidence |
|---|---|
| Every solution project identified | The solution identity and project table reconcile all seven `.sln` entries |
| Every project classified | Each row has a legacy responsibility and explicit preserve, replace, or retire disposition |
| Entry points identified | `GiaoXu` and `AutoUpdate` entry points and initial forms are recorded; five libraries have none |
| Project dependencies identified | Direct references are listed per project and reconciled in the dependency graph |
| External dependencies identified | Janus, Office Interop, SharpZipLib, Newtonsoft.Json, bundled UI libraries, and Jet/OLE DB are recorded |
| Repository-adjacent code separated | Six non-solution `.csproj` files are grouped by excluded area and not treated as application runtime |
| Verification limits explicit | Static-only status, missing macOS toolchain, Windows/x86 requirements, and absence of application tests are recorded |
| Security boundary preserved | No database content or credential value was inspected or reproduced |
