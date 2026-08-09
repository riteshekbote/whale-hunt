# Inventory: whale

## Seed 2026-08-07 (passive recon)

### Code surface
- github.com/naver/whale-browser-developers — official developer repo (only confirmed whale repo so far)
- More whale repos exist inside naver org (reposcan filters by 'whale' in name)

### Eligible focus areas
- Sync flow (Whale account sync: how it authenticates, stores, encrypts data)
- Whale-only third-party libraries (bundled, not part of Chromium)
- Browser core on latest version: crash/UXSS/URL handling in Whale-specific code

### Open questions
- Latest Whale version number (determine at report time)
- Which third-party libs are bundled ONLY in Whale (vs stock Chromium)
- Sync endpoint/host (expect naver infra — do NOT probe; static analysis only)

## 2026-08-07 18:43:32 UTC
- NEW Only 1 whale-named repo in entire `naver` GitHub org: `naver/whale-browser-developers` (seed assumed "more exist")
- NEW Repo is documentation-only — no browser source code, sync implementation, or bundled library manifests publicly available
- NEW 4 branches: `master` (Chromium cc docs only), `translate` (extension API docs + sample), `v2` (devcenter pointer), `jdkim/update_documents` (Chromium LUCI/Mojo docs)
- NEW Extension API surface documented: `whale.runtime`, `whale.storage`, `whale.sidebarAction`, `whale.windows`, `whale.tabs`, `whale.bookmarks`, `whale.browserAction` (from `translate` branch README.ko.md
- NEW Sample sidebar extension (`translate/src/sidebar-sample/`) uses `navigator.userAgent.includes('sidebar')` for context detection; content_scripts match ALL origins (`http://*/*`, `https://*/*`)
- NEW Issue #23 (open, 2025-03-30): "Ignore valid BCP47 Language tags in the Naver Whale Extensions store" → maps to `store.whale.naver.com`
- NEW NVD query reveals 21 known Whale CVEs; 6 CVEs in 2025 target sidebar + dual-tab environment (SOP bypass, iframe sandbox escape, CSP bypass)
- NEW Latest known affected version from CVEs: 4.35.351.12 (CVE-2025-69234/69235, Dec 2025); no 2026 CVEs published yet
- NEW `v2` branch README references `developers.whale.naver.com` and `lab.whale.naver.com` (Naver web services)
- CHANGED Repo metadata last updated 2025-10-22

## 2026-08-07 18:58:13 UTC
- NEW No new surface items since last aggregated hypotheses (2026-08-07 18:43:32 UTC) — inventory, knowledge, and leads unchanged
- NEW Latest stable Whale desktop version: v4.38.386.14 (June 25 2026) per Wikipedia infobox — 3 minor version bumps ahead of last CVE-fix version v4.35.351.12 (Dec 30 2025), ~6 months of undisclosed change
- NEW Wiki `whale.sidebarAction` docs reveal `show()` accepts a `url` parameter: "url to load in extension panel, if not defined loads the default page"
- NEW Wiki `whale.sidebarAction` docs warn: `use_navigation_bar` defaults true; when false "your extension page may be navigated to other websites from drag events"
- NEW Wiki page "How to avoid my extension from changing urls" confirms drag-drop navigation exposure is a documented security concern for sidebar extensions, provides mitigation code
- NEW NVD 2026 CVE query (pubStartDate=2026-01-01) returns zero results — no public CVEs exist for versions 4.35.352 through 4.38.386
- CHANGED Repo activity confirmed: last commit 2019-09-23 on both master and jdkim/update_documents; "updated" 2025-10-22 is metadata-only, no new code pushed

## 2026-08-07 19:17:01 UTC
- NEW No new surface items since last aggregated hypotheses (2026-08-07 18:43:32 UTC) — inventory, knowledge, and leads unchanged
- NEW Latest stable Whale desktop version: v4.38.386.14 (June 25 2026) per Wikipedia infobox — 3 minor version bumps ahead of last CVE-fix version v4.35.351.12 (Dec 30 2025), ~6 months of undisclosed change
- NEW Wiki `whale.sidebarAction` docs reveal `show()` accepts a `url` parameter: "url to load in extension panel, if not defined loads the default page"
- NEW Wiki `whale.sidebarAction` docs warn: `use_navigation_bar` defaults true; when false "your extension page may be navigated to other websites from drag events"
- NEW Wiki page "How to avoid my extension from changing urls" confirms drag-drop navigation exposure is a documented security concern for sidebar extensions, provides mitigation code
- NEW NVD 2026 CVE query (pubStartDate=2026-01-01) returns zero results — no public CVEs exist for versions 4.35.352 through 4.38.386
- CHANGED Repo activity confirmed: last commit 2019-09-23 on both master and jdkim/update_documents; "updated" 2025-10-22 is metadata-only, no new code pushed
- NEW 2026-08-07 REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope
- NEW 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
- NEW 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
- NEW 2026-08-07 ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
- NEW 2026-08-07 REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
- NEW 2026-08-07 REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
- NEW 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest stable v4.38.386.14 has 3 mi
- NEW 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358, CSP bypass via specific scheme) confirmed fixed in v4.33.325.17 (Oct 2025); latest v4.38.386.14 is ~8 months ahead, no new CV
- NEW 2026-08-07 CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system metadata refresh, no code changes
- NEW 2026-08-07 CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowledge gap for current-version vu
- CHANGED 2026-08-07 Latest known affected version from CVEs: 4.35.351.12 (CVE-2025-69234/69235, Dec 2025); no 2026 CVEs published yet — version increment to v4.38.386.14; 3 minor version bumps since last CVE-f

