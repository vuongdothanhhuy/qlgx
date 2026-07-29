# People and Household Legacy Inventory

## Status

This is the repository-derived review packet for LEG-002. The static catalog is
complete enough for a domain walkthrough, but LEG-002 remains open until a
domain expert reviews the list and supplies representative examples.

Observed behavior is evidence, not approved policy. Rows that conflict, depend
on age constants, or distinguish archive/transfer/death/“not counted” states
must be resolved through BUG-003 and the future flow review.

## Compiled form and control catalog

| ID | Surface | Primary source | Observed responsibility |
|---|---|---|---|
| PHF-01 | Active parishioner list | `Source/ChuongTrinh/frmGiaoDanList.cs`, `Source/GXControl/GxGiaoDanList.cs` | List/filter by sub-parish, add/edit, permanent-delete-or-archive choice, reports, household navigation, death display, map action, marriage/bann actions |
| PHF-02 | Archived parishioner list | `Source/ChuongTrinh/frmGiaoDanLuuTruList.cs` | List/filter archived/transferred people, edit, delete, print, and related person actions |
| PHF-03 | Parishioner editor | `Source/GXControl/frmGiaoDan.cs` | Identity/profile, names, gender, parents, address/sub-parish, dates, sacrament fields, marriage/family state, transfer/death, vocation, association, catechism/profile details, photo |
| PHF-04 | Embedded person selector/editor | `Source/GXControl/GxGiaoDan.cs` | Select, add, edit, clear/delete reference, and show selected person details inside other workflows |
| PHF-05 | Person picker | `Source/GXControl/frmChonGiaoDan.cs` | Search/select a person for household, sacrament, catechism, marriage, and other relationship workflows; search includes archived people per CHG-013 |
| PHF-06 | Person search | `Source/ChuongTrinh/frmTimGiaoDan.cs` | Search by configured person fields including name, birth date, and address; returns the person list |
| PHF-07 | Person data review | `Source/ChuongTrinh/frmKiemTraGiaoDanList.cs`, `ReviewGiaoDanProcess.cs` | Detect invalid date sequences, household-membership anomalies, and marriage anomalies for operator review |
| PHF-08 | Active household list | `Source/ChuongTrinh/frmGiaDinhList.cs`, `Source/GXControl/GxGiaDinhList.cs` | List/filter by sub-parish, add/edit, permanent-delete-or-archive choice, household sheets, marriage documents, member counts, map action |
| PHF-09 | Archived household list | `Source/ChuongTrinh/frmGiaDinhLuuTruList.cs` | List/filter archived/transferred households, edit/delete, print, and restore/transfer-related actions |
| PHF-10 | Household editor | `Source/GXControl/frmGiaDinh.cs` | Husband/wife/head, marriage link, member roles, address/sub-parish, counted/not-counted state, transfer, photo, family creation from a couple |
| PHF-11 | Household search | `Source/ChuongTrinh/frmTimGiaDinh.cs` | Search household keyword and sub-parish, then load the matching household list |
| PHF-12 | Household data review | `Source/ChuongTrinh/frmKiemTraGiaDinhList.cs`, `ReviewGiaDinhProcess.cs` | Detect invalid dates, parent/child age gaps, marriage-before-age cases, and multiple-spouse/household inconsistencies |
| PHF-13 | View households for a person | `Source/GXControl/frmXemGiaDinhGiaoDan.cs`, `GxGiaoDanList.showGiaDinh` | Navigate from a person to household membership/history |
| PHF-14 | Import/review grids | `GxGiaoDanImportList`, `GxGiaDinhImportList`, `GxGiaoDanLoiList`, `GxGiaDinhLoiList` | Show staged/imported or invalid records; target behavior belongs to the migration workflow rather than normal editing |

Transfer forms (`frmChuyenHoGiaoDan`, `frmChuyenHoGiaDinh`) and marriage-driven
household behavior overlap this catalog but are finalized under LEG-004.
Reports launched from the lists are finalized under RPI-001–003.

## Observed validation and side-effect catalog

