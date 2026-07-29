# Sacrament Legacy Inventory

## Status

This is the repository-derived review packet for LEG-003. It catalogs the
compiled sacrament surfaces, batch behavior, validation, and SQL intent.
LEG-003 remains open because each observed sacrament path still needs a
domain-approved representative example.

## Supported concepts observed

The person editor and batch code directly expose Baptism (`Rửa tội`), First
Communion (`Rước lễ lần đầu`), and Confirmation (`Thêm sức`). Later source and
history also add Anointing of the Sick (`Xức dầu`), Solemn Communion/catechism
(`Bao đồng`), and marriage catechism. The latter two are catechism/profile
facts, not necessarily sacrament-batch types. Their authoritative
classification must be reviewed.

## Compiled surface catalog

| ID | Surface | Primary source | Observed responsibility |
|---|---|---|---|
| SCF-01 | Sacrament batch list | `Source/ChuongTrinh/frmDotBiTichList.cs`, `Source/GXControl/GxDotBiTichList.cs` | Search/list batches, add/edit/delete, open batch detail |
| SCF-02 | Sacrament batch/detail editor | `Source/ChuongTrinh/frmBiTichChiTiet.cs`, `Source/GXControl/GxBiTichChiTiet.cs` | Batch code/type/date/place/clergy/description plus participant rows, sacrament numbers, household context, add/edit/remove, certificate printing |
| SCF-03 | Sacrament type selector | `Source/GXControl/GxLoaiBiTich.cs` | Loads available sacrament types and changes the active type |
| SCF-04 | Generate batches from person fields | `Source/ChuongTrinh/frmTaoDotBiTich.cs`, `Source/GXControl/GenerateDotBiTichProcess.cs` | Select type/date range/place/clergy, find matching person-level facts, group them into batches, and create missing participant links |
| SCF-05 | Person sacrament detail | `Source/GXControl/frmGiaoDan.cs` | Stores/displays person-level dates, places, clergy/sponsor, and sacrament numbers alongside batch-linked data |
| SCF-06 | Sacrament certificate/report launch | `Source/GXControl/frmReportChungNhanBT.cs`, `GxBiTichChiTiet.InChungNhan`, `GxGiaoDanList.XuatChungNhanBiTich` | Select people/type and generate sacrament certificates; output contracts belong to RPI-001–003 |
| SCF-07 | Introduction documents | `Source/GXControl/ReportGioiThieuRuaToi.cs`, `RpGioiThieuThemSuc.cs` | Generate baptism/confirmation introduction documents; fields and eligibility need report/flow review |

## Batch-generation behavior

`GenerateDotBiTichProcess.reViewData` performs the following legacy sequence:

1. accepts only Baptism, First Communion, or Confirmation in its type switch;
2. maps the type to date, clergy, and place columns on `GiaoDan`;
3. selects person rows whose encoded partial date lies in a supplied range,
   optionally matching place and clergy;
4. reuses a batch when type, lowercased clergy name, and normalized date match;
5. otherwise creates a batch with a generated ID and a description assembled
   from the known day/month/year parts;
6. adds a participant link only when the person/batch pair is absent;
7. updates the batch and participant tables together at the end.

The grouping key notably omits place even though place filters selection and
is copied into new batches. That may merge same-date/same-clergy events at
different places. The review must explicitly approve a target grouping key.

## Validation and side-effect catalog