## 2026-08-07 20:10:31 UTC
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-07 18:43:32 UTC)
- NEW 2026-08-07 REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope
- NEW 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
- NEW 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
- NEW 2026-08-07 ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
- NEW 2026-08-07 REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
- NEW 2026-08-07 REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
- NEW 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest stable v4.38.386.14 has 3 mi
- NEW 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358, CSP bypass via specific scheme) confirmed fixed in v4.33.325.17 (Oct 2025); latest v4.38.386.14 is ~8 months ahead, no new CV
- NEW 2026-08-07 CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system metadata refresh, no code changes
- NEW 2026-08-07 CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowledge gap for current-version vu
- CHANGED 2026-08-07 Latest known affected version from CVEs: 4.35.351.12 (CVE-2025-69234/69235, Dec 2025); no 2026 CVEs published yet — version increment to v4.38.386.14; 3 minor version bumps since last CVE-f
- NEW No new surface items since last aggregated hypotheses (2026-08-07 18:43:32 UTC) — inventory, knowledge, and leads unchanged
- NEW hypotheses-ling3.txt ranks sidebarAction.show SOP bypass at confidence 60 (vs 55 from hypotheses-laguna.txt)
- CHANGED Browser risk score increased 75→78 (3 minor-version bumps since CVE-fix v4.35.351.12, 0 published CVEs in 6-month gap)

## 2026-08-07 20:58:17 UTC
- NEW 2026-08-07 REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope
- NEW 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
- NEW 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
- NEW 2026-08-07 ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
- NEW 2026-08-07 REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
- NEW 2026-08-07 REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
- NEW 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest stable v4.38.386.14 has 3 mi
- NEW 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358, CSP bypass via specific scheme) confirmed fixed in v4.33.325.17 (Oct 2025); latest v4.38.386.14 is ~8 months ahead, no new CV
- NEW 2026-08-07 CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system metadata refresh, no code changes
- NEW 2026-08-07 CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowledge gap for current-version vu
- CHANGED 2026-08-07 Latest known affected version from CVEs: 4.35.351.12 (CVE-2025-69234/69235, Dec 2025); no 2026 CVEs published yet — version increment to v4.38.386.14; 3 minor version bumps since last CVE-f

