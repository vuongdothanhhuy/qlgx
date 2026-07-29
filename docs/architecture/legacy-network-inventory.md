# Legacy Network Inventory and Retirement Decisions

## Purpose

This inventory records every outbound network behavior found in the compiled QLGX solution, the data it can disclose or execute, and the modernization decision for that behavior.

“Retire” means the legacy endpoint or protocol is not recreated in the web application. It does not authorize changing deployed 3.3.7 desktop installations. Any useful product capability is replaced through the controlled target architecture described below.

## Method and scope

The review covered the projects included by `Source/GiaoXu - VS2015.sln`: `GiaoXu`, `GXGlobal`, `GXControl`, `AutoUpdate`, `ExcelReport`, `GiaoLy`, and `vnConvert`.

The search included:

- `WebClient`, download/upload, `OpenRead`, request, and socket APIs;
- HTTP/HTTPS, `mailto`, external `Process.Start`, and embedded-browser navigation;
- version, message, help, feedback, parish-registration, backup, and connectivity symbols;
- configuration and `VersionConfig.xml` URL sources;
- call sites and project-file membership, so compiled behavior is separated from dormant or uncompiled copies.

UTF-16 LE source was decoded before line-level inspection. No endpoint was contacted, and no production parish data or credential value was used.

## Executive decision

All legacy first-party HTTP endpoints, the self-updater, remote message/help loading, silent parish registration, dormant database upload, and third-party connectivity probes are retired.

The modern application permits only:

1. the configured HTTPS application/API origin;
2. explicitly approved storage or operational services with authenticated HTTPS;
3. a user-initiated support destination approved by the project owner;
4. any future third-party navigation approved by the privacy review.

Startup, login, migration, and schema upgrade must not transmit parish identity, record counts, clergy details, addresses, or files to an unrelated legacy service.

## Base URL resolution

The desktop application has several conflicting plaintext base URLs:

| Source | Value or behavior | Effect |
|---|---|---|
| `Source/DBAccess/CMemory.cs:73-79` | Default `Memory.ServerUrl` uses `http://qlgx.net/` | Shared by version, message, help, feedback, about, and parish-registration code |
| `Source/DBAccess/GxConstants.cs:43` | Fallback uses `http://quanlygiaoxu.net/` | Used by the About form when no runtime value exists |
| `Source/ChuongTrinh/VersionConfig.xml:153` | Download path uses `http://quanlygiaoxu.net/` | Loaded locally and becomes `Memory.ServerUrl` |
| `Source/AutoUpdate/UpdateProcessInformation.cs:31-36` | Auto-updater default uses `http://quanlygiaoxu.net/` | May be replaced by the local or downloaded version manifest |
| `Source/ChuongTrinh/app.config:31` | Registration/backup service uses `http://www.qlgx.net/` | Separate base for `GiaoXuCL` and `BackupCL` |

`GxCheckVersion` can overwrite `Memory.ServerUrl` with the value returned by the update module (`GxCheckVersion.cs:109-115`). There is no single authoritative host, TLS requirement, host allowlist, authentication requirement, signature, or certificate policy.

## Endpoint inventory