| ID | Source evidence | Observed behavior | Review state |
|---|---|---|---|
| SCV-01 | `frmBiTichChiTiet.cs:165-183` | Reject the same person twice in one batch and reject the same person in another batch of the same type | Pending: decide whether correction/reception duplicates or repeated rites are valid |
| SCV-02 | `frmBiTichChiTiet.cs:186-222` | Warn, but permit confirmation, when adding a deceased, transferred, or archived/deleted person | Pending: define historical entry versus current administration |
| SCV-03 | `frmBiTichChiTiet.cs:246-261` | Removing a participant separately asks whether to remove the person's sacrament information | Pending: define authoritative ownership and atomic side effects |
| SCV-04 | `frmBiTichChiTiet.cs:277-307` | Batch code must be numeric/unique, description required, and at least one participant required | Pending: server-generated identity and whether empty draft batches are allowed |
| SCV-05 | `frmBiTichChiTiet.cs:435-480` | Missing sacrament numbers are highlighted and produce Yes/No/Cancel warning, but save may proceed | Pending: warning versus hard requirement by sacrament/diocese |
| SCV-06 | `GxBiTichChiTiet.cs:138-195` | Inline household editing is constrained: existing household membership can block change; household code must exist; selected person cannot already be spouse in that household | Pending: remove cross-aggregate inline mutation or define it explicitly |
| SCV-07 | `frmTaoDotBiTich.cs:29-52` | Automatic generation requires a type and from-date ≤ to-date | Pending: partial-date range semantics |
| SCV-08 | `GenerateDotBiTichProcess.cs:99-230` | Auto-generation is idempotent only for existing person/batch pairs and groups by type/clergy/date | Pending: grouping key, rerun safety, error rollback, place handling |
| SCV-09 | `frmGiaoDan.cs:377-418` | Person-level sacrament chronology is birth ≤ baptism ≤ First Communion ≤ confirmation; baptism date requires saint name; low First Communion age warns | Pending: BUG-003 and partial-date comparison |
| SCV-10 | `recorded-rules.md` CHG-006 | Future sacrament dates are allowed at entry and warn only on person save | Pending: distinguish planned events from completed reception |
| SCV-11 | `GxBiTichChiTiet.cs:131-136`, report launchers | Certificates can be printed from batch/person selections | Pending: authorization, required fields, template and audit contract |

Two legacy validation messages say “Mã gia đình” (household code) while checking
the sacrament-batch code. This is treated as a copy/paste UI defect, not domain
terminology.

## SQL and persistence catalog

| Family | Legacy evidence | Target intent |
|---|---|---|
| Batch list/read | `SELECT_DOTBITICH_LIST` | Query batch identity, type, description, date, place, clergy, participant count, and status under parish scope |
| Batch participants | `SELECT_BITICH_CHITIET_THEODOT` | Query ordered participant rows for one batch, including person and sacrament-number projection |
| Person/type uniqueness | `SELECT_BITICH_CHITIET_THEOLOAI` | Detect an existing rite/batch association for a person and type; replace string concatenation with scoped parameterized query |
| Batch write | `DotBiTich` table rows assembled in `frmBiTichChiTiet` and `GenerateDotBiTichProcess` | Transactional create/update with server identity, explicit partial date, type, place, minister, description, audit |
| Participant write | `BiTichChiTiet` table/DataSet mutations | Transactional batch membership with stable source key, order/number, correction history, and uniqueness approved per type |
| Person-field synchronization | Person columns used by `GenerateDotBiTichProcess` and updated from batch/detail workflows | Choose one source of truth plus derived projection; prevent divergent person-level and batch-level sacrament facts |

## Required representative examples

The domain walkthrough must provide, for each supported sacrament or
catechism milestone:

1. a normal completed event with full date, place, minister/sponsor, number,
   and one or more participants;
2. a historical record with partial date and missing optional values;
3. a future/planned date and the expected warning/save behavior;
4. the same person encountered in another batch of the same type;
5. an archived, transferred, or deceased person receiving historical data;
6. correction/removal behavior and whether person-level fields change;
7. certificate/intro-document eligibility and required fields.

For automatic generation, add cases with the same date/clergy at different
places, same date/place with different clergy spelling, rerun after partial
failure, and an existing batch with only some participant links.

## Static completion and blocker

| Check | Result |
|---|---|
| Compiled surfaces cataloged | 7 surface groups |
| Batch algorithm cataloged | Selection, grouping, reuse/create, participant deduplication, and persistence sequence |
| Validation cataloged | 11 observed validation/side-effect groups |
| SQL intent cataloged | 6 read/write/synchronization families |
| Remaining LEG-003 evidence | **Blocked on one approved example for every supported sacrament path and edge case** |