## 2026-08-07 21:57:03 UTC
- NEW Current timestamp 2026-08-07 21:39:04 UTC — ~41 minutes since last inventory aggregation (2026-08-07 20:58:17 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context

## 2026-08-07 22:22:47 UTC
- NEW Current timestamp 2026-08-07 22:18:21 UTC — ~80 minutes since last inventory aggregation (2026-08-07 20:58:17 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context

## 2026-08-07 23:07:05 UTC

## 2026-08-07 23:49:41 UTC
- NEW 2026-08-07 23:41:48 UTC — ~34 minutes since last inventory aggregation (2026-08-07 23:07:05 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context

## 2026-08-08 00:43:15 UTC
- NEW 2026-08-08 00:35:12 UTC — ~45 minutes since last inventory aggregation (2026-08-07 23:49:41 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context

## 2026-08-08 02:51:36 UTC
- NEW 2026-08-08 00:35:12 UTC — ~45 minutes since last inventory aggregation (2026-08-07 23:49:41 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context

## 2026-08-08 04:14:14 UTC
- NEW 2026-08-08 00:35:12 UTC — ~45 minutes since last inventory aggregation (2026-08-07 23:49:41 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context

## 2026-08-08 05:17:32 UTC
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 04:14:14 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context
- NEW Android sync asset pinned for the first time: com.naver.whale 3.9.14.9 (vc 15965), arm64-v8a XAPK 166.29 MB, SHA256 3c7232913cd054651eae6151d82cfd7719da1f35bf69e3cbc3da79bf1e011faf, published 2026-07-
- NEW Android patch cadence: 3.9.14.5 → 3.9.14.9 in ~6 weeks (2026-06-27 → 2026-08-05) with TWO re-uploads of 3.9.14.9 (2026-08-02, 08-05) — rapid churn on latest, sync encryption (added 3.8.6.2, 2025-04) s
- NEW 2026-08-08 00:35:12 UTC — ~45 minutes since last inventory aggregation (2026-08-07 23:49:41 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context

## 2026-08-08 06:06:56 UTC
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 04:14:14 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context

## 2026-08-08 06:40:43 UTC
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 06:06:56 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 04:14:14 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context
- NEW 2026-08-07 REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope
- NEW 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
- NEW 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
- NEW 2026-08-07 ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
- NEW 2026-08-07 REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
- NEW 2026-08-07 REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
- NEW 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest stable v4.38.386.14 has 3 mi
- NEW 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358, CSP bypass via specific scheme) confirmed fixed in v4.33.325.17 (Oct 2025); latest v4.38.386.14 is ~8 months ahead, no new CV
- NEW 2026-08-07 CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system metadata refresh, no code changes
- NEW 2026-08-07 CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowledge gap for current-version vu
- CHANGED 2026-08-07 Latest known affected version from CVEs: 4.35.351.12 (CVE-2025-69234/69235, Dec 2025); no 2026 CVEs published yet — version increment to v4.38.386.14; 3 minor version bumps since last CVE-f
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 04:14:14 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-07 18:43:32 UTC)
- NEW 2026-08-07 REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope
- NEW 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
- NEW 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
- NEW 2026-08-07 ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
- NEW 2026-08-07 REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
- NEW 2026-08-07 REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
- NEW 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest stable v4.38.386.14 has 3 mi
- NEW 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358, CSP bypass via specific scheme) confirmed fixed in v4.33.325.17 (Oct 2025); latest v4.38.386.14 is ~8 months ahead, no new CV
- NEW 2026-08-07 CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system metadata refresh, no code changes
- NEW 2026-08-07 CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowledge gap for current-version vu
- CHANGED 2026-08-07 Latest known affected version from CVEs: 4.35.351.12 (CVE-2025-69234/69235, Dec 2025); no 2026 CVEs published yet — version increment to v4.38.386.14; 3 minor version bumps since last CVE-f

## 2026-08-08 07:48:25 UTC
- NEW 2026-08-07 REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope
- NEW 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
- NEW 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
- NEW 2026-08-07 ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
- NEW 2026-08-07 REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
- NEW 2026-08-07 REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
- NEW 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest stable v4.38.386.14 has 3 mi
- NEW 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358, CSP bypass via specific scheme) confirmed fixed in v4.33.325.17 (Oct 2025); latest v4.38.386.14 is ~8 months ahead, no new CV
- NEW 2026-08-07 CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system metadata refresh, no code changes
- NEW 2026-08-07 CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowledge gap for current-version vu
- CHANGED 2026-08-07 Latest known affected version from CVEs: 4.35.351.12 (CVE-2025-69234/69235, Dec 2025); no 2026 CVEs published yet — version increment to v4.38.386.14; 3 minor version bumps since last CVE-f
- NEW No new surface items since last aggregated hypotheses (2026-08-07 18:43:32 UTC) — inventory, knowledge, and leads unchanged
- NEW No new surface items since last aggregated hypotheses (2026-08-07 18:43:32 UTC) — inventory, knowledge, and leads unchanged
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-07 18:43:32 UTC)
- NEW Current timestamp 2026-08-07 21:39:04 UTC — ~41 minutes since last inventory aggregation (2026-08-07 20:58:17 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context
- NEW Current timestamp 2026-08-07 22:18:21 UTC — ~80 minutes since last inventory aggregation (2026-08-07 20:58:17 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context
- NEW 2026-08-07 REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope
- NEW 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
- NEW 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
- NEW 2026-08-07 ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
- NEW 2026-08-07 REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
- NEW 2026-08-07 REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
- NEW 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest stable v4.38.386.14 has 3 mi
- NEW 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358, CSP bypass via specific scheme) confirmed fixed in v4.33.325.17 (Oct 2025); latest v4.38.386.14 is ~8 months ahead, no new CV
- NEW 2026-08-07 CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system metadata refresh, no code changes
- NEW 2026-08-07 CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowledge gap for current-version vu
- CHANGED 2026-08-07 Latest known affected version from CVEs: 4.35.351.12 (CVE-2025-69234/69235, Dec 2025); no 2026 CVEs published yet — version increment to v4.38.386.14; 3 minor version bumps since last CVE-f

## 2026-08-08 08:21:21 UTC

## 2026-08-08 09:11:38 UTC
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 08:21:21 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected

## 2026-08-08 09:56:46 UTC
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 08:21:21 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected

## 2026-08-08 10:35:54 UTC
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 09:56:46 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected

## 2026-08-08 11:06:56 UTC
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 09:56:46 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected
- NEW Only 1 whale-named repo in entire `naver` GitHub org: `naver/whale-browser-developers` (seed assumed "more exist")
- NEW Repo is documentation-only — no browser source code, sync implementation, or bundled library manifests publicly available
- NEW 4 branches: `master` (Chromium cc docs only), `translate` (extension API docs + sample), `v2` (devcenter pointer), `jdkim/update_documents` (Chromium LUCI/Mojo docs)
- NEW Extension API surface documented: `whale.runtime`, `whale.storage`, `whale.sidebarAction`, `whale.windows`, `whale.tabs`, `whale.bookmarks`, `whale.browserAction` (from `translate` branch README.ko.md
- NEW Sample sidebar extension (`translate/src/sidebar-sample/`) uses `navigator.userAgent.includes('sidebar')` for context detection; content_scripts match ALL origins (`http://*/*`, `https://*/*`)
- NEW Issue #23 (open, 2025-03-30): "Ignore valid BCP47 Language tags in the Naver Whale Extensions store" → maps to `store.whale.naver.com`
- NEW NVD query reveals 21 known Whale CVEs; 6 CVEs in 2025 target sidebar + dual-tab environment (SOP bypass, iframe sandbox escape, CSP bypass)
- NEW Latest known affected version from CVEs: 4.35.351.12 (CVE-2025-69234/69235, Dec 2025); no 2026 CVEs published yet
- NEW `v2` branch README references `developers.whale.naver.com` and `lab.whale.naver.com` (Naver web services)
- CHANGED Repo metadata last updated 2025-10-22
- NEW Latest stable Whale desktop version: v4.38.386.14 (June 25 2026) per Wikipedia infobox — 3 minor version bumps ahead of last CVE-fix version v4.35.351.12 (Dec 30 2025), ~6 months of undisclosed change
- NEW Wiki `whale.sidebarAction` docs reveal `show()` accepts a `url` parameter: "url to load in extension panel, if not defined loads the default page"
- NEW Wiki `whale.sidebarAction` docs warn: `use_navigation_bar` defaults true; when false "your extension page may be navigated to other websites from drag events"
- NEW Wiki page "How to avoid my extension from changing urls" confirms drag-drop navigation exposure is a documented security concern for sidebar extensions, provides mitigation code
- NEW NVD 2026 CVE query (pubStartDate=2026-01-01) returns zero results — no public CVEs exist for versions 4.35.352 through 4.38.386
- CHANGED Repo activity confirmed: last commit 2019-09-23 on both master and jdkim/update_documents; "updated" 2025-10-22 is metadata-only, no new code pushed
- NEW 2026-08-07 REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope
- NEW 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
- NEW 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
- NEW 2026-08-07 ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
- NEW 2026-08-07 REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
- NEW 2026-08-07 REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
- NEW 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest stable v4.38.386.14 has 3 mi
- NEW 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358, CSP bypass via specific scheme) confirmed fixed in v4.33.325.17 (Oct 2025); latest v4.38.386.14 is ~8 months ahead, no new CV
- NEW 2026-08-07 CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system metadata refresh, no code changes
- NEW 2026-08-07 CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowledge gap for current-version vu
- CHANGED 2026-08-07 Latest known affected version from CVEs: 4.35.351.12 (CVE-2025-69234/69235, Dec 2025); no 2026 CVEs published yet — version increment to v4.38.386.14; 3 minor version bumps since last CVE-f
- NEW No new surface items since last aggregated hypotheses (2026-08-07 18:43:32 UTC) — inventory, knowledge, and leads unchanged

## 2026-08-08 11:42:42 UTC

## 2026-08-08 12:07:00 UTC

## 2026-08-08 13:16:20 UTC
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 12:07:00 UTC); no new public CVEs, no new GitHub commits, no new wiki edits, all binary acquisition pa

## 2026-08-08 13:59:13 UTC
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 13:16:20 UTC); no new public CVEs, no new GitHub commits, no new wiki edits, all binary acquisition pa

## 2026-08-08 14:37:43 UTC
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 13:59:13 UTC); no new public CVEs, no new GitHub commits, no new wiki edits, all binary acquisition pa
- NEW 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest v4.38.386.14 has 3 minor ver
- NEW 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
- NEW 2026-08-07 ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
- NEW 2026-08-07 REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
- NEW 2026-08-07 REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
- NEW 2026-08-07 CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowledge gap for current-version vu
- NEW 2026-08-07 CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system metadata refresh, no code changes
- NEW 2026-08-08 REJECTED @ naver/whale-browser-developers: Static analysis path dead; binary acquisition only vector
- NEW 2026-08-08 REJECTED passive binary acquisition @ APKMirror/APKCombo/apkpure: Cloudflare 403 on all paths — dead in-sandbox
- NEW 2026-08-08 ACCEPTED Android sync asset @ com.naver.whale 3.9.14.9: version + SHA256 pinned via non-Naver mirror metadata — in-scope sync surface confirmed real
- NEW 2026-08-08 CONFIRMED desktop latest @ changelog.whale.naver.com: Page is fully JS-rendered (empty text fetch) — no server-side version assertion available passively
- NEW 2026-08-08 ACCEPTED @ binary static analysis: Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account`, `_migration_done`, `whale_need_encryption_key_forced_time`) + Whale-forked `os_crypt
- NEW 2026-08-08 REJECTED passive binary acquisition @ APKMirror/APKCombo/apkpure: Cloudflare 403 returns on all paths — dead in-sandbox
- NEW 2026-08-08 ACCEPTED @ socket.io.slim.js bundled: Confirmed Whale-only in `resources.pak`; non-Chromium runtime-bundled lib worth auditing
- NEW 2026-08-08 REJECTED @ cloudfront CDN binary acquisition: DNS resolution fails for `*.cloudfront.net` (nslookup: No answer) — all binary acquisition paths blocked
- NEW 2026-08-08 ACCEPTED @ GitHub wiki documentation: `whale.sidebarAction` page accessible via `raw.githubusercontent.com/wiki/` — confirms `show({url})` loads arbitrary URL, `use_navigation_bar=false` ex
- NEW 2026-08-08 ACCEPTED @ GitHub sample extension source: `manifest.json` declares `content_scripts` matching `http://*/*` + `https://*/*`; `contentscript.js` detects sidebar context; `background.js` call
- NEW 2026-08-08 REJECTED @ APKMirror: Only hosts legacy versions (01.0.0.48/49), not latest 3.9.14.9
- NEW 2026-08-08 REJECTED @ uptodown: `whale-browser.en.uptodown.com` now returns HTTP 404 (was 410 Gone) — passive path permanently dead
- NEW 2026-08-08 CONFIRMED @ NVD: 0 Whale CVEs published in 2026 — 8-month disclosure gap
- NEW 2026-08-08 CONFIRMED @ GitHub: `naver/whale-browser-developers` remains documentation-only — 0 releases, 0 commits since 2019-09-23
- NEW 2026-08-08 CONFIRMED @ cloudfront DNS: `d1vdt4q2qgdbji.cloudfront.net` + `*.cloudfront.net` resolve `No answer` (127.0.0.53) — all binary acquisition paths blocked
- NEW 2026-08-08 CONFIRMED @ GitHub sample extension manifest: HTTP 200 — `content_scripts` matching ALL origins confirmed live on translate branch
- NEW 2026-08-08 REJECTED GitHub wiki sidebarAction docs: `raw.githubusercontent.com/wiki/naver/whale-browser-developers/sidebarAction.md` returns HTTP 404 — stale/unverified