| ID | Source evidence | Observed behavior | Review state |
|---|---|---|---|
| PHV-01 | `frmGiaoDan.cs:256-450` | Person code is numeric and unique; name, gender, valid birth input, and a sub-parish are required by the editor | Pending: confirm required fields and server-generated ID policy |
| PHV-02 | `frmGiaoDan.cs:324-343`, `frmGiaDinh.cs:1324-1343` | Warn when “Ngoài xứ” and the “not counted” flag are combined unexpectedly | Pending: define independent location and statistics-inclusion states |
| PHV-03 | `frmGiaoDan.cs:377-396` | Enforce birth ≤ baptism ≤ First Communion ≤ confirmation; warn for First Communion below a configured age | Pending: confirm partial-date comparisons and warning threshold |
| PHV-04 | `frmGiaoDan.cs:412-418` | Baptism date requires a saint/baptismal name | Pending: CHG-066 / BUG-003 |
| PHV-05 | `frmGiaoDan.cs:572-620` | A selected father/mother must be at least 15 years older than the child | Pending: HIST-010 and incomplete-date policy |
| PHV-06 | `frmGiaoDan.cs:631-748` | Marking a married person deceased may update the surviving spouse's “has family/married” state | Pending: CHG-027/039; confirm status-history model |
| PHV-07 | `frmGiaoDan.cs:663-672` | Matching name, saint name, and birth date triggers a possible-duplicate warning rather than an automatic merge | Preserve candidate: explicit duplicate review |
| PHV-08 | `frmGiaoDan.cs:750-765` | Offer to add a person as a child in the selected parents' household, but reject conflicting child membership | Pending: exact multi-household exceptions |
| PHV-09 | `GxGiaoDanList.cs:915-945`, `frmGiaoDanList.cs:223-284` | Do not permanently delete a person referenced by a household; list action distinguishes permanent delete, archive, and cancel | Pending: replace hard delete with approved retention/archive policy |
| PHV-10 | `frmGiaoDan.cs:1502-1521` | Do not clear “has family” while qualifying marriage information remains, except where the partner has died | Pending: define derived versus stored status |
| PHV-11 | `frmGiaDinh.cs:450-614` | Wife must not be male; husband must not be female; a person cannot be spouse in another active household | Pending: confirm gender model and historical/archived-household exception |
| PHV-12 | `frmGiaDinh.cs:617-915` | Replacing husband/wife can prompt role/membership rewrites for the old/new people | Pending: require explicit before/after preview and atomic update |
| PHV-13 | `frmGiaDinh.cs:1030-1188` | Reject duplicate member rows and, normally, a non-spouse member who belongs to another household; warn for deleted/transferred people | Pending: CHG-050/051 and allowed historical memberships |
| PHV-14 | `frmGiaDinh.cs:1223-1439` | Household code/name/sub-parish and at least one spouse are required; detect duplicate couple; warn for deceased/transferred spouse; choose/repair household head | Pending: confirm whether single-adult/non-spousal households are valid |
| PHV-15 | `frmGiaDinh.cs:1576-1966` | Saving can cascade address, sub-parish, counted flag, and transfer state to members after confirmation; separately housed or archived members may be excluded from transfer | Pending: define field-by-field cascade and transactional failure behavior |
| PHV-16 | `frmGiaoDanList.cs:353-371`, `frmGiaDinh.cs:43` | Visual strike-through/status markers represent deceased, transferred, or separately housed members | Pending: replace formatting-only meaning with explicit accessible status labels |
| PHV-17 | `ReviewGiaoDanProcess.cs`, `ReviewGiaDinhProcess.cs` | Batch review flags date, membership, marriage, age-gap, and multiple-spouse anomalies without proving how to repair them | Preserve outcome: migration anomaly classes; replace live mutation with staged resolution |

## SQL and persistence catalog

The code builds Access SQL through `Source/DBAccess/SqlConstants.cs` and sends
tables/rows through the shared `DataProvider`. The target must preserve query
intent, not SQL text or table-shaped UI mutation.