| ID | Legacy destination | Trigger and status | Method and data | Risk | Decision |
|---|---|---|---|---|---|
| NET-001 | ICMP to `www.google.com`, `www.luudiachiweb.com`, `www.yahoo.com`, and `www.vnexpress.net` | Called before most online actions through `Memory.IsConnectionAvailable`; active | Up to four five-millisecond pings; no application data | Third-party dependency, network metadata disclosure, ICMP may be blocked, and the loop condition can index past the host list before its catch (`CMemory.cs:1313-1349`) | **Retire.** Determine connectivity from the actual first-party API/sync request; do not probe unrelated hosts |
| NET-002 | `<Memory.ServerUrl>/version.txt` | Automatic startup check when `AUTO_UPDATE` is true; manual menu checks also active (`frmMain.cs:54-68`, `316-322`, `633-638`) | Plain HTTP GET; returns a version string | Unauthenticated/integrity-free update decision, blocking network dependency, errors often suppressed | **Retire.** Web releases are deployed by the controlled delivery pipeline; expose release metadata through the authenticated HTTPS app only if users need it |
| NET-003 | `<downloadpath>/VersionConfig.xml` | Active after the version check invokes `AutoUpdate.frmProcess.CheckVersion` | Plain HTTP GET to a temporary file; remote XML supplies version, executable name, update information, and download paths (`AutoUpdate.cs:70-147`) | Remote party controls subsequent download and executable selection; no schema hardening, signature, hash, or TLS | **Retire.** Deployment manifests are CI-owned artifacts and are never fetched and trusted by a client updater |
| NET-004 | Manifest-controlled archive or library URLs | Active when the user accepts an offered update | Plain HTTP file downloads followed by ZIP extraction with overwrite into the application directory or per-file downloads (`AutoUpdate.cs:155-239`) | Direct remote-code execution path; Zip Slip/integrity risks; overwrites binaries without verification; launches the downloaded application | **Retire immediately in the replacement.** Use immutable server deployments, reviewed migrations, health checks, and rollback; never unzip remote code on a client |
| NET-005 | `<Memory.ServerUrl>/help/thong_tin_cap_nhat.htm` | User clicks update details (`frmShowUpdateInfo.cs:37-40`) | Opens a plaintext HTTP page in the default handler | Untrusted content and host drift | **Retire.** Release notes are same-origin, versioned, escaped content distributed with the application |
| NET-006 | `<Memory.ServerUrl>/message.txt` | Automatic on every main-form load and manual through “Thông tin online” (`frmMain.cs:119-126`, `387-402`) | Plain HTTP GET; returned text is passed to `frmHelp.SetHelp` (`GxKiemTraThongBao.cs:29-54`) | Remote text can become an arbitrary URI loaded in an embedded browser; no authentication, integrity, content validation, or audit | **Retire.** If announcements are required, use an authenticated first-party API with typed fields, escaped rendering, expiry, role targeting, and audit |
| NET-007 | `<Memory.ServerUrl>/help/<topic>.htm` | Index help uses the online page whenever connectivity is reported; missing local topics fall back online (`frmHelp.cs:52-101`) | Embedded Internet Explorer navigation over HTTP | Mixed local/remote trust, obsolete browser engine, script/content injection, unavailable offline despite bundled help | **Retire.** Bundle versioned help in the Angular application and service-worker cache; same-origin HTTPS documentation may supplement it |
| NET-008 | `http://forum.qlgx.net/gopy` | Current feedback menu path; user initiated (`frmMain.cs:328-349`) | Arbitrary URL handed to the embedded help browser | Plain HTTP, obsolete/uncontrolled destination, embedded-browser risk | **Retire.** Replace with a configured HTTPS support page or mail link after owner and privacy approval; transmit no parish data automatically |
| NET-009 | `<Memory.ServerUrl>/gopy.aspx` | Compiled `frmGopY` form, but its only main-menu construction is commented out; dormant (`frmGopY.cs:24-60`, `frmMain.cs:328-331`) | Plain HTTP GET query containing user email, user name, parish name/address, and free-text feedback | Personal and free-text data in URL/query logs; no consent record, CSRF/authentication, reliable response handling, or visible error | **Retire and delete from the replacement.** A future support form requires explicit user action, privacy notice, minimal fields, authenticated HTTPS, retention rules, and abuse controls |
| NET-010 | `<Memory.ServerUrl>/themgiaoxu.aspx` | Active at the end of any legacy database-version upgrade through `UpdateProcess.sendGiaoXuInfo` (`UpdateProcess.cs:183-185`, `2188-2235`) | Plain HTTP GET query containing diocese, deanery and parish names; parish address, website, phone and email; family/person counts; current priest name, phone and email | Silent unauthorized telemetry; personal/contact data and institutional statistics in URL logs; no consent, authentication, purpose limitation, audit, or failure visibility | **Retire with no automatic replacement.** Migration and schema upgrades must never register or profile a parish. Any future directory is a separate opt-in product with explicit ownership and data policy |
| NET-011 | `<app.config SERVER>/GiaoXuCL/insert` | Helper reachable only from the dormant backup-upload path; inactive (`GxCheckVersion.cs:431-483`, `485-502`) | Plain HTTP form POST containing every column from the first `GiaoXu`, `GiaoHat`, and `GiaoPhan` rows; response IDs are written into the local database | Broad unbounded institutional-data disclosure, unauthenticated response controls local identifiers, no field allowlist or consent | **Retire and remove.** New identifiers are generated locally/server-side under the application’s own authenticated domain model |
| NET-012 | `<app.config SERVER>/BackupCL/uploadFile/<parish-id>` | Upload call is implemented but its only call site is commented out; dormant. Local ZIP backup creation remains active (`GxCheckVersion.cs:373-527`) | Plain HTTP multipart upload of a ZIP containing `giaoxu.mdb`, at most daily according to `LastUpload` | Complete parish database disclosure; no TLS, authentication, encryption contract, integrity proof, retention policy, restore proof, or operator consent | **Retire and prohibit.** PostgreSQL uses encrypted managed backups and tested restores; client replicas and legacy files are never uploaded by a background desktop task |
| NET-013 | `Memory.ServerUrl` website link | User clicks website on About form (`frmAbout.cs:15-39`) | Opens the configured plaintext site | Host changes with updater state; plain HTTP | **Retire the hardcoded legacy target.** The web app’s About page is local/same-origin; any external project site must be an owner-approved HTTPS URL |
| NET-014 | Hardcoded support `mailto:` link | User clicks email on About form (`frmAbout.cs:31-34`) | Opens the local mail client; no automatic payload | Destination is compiled, email may encourage users to attach sensitive data without guidance | **Retire the hardcoded address.** A configured support action may be preserved only with a warning not to attach parish data and with an approved support process |
| NET-015 | `https://www.google.com/maps/search/<encoded-address>` | User clicks map from person or household views; active in four call sites (`CMemory.cs:1684-1690`) | Opens a third-party map with the stored address in the URL | Discloses a person/household address to a third party; no consent or privacy decision is recorded | **Retire for the initial release.** Reintroduction requires privacy approval, an explicit user action, clear disclosure, provider configuration, and permission checks |