## 2026-08-08 15:06:31 UTC
- NEW CONFIRMED @ GitHub sample extension source (translate branch), `js/background.js`: HTTP 200 — `whale.runtime.onMessage.addListener` handles `sidebarAction.show`/`hide`/`show2`/`hideAll` from ANY `send
- NEW CONFIRMED @ `static-whale.pstatic.net/WhaleSetup.exe`: HTTP 404 (server: Apache) — the online installer CDN artifact URL from bigpickle hypotheses is also dead; Naver pstatic infra excluded per scope.
- NEW CONFIRMED @ NVD API (keywordSearch=`naver+whale`, no date filter): returns exactly 2 CVEs total (CVE-2018-9859, CVE-2020-9754); 0 in 2026 — disclosure gap confirmed
- NEW CONFIRMED @ GitHub search API (`q=org:naver+whale`): 1 repo total (`naver/whale-browser-developers`); documentation-only; `pushed_at` 2019-09-23 — seed assumption of additional repos disproven

## 2026-08-08 15:48:16 UTC
- NEW GitHub sample extension `js/background.js` (translate branch): HTTP 200 — `whale.runtime.onMessage.addListener` handles `sidebarAction.show`/`hide`/`show2`/`hideAll` from ANY `sendMessage` origin (no 
- NEW `static-whale.pstatic.net/WhaleSetup.exe`: HTTP 404 (Apache) — online installer CDN artifact dead; Naver pstatic infra excluded per scope
- NEW NVD API (keywordSearch=`naver+whale`): exactly 2 CVEs total (CVE-2018-9859, CVE-2020-9754); 0 in 2026 — disclosure gap confirmed
- NEW GitHub search API (`q=org:naver+whale`): 1 repo total (`naver/whale-browser-developers`); documentation-only; `pushed_at` 2019-09-23 — seed assumption of additional repos disproven

## 2026-08-08 17:04:37 UTC
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 15:48:16 UTC); no new public CVEs, no new GitHub commits, no new wiki edits, all binary acquisition pa

## 2026-08-08 17:42:49 UTC
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 17:04:37 UTC); no new public CVEs, no new GitHub commits, no new wiki edits, all binary acquisition pa
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-07 18:43:32 UTC)
- NEW 2026-08-07 REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope
- NEW 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
- NEW 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
- NEW 2026-08-07 ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
- NEW 2026-08-07 REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
- NEW 2026-08-07 REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
- NEW 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest stable v4.38.386.14 has 3 mi
- NEW 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358, CSP bypass via specific scheme) confirmed fixed in v4.33.325.17 (Oct 2025); latest v4.38.386.14 is ~8 months ahead, no new CV
- NEW 2026-08-07 CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system metadata refresh, no code changes
- NEW 2026-08-07 CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowledge gap for current-version vu
- CHANGED 2026-08-07 Latest known affected version from CVEs: 4.35.351.12 (CVE-2025-69234/69235, Dec 2025); no 2026 CVEs published yet — version increment to v4.38.386.14; 3 minor version bumps since last CVE-f

## 2026-08-08 18:06:07 UTC
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 17:42:49 UTC); no new public CVEs, no new GitHub commits, no new wiki edits, all binary acquisition pa

## 2026-08-08 19:03:55 UTC

## 2026-08-08 19:31:15 UTC

## 2026-08-08 19:58:10 UTC

## 2026-08-08 20:26:23 UTC
- NEW No new surface items since last aggregated hypotheses (2026-08-08 19:58:10 UTC).
- NEW Live probe re-confirmed: sample extension `manifest.json` (HTTP 200), `background.js` (HTTP 200), `contentscript.js` (HTTP 200), `index.html` (HTTP 200) all still live on `translate` branch — ALL-orig
- NEW Live probe re-confirmed: NVD keywordSearch `naver+whale` returns exactly 2 CVEs total (CVE-2018-9859, CVE-2020-9754), 0 in 2026 — 8-month disclosure gap static for v4.35.352–v4.38.386.14.
- NEW Live probe re-confirmed: `naver/whale-browser-developers` pushed_at `2019-09-23T08:03:26Z`, updated_at `2025-10-22T03:15:17Z`, 4 branches unchanged (master, translate, v2, jdkim/update_documents) — do
- NEW Live probe re-confirmed: `raw.githubusercontent.com/wiki/naver/whale-browser-developers/sidebarAction.md` returns HTTP 404 — wiki documentation unreachable; SOP bypass attack-surface evidence rests so
- NEW Live probe re-confirmed: all binary acquisition channels blocked (cloudfront DNS `No answer`; APKMirror 403; Uptodown 404 page removed; pstatic 404; Naver domains OOS).
- NEW Wikipedia infobox confirms latest stable desktop version is still v4.38.386.14 (June 25 2026) — 3 minor-version bumps past last CVE-fix v4.35.351.12 (Dec 2025), 0 published CVEs in between.

## 2026-08-08 20:55:36 UTC
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 20:26:23 UTC); no new public CVEs, no new GitHub commits, no new wiki edits, all binary acquisition pa

## 2026-08-08 21:30:03 UTC
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 20:55:36 UTC); no new public CVEs, no new GitHub commits, no new wiki edits, all binary acquisition pa

## 2026-08-08 21:57:51 UTC
- NEW NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 21:30:03 UTC); no new public CVEs, no new GitHub commits, no new wiki edits, all binary acquisition pa

## 2026-08-08 22:29:16 UTC
- NEW No new surface items since last aggregated hypotheses (2026-08-08 19:58:10 UTC).
- NEW Live probe re-confirmed: sample extension `manifest.json` (HTTP 200), `background.js` (HTTP 200), `contentscript.js` (HTTP 200), `index.html` (HTTP 200) all still live on `translate` branch — ALL-orig
- NEW Live probe re-confirmed: NVD keywordSearch `naver+whale` returns exactly 2 CVEs total (CVE-2018-9859, CVE-2020-9754), 0 in 2026 — 8-month disclosure gap static for v4.35.352–v4.38.386.14.
- NEW Live probe re-confirmed: `naver/whale-browser-developers` pushed_at `2019-09-23T08:03:26Z`, updated_at `2025-10-22T03:15:17Z`, 4 branches unchanged (master, translate, v2, jdkim/update_documents) — do
- NEW Live probe re-confirmed: `raw.githubusercontent.com/wiki/naver/whale-browser-developers/sidebarAction.md` returns HTTP 404 — wiki documentation unreachable; SOP bypass attack-surface evidence rests so
- NEW Live probe re-confirmed: all binary acquisition channels blocked (cloudfront DNS `No answer`; APKMirror 403; Uptodown 404 page removed; pstatic 404; Naver domains OOS).
- NEW Wikipedia infobox confirms latest stable desktop version is still v4.38.386.14 (June 25 2026) — 3 minor-version bumps past last CVE-fix v4.35.351.12 (Dec 2025), 0 published CVEs in between.
- NEW None — all surface items static since 2026-08-08 21:57:51 UTC scan.

## 2026-08-08 23:01:24 UTC

## 2026-08-08 23:42:38 UTC

## 2026-08-09 00:02:57 UTC

## 2026-08-09 02:24:22 UTC

## 2026-08-09 03:58:25 UTC
- NEW Binary delivery directory created at `/tmp/opencode/whale_binary/` — awaiting HUMAN artifact push (`.deb` or `WhaleSetup.exe` v4.38.386.14)
- NEW All binary acquisition channels remain blocked in-sandbox (cloudfront DNS `No answer`, APKMirror 403, Uptodown 404 page removed, pstatic 404, Naver domains OOS)
- NEW NVD API: 0 Whale CVEs published in 2026 — keywordSearch `naver+whale` returns exactly 2 CVEs total (CVE-2018-9859, CVE-2020-9754), both pre-2021; 8-month disclosure gap static
- NEW GitHub repo `naver/whale-browser-developers`: `pushed_at`=2019-09-23, `updated_at`=2025-10-22, 0 releases — documentation-only surface unchanged
- NEW Sample extension source (translate branch): all 5 files (manifest.json, js/background.js, js/contentscript.js, index.html, js/index.js) still HTTP 200 — ALL-origin content_scripts + unvalidated `sideb
- NEW CVE-2025-69234/69235: Fixed v4.35.351.12 (Dec 2025), generic CPE `cpe:2.3:a:navercorp:whale:*:*:*:*:*:*:*:*` — Linux fix status unclaimed; v4.38.386.14 is 3 minor bumps past fix with 0 CVEs published
- NEW Sample extension (translate branch): all 5 files confirm unvalidated `sidebarAction.show`/`show2` dispatch from arbitrary web origin with `whale.runtime.onMessage.addListener` accepting sender param +
- CHANGED Wikipedia infobox confirms latest stable desktop v4.38.386.14 (June 25 2026) — 3 minor-version bumps past last CVE fix v4.35.351.12 (Dec 2025), 0 CVEs in between

