# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

QLGX (Quản Lý Giáo Xứ) — a Vietnamese Catholic parish-management desktop application. C# WinForms, .NET Framework 2.0, x86, backed by a local Microsoft Access database. Distributed as an installer + self-update ZIP from http://quanlygiaoxu.net. Current shipping version is 3.3.7.

`AGENTS.md` holds the repository contribution conventions (style, commits, PRs). This file covers build commands and architecture.

## Build and run

Windows-only. `msbuild`, `dotnet`, and `mono` are **not installed on this machine** — you cannot compile or run the app here. Verify changes by reading code; do not claim a build passed.

On a Windows dev box with .NET Framework 2.0 SDK, Janus WinForms controls, and Office Interop available:

```
nuget restore "Source\GiaoXu - VS2015.sln"
msbuild "Source\GiaoXu - VS2015.sln" /t:Build /p:Configuration=Debug   /p:Platform="Any CPU"
msbuild "Source\GiaoXu - VS2015.sln" /t:Build /p:Configuration=Release /p:Platform="Any CPU"
BIN\GiaoXu.exe
```

All seven projects emit to `BIN\` (`OutputPath` is `..\..\BIN\`), which is committed and doubles as the runtime directory: third-party DLLs, `Template/`, `help/`, `version.txt`. `Release/` holds the packaged `qlgx_X_Y_Z.exe` installers and `qlgx_X_Y_Z_update.zip` deltas.

`Source/GiaoXu.sln` and `Source/GiaoXu - VS2015.sln` are byte-identical; use the VS2015 one.

There is **no automated test suite and no CI**. `TabStrip.Test` is a vendor demo, not a test project. Verification means building the solution, launching it, and exercising the affected workflow by hand.

## File encoding — read this before editing

Roughly half the `.cs` files are **UTF-16 LE with BOM**, the rest ASCII or UTF-8-BOM, mixed within the same directory (e.g. `Source/DBAccess/GxConstants.cs` is UTF-16, `Source/DBAccess/DBAccess.cs` is ASCII). Reading a UTF-16 file with a byte-oriented tool yields `u\0s\0i\0n\0g\0` garbage, and Vietnamese diacritics render as mojibake.

- Check first: `file Source/path/File.cs`
- To read a UTF-16 file in the shell: `iconv -f UTF-16 -t UTF-8 <file>`
- When editing, **preserve the original encoding**. Rewriting a UTF-16 file as UTF-8 produces a whole-file diff and breaks the Vietnamese literals that drive UI text and business logic.

## Architecture

Seven projects, layered bottom-up:

| Project | Assembly | Role |
|---|---|---|
| `Source/DBAccess` | `GXGlobal` | Data access, global state, constants, Office engines |
| `Source/ConvertFont` | `vnConvert` | Legacy Vietnamese font/encoding conversion (VNI ↔ Unicode) |
| `Source/GXControl` | `GXControl` | `frmBase`, `GxBaseField`, ~80 domain-aware UserControls |
| `Source/ExcelReport` | `ExcelReport` | Report and chart generators |
| `Source/Giaoly` | `Giaoly` | Catechism module (students, classes, grade levels) |
| `Source/ChuongTrinh` | `GiaoXu.exe` | Main app: `frmMain` + ~60 forms |
| `Source/AutoUpdate` | `AutoUpdate.exe` | Self-updater, loaded by reflection |

`Source/AutoCompleteTextBox`, `Source/TabStrip_src`, and `Source/GxTranslation` are not in the solution — bundled third-party/sample/stub code. Leave them alone unless a change specifically requires them.

### `Memory` — the global god-object

`Source/DBAccess/CMemory.cs` (namespace `GxGlobal`, 2000+ lines) is a static singleton that nearly every form touches. It owns:

- the `DataProvider` connection (`Memory.Instance.Provider`)
- static query helpers: `Memory.GetData(sql, params)`, `ExecuteSqlCommand`, `UpdateDataSet(ds)`, `GetTable`, `DeleteRows`, `GetNextId`
- app config, read/written through the `CauHinh` table: `GetConfig(key)` / `SetConfig(key, value)`, keys are `GxConstants.CF_*`
- ambient session state: `CurrentGiaoHo`, `GiaoXuInfo`, `AppPath`, `DbVersion`, `ComputerID`, `IsDesignMode`
- the last error: code sets `Memory.Instance.Error = ex` and callers check `Memory.HasError()` / `Memory.ShowError()` instead of letting exceptions propagate
- a large pile of Vietnamese-specific static utilities — partial-date parsing (`GetDateFromString`, `GetDatePart`, `SplitDatePart`), name casing (`AutoUpperFirstChar`, `ChuanHoaDau`), version comparison

Follow the existing pattern rather than introducing DI or new statics: new queries go through `Memory.GetData`, new settings through `Memory.GetConfig`/`SetConfig`.

### Data access

`Source/DBAccess/DBAccess.cs` — `DataProvider` wraps `OleDbConnection` over the Access file, choosing `Microsoft.Jet.OLEDB.4.0` for `.mdb` and `Microsoft.ACE.OLEDB.12.0` for `.accdb`. It supports explicit transactions (`BeginTransaction`/`Commit`/`Rollback`) and `Execute(DataSet)` via `OleDbDataAdapter` + `OleDbCommandBuilder`.

The database is `giaoxu.mdb`, sitting next to the executable (`Memory.AppPath`), password-protected with credentials hardcoded as `GxConstants.DB_USER` / `DB_PASSWORD`. On first launch `Program.Main` extracts a blank `giaoxu.mdb` from the `GiaoXu.Resources.giaoxu.mdb` embedded resource.

There is **no ORM and there are no domain entities**. Everything is `DataSet`/`DataTable`/`DataRow`, addressed by string column names. The one guard against typos is the constants classes at the bottom of `Source/DBAccess/GxConstants.cs` — `GiaoDanConst`, `GiaDinhConst`, `HonPhoiConst`, `BiTichChiTietConst`, `HoiDoanConst`, etc., each with a `TableName` plus one `const string` per column. Use them; do not inline raw column strings.

SQL lives as `const string` in `Source/DBAccess/SqlConstants.cs`. It is **OLE DB positional-parameter SQL**: placeholders are bare `?`, so the order of the `params object[]` passed to `GetData` must match the query exactly. Named parameters do not work here. Access dialect applies (`IIF(...)`, `*` wildcards, no `TOP n` semantics you'd expect from SQL Server).

### UI composition

`frmMain` is an MDI-like tab host. It never opens child windows: `ShowForm(Form)` reparents the form into a `FATabStripItem` (`frm.TopLevel = false`, borderless, docked fill) and tracks it in a `dicShows` name→tab dictionary, so a form name can only be open once. `GXControl` cannot reference `GiaoXu.exe`, so lower layers reach the host through `GxGlobal.Dispatcher.ShowTab(frm)`, which finds `Application.OpenForms["frmMain"]` and invokes `ShowForm` **by reflection**. Renaming `frmMain` or `ShowForm` breaks this silently at runtime.

All application forms derive from `GxControl.frmBase`, which supplies the shared contract every screen relies on: a `DataSet DataObj`, `GxOperation Operation` (VIEW/ADD/EDIT/NONE), `Id`/`ParentId`, `CurrentRow`/`DataReturn` for parent↔child hand-off, a `HelpKey` wired to F1, and hotkeys (F6 save, F11 close; `GxAddEdit` adds F2 new / F3 delete / F4 edit).

Data-entry screens are assembled from `GxBaseField` subclasses (`GxTextField`, `GxDateField`, `GxComboField`, …) that read config on construction — e.g. auto-capitalization is gated on `GxConstants.CF_CHUANHOA_TUCHUANHOA`. `GxAddEdit` is the standard toolbar strip, exposing `AddClick`/`EditClick`/`DeleteClick`/`FindClick`/`PrintClick` events that the hosting form subscribes to. Grids are Janus `GridEX`; layouts are persisted as base64 XML in `app.config`.

Long-running operations implement `GxControl.IGxProcess` (`Execute()` plus `OnStart`/`OnExecuting`/`OnError`/`OnFinished` events) and are selected by the `ProcessOptions` enum — imports, exports, household transfers, data-review passes, and schema upgrades all ride this one interface.

### Reports

`ExcelEngine` and `WordEngine` (both in `Source/DBAccess/`) drive Office through COM Interop. Reports are **template-substitution**, not code-generated layout: open a `.doc`/`.xls` from `BIN/Template/`, replace placeholder keys with values (`Write_to_excel(key, value)`, `Replace(key, value)`, `FindRange`), save to a temp path, then show it.

Templates are organized per diocese — `BIN/Template/Chung` (generic) plus `BMT`, `Phan Thiet`, `TP HCM`, `TP HCM A4`, `Vinh`, `Xuan Loc` — because certificate wording and layout are regional. `Memory.GetReportTemplatePath(name)` resolves against the folder chosen in config (`GxConstants.CF_TEMPLATE_FOLDER`). Report filenames are `GxConstants.REPORT_*_FILENAME` constants. A change to a report almost always means editing the `.doc`/`.xls` template, not C# code.

Office Interop is process-heavy and version-sensitive; `app.config` binding redirects pin `office` to 14.0 and `Microsoft.Vbe.Interop` to 12.0.

### Versioning, updates, and schema migration

Three version numbers move independently — keep them straight:

1. **App version** — `Source/ChuongTrinh/VersionConfig.xml` (`version-info value`/`display`, plus the Vietnamese changelog shown to users) and `BIN/version.txt`. `GxCheckVersion` downloads `{ServerUrl}version.txt`, compares, and if newer reflects into `AutoUpdate.exe` (`AutoUpdate.frmProcess.CheckVersion`) to download and apply the update ZIP. Server URL comes from the `SERVER` app setting, `VersionConfig.xml`'s `downloadpath`, or `GxConstants.SERVER_URL_DEFAULT`.
2. **Assembly version** — per-project `Properties/AssemblyInfo.cs`.
3. **Database schema version** — stored in the `CauHinh` table under `CF_CURRENT_DB_VERSION`.

Schema migration is a hand-written ladder in `Source/ChuongTrinh/UpdateProcess.cs` (~2450 lines, UTF-16) under `ProcessOptions.UpgradeVersion`: a long chain of `if (CompareVersion(Memory.DbVersion, "2.1.1.8") < 0) updateTo2_1_1_8();`. To add a schema change, append a new `updateToX_Y_Z_W()` that alters tables and then writes the new `CF_CURRENT_DB_VERSION` — never edit an existing step, since deployed parish databases have already run it. `GxCheckVersion.GetDbVersion()` additionally *probes* for expected columns and reports a lower version when the stored number lies, so migrations that are declared must actually land.

Cutting a release means bumping `VersionConfig.xml` + `BIN/version.txt`, rebuilding into `BIN/`, and adding both the installer and the update ZIP under `Release/`.

## Domain vocabulary

Identifiers, table names, comments, and UI strings are Vietnamese. The essentials:

| Term | Meaning |
|---|---|
| `GiaoXu` | Parish (the installation's own parish) |
| `GiaoPhan` / `GiaoHat` | Diocese / deanery |
| `GiaoHo` | Sub-parish district; the primary grouping for people and households |
| `GiaoDan` | Parishioner — the central person record |
| `GiaDinh` / `ThanhVienGiaDinh` | Household / household membership (roles: `VAITRO_CHONG` husband, `VAITRO_VO` wife, `VAITRO_CON` child) |
| `BiTich` / `BiTichChiTiet` / `DotBiTich` | Sacrament / sacrament record / sacrament batch |
| `RuaToi`, `RuocLe`, `ThemSuc`, `HonPhoi`, `AnTang`, `XucDau` | Baptism, First Communion, Confirmation, Marriage, Funeral, Anointing (see `LoaiBiTich`) |
| `RaoHonPhoi` | Marriage banns |
| `ChuyenXu` | Parish transfer in/out (`LOAI_CHUYENXU_*`) |
| `HoiDoan` / `ChiTietHoiDoan` | Parish association / membership |
| `GiaoLy`, `HocSinh`, `LopGiaoLy`, `KhoiGiaoLy` | Catechism, student, class, grade level |
| `LinhMuc` | Priest |
| `TaiKhoan` / `LoaiTaiKhoan` | User account / role |
| `CauHinh` | Configuration key-value table |
| `LuuTru` | Archived (soft-retired records, e.g. `frmGiaoDanLuuTruList`) |

Marriage/age rules are encoded as constants, not scattered literals: `TUOI_CHO_PHEP_KET_HON` (18), `TUOI_KHONG_CHO_PHEP_KET_HON` (14), `TUOI_CHO_PHEP_CO_CON` (15).

**Partial dates are a first-class concept.** Many sacramental dates are known only to the year or month. Dates are stored and manipulated as day/month/year string parts (`DateDTO`, `GetIntOfDateFrom`/`GetIntOfDateTo`, `GetMaskString`, `IsValidDateString`) rather than as `DateTime`. Do not "simplify" this into `DateTime` parsing — it will destroy precision information.

## Working in this codebase

- Match the surrounding legacy style: four-space indent, braces on their own line, `PascalCase` members, `frm`/`Gx` prefixes, `#region` blocks, Vietnamese comments where they already exist. Keep `.cs`/`.Designer.cs`/`.resx` triples together and prefer the WinForms designer for generated UI.
- Some names in the codebase are misspelled (`ConputerInfo.cs`, `frmAccoutList.cs`, `GxListAccout.cs`). They are load-bearing — do not rename them incidentally.
- `Source/ChuongTrinh/Copy of frmMain.*` and `Source/DBAccess/ExcelEngine_old.cs` are dead copies kept in tree; ignore them.
- Never commit parish databases (`*.mdb`, `*.accdb`), exports, or credentials. Review staged changes under `BIN/` and `Release/` especially carefully — binaries land there on every build.

## Related planning document

`WEB_MODERNIZATION_MASTER_PLAN.md` (untracked, at repo root) is a detailed plan to replace this desktop app with an Angular + NestJS + PostgreSQL web application. It is **planning only and authorizes no code, schema, or infrastructure changes**. Treat it as context for questions about the legacy system's behavior, not as a mandate to modify anything.