## Source evidence index

| Exact repository path | Relevant lines | Network evidence |
|---|---:|---|
| `Source/DBAccess/CMemory.cs` | 73–119, 1313–1349, 1684–1690 | Runtime server URL, version-manifest URL loading, third-party connectivity pings, and Google Maps navigation |
| `Source/DBAccess/GxConstants.cs` | 42–43 | Compiled support address and fallback legacy server URL; inspect only these lines because the file also contains the accepted legacy credential |
| `Source/ChuongTrinh/frmMain.cs` | 54–68, 119–166, 316–349, 387–402, 633–638, 745–790 | Startup/manual updater triggers, startup/manual messages, active feedback forum, and online-content rendering |
| `Source/ChuongTrinh/GxCheckVersion.cs` | 80–190, 373–527 | Version check, update-module invocation, local backup, dormant parish registration, and dormant database upload |
| `Source/AutoUpdate/AutoUpdate.cs` | 39–239 | Remote manifest and archive/library downloads, overwrite extraction, and application launch |
| `Source/AutoUpdate/UpdateProcessInformation.cs` | 31–36 | Auto-updater default server URL |
| `Source/AutoUpdate/Enums.cs` | 7–20 | Update modes and `VersionConfig.xml` filename |
| `Source/ChuongTrinh/UpdateProcess.cs` | 183–185, 2188–2235 | Active `sendGiaoXuInfo` call and complete outbound field construction |
| `Source/GXControl/GxKiemTraThongBao.cs` | 29–54 | Remote `message.txt` download and callback behavior |
| `Source/GXControl/frmHelp.cs` | 52–101 | Online help selection, missing-local-file fallback, and arbitrary-URI embedded-browser navigation |
| `Source/ChuongTrinh/frmGopY.cs` | 24–60 | Dormant feedback form validation and query-string submission |
| `Source/ChuongTrinh/frmAbout.cs` | 15–39 | User-initiated website and mail links |
| `Source/ChuongTrinh/frmShowUpdateInfo.cs` | 37–50 | Update-details page and local rendering of downloaded update text |
| `Source/ChuongTrinh/VersionConfig.xml` | 153 | Legacy update download base |
| `Source/ChuongTrinh/app.config` | 31 | Separate parish-registration and backup service base |

## Active, dormant, and excluded code