## 2026-08-09 05:12:13 UTC

## 2026-08-09 05:58:21 UTC

## 2026-08-09 07:01:36 UTC
- NEW Background.js static analysis confirms ZERO origin validation in `whale.runtime.onMessage.addListener` — accepts `sender` param but never inspects `sender.origin`/`sender.url`, only string-equality on
- NEW Wikipedia infobox version assertion cannot be passively re-verified — HTML parse shows no version strings (4.38/4.35 not found)
- CHANGED NVD primary endpoint `nvd.nist.gov/rest` now Cloudflare-blocked in-sandbox; `services.nvd.nist.gov/rest/json/cves/2.0` remains reachable and returns identical results — route future CVE checks via ser
- NEW Sample extension (translate branch): all 5 files confirm unvalidated `sidebarAction.show`/`show2` dispatch from arbitrary web origin with `whale.runtime.onMessage.addListener` accepting sender param +
- CHANGED Wikipedia infobox confirms latest stable desktop v4.38.386.14 (June 25 2026) — 3 minor-version bumps past last CVE fix v4.35.351.12 (Dec 2025), 0 CVEs in between

## 2026-08-09 07:58:09 UTC

## 2026-08-09 08:47:46 UTC
- NEW Binary delivery directory created at `/tmp/opencode/whale_binary/` — awaiting HUMAN artifact push (`.deb` or `WhaleSetup.exe` v4.38.386.14)
- NEW Background.js static analysis confirms ZERO origin validation in `whale.runtime.onMessage.addListener` — accepts `sender` param but never inspects `sender.origin`/`sender.url`, only string-equality on
- CHANGED NVD primary endpoint `nvd.nist.gov/rest` now Cloudflare-blocked in-sandbox; `services.nvd.nist.gov/rest/json/cves/2.0` remains reachable — route future CVE checks via services host
- CHANGED Wikipedia infobox confirms latest stable desktop v4.38.386.14 (June 25 2026) — 3 minor-version bumps past last CVE fix v4.35.351.12 (Dec 2025), 0 CVEs in between