| Family | Observed symbols and consumers | Intent to carry forward |
|---|---|---|
| Person identity/read | `SELECT_GIAODAN_THEO_ID`, `SELECT_CHECK_GIAODAN_TONTAI`, `SELECT_GIAODAN_LIST_CO_GIAOHO` | Fetch by stable identity, detect duplicate legacy code, list under authorized parish/sub-parish scope |
| Person write/delete | `DELETE_GIAODAN` plus table-row updates in `frmGiaoDan.updateDataGiaoDan` | Transactional person create/update; replace direct delete with relationship/retention policy |
| Household identity/read | `SELECT_GIADINH`, `SELECT_GIADINH_THEO_ID`, `SELECT_CHITIET_GIADINH`, `SELECT_GIADINH_LIST`, `SELECT_GIADINH_LIST_CO_HONPHOI` | Fetch/list household with spouse, head, marriage, sub-parish, status, and count projection |
| Household write/delete | `DELETE_GIADINH_THEO_ID`, `DELETE_CONCAI_THEO_GIADINH`, row updates in `frmGiaDinh.updateGiaDinh` | Atomic household aggregate mutation; no orphaned membership or partial cascades |
| Membership | `SELECT_THANHVIEN_GIADINH`, `SELECT_THANHVIEN_GIADINH_ALL`, `SELECT_THANHVIEN_GIADINH_LIST`, `SELECT_THANHVIENGIADINH_GIAODAN`, `SELECT_LISTGIAODAN_BYGIADINH` | Explicit membership entity with role, validity/history, inclusion status, and stable legacy key |
| Person-to-household navigation | `SELECT_LIST_GIADINH_BY_MAGIAODAN`, `SELECT_GIADINH_GIAODAN`, `SELECT_CHA_ME_THEO_GIADINH` | Navigate memberships and derive parent/household context without assuming only one historical relationship |
| Spouse/marriage consistency | `SELECT_CHECK_GIADINH_THEO_VOCHONG`, `SELECT_VOCHONG_THEO_MAGIADINH`, `SELECT_GIAODAN_THEO_HONPHOI`, `UPDATE_GIAODAN_DACOGIADINH`, `UPDATE_DACOGIADINH_THEOHONPHOI`, `UPDATE_CHUACOGIADINH_THEOHONPHOI` | Replace duplicated stored booleans with approved domain invariants/projections and status history |
| Parish constraints | `SELECT_COUNT_GIAODAN_THUOC_GIAOHO`, `SELECT_COUNT_GIADINH_THUOC_GIAOHO`, `DELETE_GIAOHO_CO_GIADINH`, `DELETE_GIAOHO_KHONG_CO_GIADINH` | Prevent hierarchy deletion while scoped people/households remain; enforce through foreign keys and explicit reassignment |

## Required domain walkthrough

The reviewer must provide at least these representative cases:

1. active person, archived person, transferred person, deceased person, and
   “not counted” person, including combinations that are legal;
2. married couple household, widowed household, single-adult household,
   household without both spouse roles, and archived former household;
3. a person with current and historical memberships and the rule for a child
   who later forms a household;
4. unknown year/month/day in birth and sacrament dates, plus a date sequence
   that cannot be compared precisely;
5. parent younger than the configured gap, duplicate-looking people, and a
   referenced person requested for deletion;
6. address/sub-parish/transfer changes where some members follow and some do
   not;
7. the authoritative definitions of household head, husband, wife, child,
   other member, active membership, and counted member.

For each case, record expected save result, warning versus hard error,
side effects, authorized role, list/statistics inclusion, and audit event.

## Static completion and blocker

| Check | Result |
|---|---|
| Compiled surfaces cataloged | 14 form/control groups covering active, archived, edit, search, picker, review, navigation, and import/error views |
| Validation families cataloged | 17 observed validation/side-effect groups with source paths |
| SQL intent cataloged | 8 query/mutation families with named legacy symbols |
| Rule-register links | Relevant CHG/HIST behaviors are referenced without promoting them to approved policy |
| Remaining LEG-002 evidence | **Blocked on domain-expert review and representative examples** |