### Active

- Automatic and manual version checks, manifest/archive updater, message check, online help fallback, feedback forum link, parish information submission during legacy schema upgrade, About links, connectivity pings, and map navigation have reachable call sites.
- Local ZIP backup creation runs after startup except on first installation. The local backup itself is not a network action; only its commented upload call is retired here.

### Dormant but security-relevant

- `frmGopY` is compiled, but the main menu no longer constructs it.
- `uploadFile` and `GiaoXuCL/insert` are compiled, but the sole call to `uploadFile` is commented out.
- Dormant code remains evidence because a small future edit could reactivate it.

### Excluded from runtime inventory

- `Source/ChuongTrinh/Copy of frmMain.cs` is not included by `GiaoXu.csproj`; its older updater code is uncompiled.
- `Source/ConvertFont/Convert.cs` contains an author website only in a source comment.
- NuGet XML documentation URLs are metadata, not application requests.
- `Process.Start` calls that open locally generated Word, Excel, PDF, or other files are not network behavior.

## Replacement network boundary

### Application and sync

- The Angular client communicates only with the configured first-party NestJS API over HTTPS.
- Offline detection comes from actual sync/API results. `navigator.onLine` may be a hint but never proof.
- API requests use typed contracts, authentication, authorization, timeouts, bounded retries, and audit where required.
- Parish or person data never appears in URL query strings when it can be carried in an authenticated body. Sensitive GET parameters are prohibited.

### Updates and releases

- CI builds immutable artifacts and records their source revision.
- Deployment applies reviewed database migrations, runs health checks, and supports rollback.
- The browser receives application assets through HTTPS and the service worker; no client downloads and executes a ZIP or manifest-selected binary.
- Release notes are versioned same-origin content. They cannot select executable names or download locations.

### Help and announcements

- Core help is bundled and usable offline.
- Optional announcements use a typed, authenticated first-party endpoint and escaped UI components, not an embedded browser or arbitrary URI.
- Content has an owner, publication/expiry timestamps, role scope, and audit history.

### Feedback and support

- No background support or telemetry request is allowed.
- A support action is user initiated and never prepopulates parish identity, record counts, clergy data, addresses, logs, exports, or database files.
- The support destination, privacy notice, retention, and authorized readers are approved before release.

### Backup

- PostgreSQL backups are encrypted, access-controlled, monitored, and restored in a timed drill.
- The application client never uploads a database backup.
- IndexedDB replicas are not backups and are cleared according to the authentication/offline policy.

### External navigation

- Every external host is owner-approved and HTTPS.
- Navigation is visibly user initiated.
- When a URL contains parish or person data, the privacy review must approve the provider, disclosure text, permissions, and data minimization before implementation.

## Enforcement and verification

Before Gate 1:

1. define a production egress allowlist for the app, API, database, storage, and operational services;
2. set CSP `connect-src`, `frame-src`, and navigation rules to the smallest approved set;
3. add a static check that rejects the legacy hostnames and raw HTTP URLs in new application packages;
4. add browser tests proving startup, login, offline load, and reconnect do not contact third-party or legacy hosts;
5. add API tests proving migration/schema operations issue no outbound parish-registration or telemetry request;
6. verify no background path uploads an export, photo, replica, or database file;
7. review logs to ensure URLs, errors, and tracing attributes do not contain parish/person fields.

At cutover, the retained desktop application is read-only. Block its legacy network destinations at the deployment boundary where practical; do not rely on the remote hosts remaining unavailable.

## Completion matrix

| Requirement | Evidence |
|---|---|
| `sendGiaoXuInfo` covered | NET-010 records trigger, complete field classes, silent failure behavior, and no-replacement retirement |
| Updater covered | NET-002–005 record version, manifest, archive/library, execution, and details-page paths |
| Feedback covered | NET-008–009 and NET-014 separate active forum, dormant form submission, and email behavior |
| Parish registration and backup covered | NET-011–012 distinguish dormant code from active local backup |
| Remaining outbound behavior covered | NET-001, NET-006–007, NET-013, and NET-015 cover connectivity, messages/help, About, and maps |
| Every destination has a decision | NET-001–015 each states an explicit legacy retirement and replacement or reintroduction condition |