## 2026-08-09 09:31:18 UTC
- NEW Binary delivery directory created at `/tmp/opencode/whale_binary/` — awaiting HUMAN artifact push (`.deb` or `WhaleSetup.exe` v4.38.386.14)
- NEW Background.js static analysis confirms ZERO origin validation in `whale.runtime.onMessage.addListener` — accepts `sender` param but never inspects `sender.origin`/`sender.url`, only string-equality on
- CHANGED NVD primary endpoint `nvd.nist.gov/rest` now Cloudflare-blocked in-sandbox; `services.nvd.nist.gov/rest/json/cves/2.0` remains reachable — route future CVE checks via services host
- CHANGED Wikipedia infobox confirms latest stable desktop v4.38.386.14 (June 25 2026) — 3 minor-version bumps past last CVE fix v4.35.351.12 (Dec 2025), 0 CVEs in between

## 2026-08-09 10:07:26 UTC
- NEW Binary delivery directory created at `/tmp/opencode/whale_binary/` — awaiting HUMAN artifact push (`.deb` or `WhaleSetup.exe` v4.38.386.14)
- NEW Background.js static analysis confirms ZERO origin validation in `whale.runtime.onMessage.addListener` — accepts `sender` param but never inspects `sender.origin`/`sender.url`, only string-equality on
- CHANGED NVD primary endpoint `nvd.nist.gov/rest` now Cloudflare-blocked in-sandbox; `services.nvd.nist.gov/rest/json/cves/2.0` remains reachable — route future CVE checks via services host
- CHANGED Wikipedia infobox confirms latest stable desktop v4.38.386.14 (June 25 2026) — 3 minor-version bumps past last CVE fix v4.35.351.12 (Dec 2025), 0 CVEs in between

## 2026-08-09 10:52:02 UTC
- CHANGED Binary delivery directory `/tmp/opencode/whale_binary/` reported as created in prior scans (09:31:18, 08:47:46, 07:58:09, 07:01:36, 03:58:25 UTC) but now missing — `ls` returns "No such file or direct
- CHANGED NVD primary endpoint `nvd.nist.gov/rest` confirmed Cloudflare-blocked in-sandbox; `services.nvd.nist.gov/rest/json/cves/2.0` remains reachable (persistent since 07:01:36 UTC)

## 2026-08-09 11:30:22 UTC
- CHANGED Binary delivery directory `/tmp/opencode/whale_binary/` reported as created in prior scans (09:31:18, 08:47:46, 07:58:09, 07:01:36, 03:58:25 UTC) but now missing — `ls` returns "No such file or direct
- CHANGED NVD primary endpoint `nvd.nist.gov/rest` confirmed Cloudflare-blocked in-sandbox; `services.nvd.nist.gov/rest/json/cves/2.0` remains reachable (persistent since 07:01:36 UTC)

## 2026-08-09 11:52:29 UTC
- CHANGED Binary delivery directory `/tmp/opencode/whale_binary/` reported as created in prior scans (03:58, 07:01, 07:58, 08:47, 09:31 UTC) but now missing — `ls` returns "No such file or directory"
- CHANGED NVD primary endpoint `nvd.nist.gov/rest` confirmed Cloudflare-blocked in-sandbox; `services.nvd.nist.gov/rest/json/cves/2.0` remains reachable (persistent since 07:01 UTC)

## 2026-08-09 12:22:42 UTC
- NEW Binary delivery directory `/tmp/opencode/whale_binary/` still missing (reported created in prior scans but `ls` returns "No such file or directory")
- NEW NVD primary endpoint `nvd.nist.gov/rest` confirmed Cloudflare-blocked; `services.nvd.nist.gov` reachable and returns 0 Whale CVEs in 2026 (2 total, both pre-2021)
- NEW All binary acquisition channels remain 100% blocked in-sandbox (cloudfront DNS `No answer`, APKMirror 403, Uptodown 404 page removed, pstatic 404)
- CHANGED Wikipedia infobox version assertion cannot be passively re-verified — HTML parse shows no version strings (4.38/4.35/4.33 not found)
- CHANGED Sample extension source (translate branch): all 5 files still HTTP 200 — ALL-origin content_scripts + unvalidated `sidebarAction.show`/`show2` dispatch + zero origin validation in `onMessage` confirme
- CHANGED Binary delivery directory `/tmp/opencode/whale_binary/` reported as created in prior scans (03:58–11:52 UTC) but now missing — `ls` returns "No such file or directory"
- NEW Wikipedia infobox version assertion cannot be passively re-verified — HTML parse shows no version strings (4.38/4.35/4.33 not found at parse time)
- CHANGED NVD primary endpoint `nvd.nist.gov/rest` confirmed Cloudflare-blocked in-sandbox; `services.nvd.nist.gov/rest/json/cves/2.0` remains reachable (persistent since 07:01 UTC)
- NEW NVD API re-verify: `keywordSearch=naver whale` returns total=2 (CVE-2018-9859, CVE-2020-9754), both pre-2021 — 0 CVEs in 2026, disclosure gap static for v4.35.352–v4.38.386.14 (confirmed at scan 12:19
- CHANGED Sample extension (translate branch): all 5 files re-confirmed HTTP 200 (manifest.json, background.js, contentscript.js, index.html, index.js) — attack surface live and unchanged (re-verified at 12:19 

## 2026-08-09 13:28:44 UTC
- NEW Binary delivery directory `/tmp/opencode/whale_binary/` still missing (reported created in prior scans but `ls` returns "No such file or directory")
- NEW NVD primary endpoint `nvd.nist.gov/rest` confirmed Cloudflare-blocked; `services.nvd.nist.gov` reachable and returns 0 Whale CVEs in 2026 (2 total, both pre-2021)
- CHANGED Wikipedia infobox version assertion cannot be passively re-verified — HTML parse shows no version strings (4.38/4.35/4.33 not found)
- CHANGED Sample extension source (translate branch): all 5 files still HTTP 200 — ALL-origin content_scripts + unvalidated `sidebarAction.show`/`show2` dispatch + zero origin validation in `onMessage` confirme
- CHANGED All binary acquisition channels remain 100% blocked in-sandbox (cloudfront DNS `No answer`, APKMirror 403, Uptodown 404 page removed, pstatic 404)

## 2026-08-09 14:11:46 UTC
- NEW Binary delivery directory `/tmp/opencode/whale_binary/` still missing (reported created in prior scans but `ls` returns "No such file or directory")
- NEW NVD primary endpoint `nvd.nist.gov/rest` confirmed Cloudflare-blocked; `services.nvd.nist.gov` reachable and returns 0 Whale CVEs in 2026 (2 total, both pre-2021)
- CHANGED Wikipedia infobox version assertion cannot be passively re-verified — HTML parse shows no version strings (4.38/4.35/4.33 not found)
- CHANGED Sample extension source (translate branch): all 5 files still HTTP 200 — ALL-origin content_scripts + unvalidated `sidebarAction.show`/`show2` dispatch + zero origin validation in `onMessage` confirme
- CHANGED All binary acquisition channels remain 100% blocked in-sandbox (cloudfront DNS `No answer`, APKMirror 403, Uptodown 404 page removed, pstatic 404)

## 2026-08-09 14:54:14 UTC

## 2026-08-09 15:21:45 UTC
- NEW Binary delivery directory `/tmp/opencode/whale_binary/` still missing (reported created in prior scans but `ls` returns "No such file or directory")
- NEW NVD primary endpoint `nvd.nist.gov/rest` confirmed Cloudflare-blocked; `services.nvd.nist.gov` reachable and returns 0 Whale CVEs in 2026 (2 total, both pre-2021)
- CHANGED Wikipedia infobox version assertion cannot be passively re-verified — HTML parse shows no version strings (4.38/4.35/4.33 not found)
- CHANGED Sample extension source (translate branch): all 5 files still HTTP 200 — ALL-origin content_scripts + unvalidated `sidebarAction.show`/`show2` dispatch + zero origin validation in `onMessage` confirme
- CHANGED All binary acquisition channels remain 100% blocked in-sandbox (cloudfront DNS `No answer`, APKMirror 403, Uptodown 404 page removed, pstatic 404)

## 2026-08-09 15:55:14 UTC
- CHANGED Binary delivery directory `/tmp/opencode/whale_binary/` still missing (reported created in prior scans but `ls` returns "No such file or directory")
- CHANGED NVD primary endpoint `nvd.nist.gov/rest` confirmed Cloudflare-blocked; `services.nvd.nist.gov/rest/json/cves/2.0` remains reachable and returns 0 Whale CVEs in 2026
- CHANGED Wikipedia infobox version assertion cannot be passively re-verified — HTML parse shows no version strings (4.38/4.35/4.33 not found)
- CHANGED Sample extension source (translate branch): all 5 files still HTTP 200 — ALL-origin content_scripts + unvalidated `sidebarAction.show`/`show2` dispatch + zero origin validation in `onMessage` confirme
- CHANGED All binary acquisition channels remain 100% blocked in-sandbox (cloudfront DNS `No answer`, APKMirror 403, Uptodown 404 page removed, pstatic 404)
- NEW uptodown ANDROID page `naver-whale-browser.en.uptodown.com/android` returns HTTP 200 (155,246 B) and live-pins latest com.naver.whale **3.9.14.9** (page title + version history 3.9.14.5/6/7/9) — contr
- NEW uptodown download resolver JS `stc.utdstc.com/1786093238827/download.js` returns **HTTP 410 Gone** — client-side token generator for the dw flow is dead
- NEW `dw.uptodown.com/dwn/1197336657` (with session cookie from the 200 page) → HTTP 400 JSON `{"success":0,"errorCode":-51,"errorMsg":"Bad request"}` — passive token-free APK fetch confirmed blocked; Andr
- CHANGED NVD re-verify @ 15:52:35 UTC via services.nvd.nist.gov: totalResults=2 (CVE-2018-9859, CVE-2020-9754), both pre-2021 — 0 CVEs in 2026, disclosure gap static for v4.35.352–v4.38.386.14 (unchanged)
- CHANGED `/tmp/opencode/whale_binary/` still missing (workspace re-provisioned 15:52 UTC) — binary delivery path unsatisfied, blocking all binary-dependent verification

## 2026-08-09 16:24:22 UTC
- NEW Uptodown Android page `naver-whale-browser.en.uptodown.com/android` returns HTTP 200 (155 KB) and live-pins com.naver.whale **3.9.14.9** — only live Android acquisition channel confirmed
- NEW Uptodown download resolver JS `stc.utdstc.com/1786093238827/download.js` returns HTTP 410 Gone — client-side token generator for `dw` flow dead
- NEW `dw.uptodown.com/dwn/1197336657` (with session cookie) → HTTP 400 `{"success":0,"errorCode":-51}` — passive token-free APK fetch confirmed blocked
- CHANGED Binary delivery directory `/tmp/opencode/whale_binary/` still missing after workspace re-provision at 15:52 UTC — blocks all binary-dependent verification
- CHANGED NVD primary `nvd.nist.gov/rest` Cloudflare-blocked; `services.nvd.nist.gov/rest/json/cves/2.0` returns 0 Whale CVEs in 2026 (2 total, both pre-2021)
