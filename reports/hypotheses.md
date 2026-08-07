# Ranked Hypotheses

## SEED 2026-08-07 (from passive recon, not model-generated yet)
- [55] Whale sync: token handling / data encryption in sync flow — static analysis of whale-sync client code first (from inventory seed)
- [50] Whale-only bundled libs: version drift vs upstream with known CVEs — diff bundled third-party libs against upstream (from inventory seed)
- [45] whale-browser-developers repo: security-relevant disclosures/issues in its issues/discussions (from inventory seed)

## RANKED HYPOTHESES 2026-08-07 18:43:32 UTC
- [58] Whale: Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9) (from reports/hypotheses-bigpickle.txt)
- [45] Whale: Sidebar context SOP bypass — new variant post-CVE-2025-69235 (from reports/hypotheses-laguna.txt)
- NEXT(hypotheses-laguna.txt): [HUMAN]: Install latest Whale browser binary (currently unknown exact version ≥4.35.351.12) locally and test sidebar context SOP isolation — reproduce CVE-2025-
- LEARN: REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope
- LEARN: ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
- LEARN: ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
- LEARN: ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
- LEARN: REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests ava
- LEARN: REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)

## RANKED HYPOTHESES 2026-08-07 18:58:13 UTC
- [55] Whale: Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14 (from reports/hypotheses-laguna.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: Acquire latest Whale desktop installer (non-Naver mirror) and Android XAPK 3.9.14.9; extract and decompile; static grep for sync module strings: "passphr
- NEXT(hypotheses-laguna.txt): [HUMAN]: Obtain Whale browser v4.38.386.14 binary from a non-naver.com source (e.g., official download mirror, third-party archive, or enterprise package) and i
- LEARN: REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope
- LEARN: ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
- LEARN: ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
- LEARN: ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
- LEARN: REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests ava
- LEARN: REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
- LEARN: ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest
- LEARN: ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358, CSP bypass via specific scheme) confirmed fixed in v4.33.325.17 (Oct 2025); latest v4.38.386.14 
- LEARN: CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system meta
- LEARN: CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowled

## RANKED HYPOTHESES 2026-08-07 19:17:01 UTC
- [60] sidebarAction.show: Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14 (from reports/hypotheses-ling3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: Acquire latest Whale desktop installer (non-Naver mirror) and Android XAPK 3.9.14.9; extract and decompile; static grep for sync module strings: "passphr
- NEXT(hypotheses-laguna.txt): [HUMAN]: Obtain Whale browser v4.38.386.14 binary from a non-naver.com source (e.g., official download mirror, third-party archive, or enterprise package) and i
- LEARN: REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope
- LEARN: ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
- LEARN: ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
- LEARN: ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
- LEARN: REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests ava
- LEARN: REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
- LEARN: ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest
- LEARN: ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358, CSP bypass via specific scheme) confirmed fixed in v4.33.325.17 (Oct 2025); latest v4.38.386.14 
- LEARN: CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system meta
- LEARN: CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowled

## RANKED HYPOTHESES 2026-08-07 20:10:31 UTC
- [60] Whale: Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14 (from reports/hypotheses-nemotron3.txt)
- [58] Whale: Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9) (from reports/hypotheses-bigpickle.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: Acquire latest Whale desktop installer (non-Naver mirror) and Android XAPK 3.9.14.9; extract and decompile; static grep for sync module strings: "passphr
- NEXT(hypotheses-bigpickle.txt): PROBE: Acquire latest Whale desktop installer (non-Naver mirror) and Android XAPK 3.9.14.9; extract and decompile; static grep for sync module strings: "passphr
- NEXT(hypotheses-laguna.txt): [HUMAN]: Obtain Whale browser v4.38.386.14 binary from a non-naver.com mirror or local source. Install and load the sidebar-sample extension from the `translate
- LEARN: REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope
- LEARN: ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
- LEARN: ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
- LEARN: ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
- LEARN: REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests ava
- LEARN: REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
- LEARN: REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope
- LEARN: ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
- LEARN: ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
- LEARN: ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
- LEARN: REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests ava
- LEARN: REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
- LEARN: CONFIRMED @ GitHub: naver/whale-browser-developers repo remains documentation-only (last commit 2019-09-23; 2025-10-22 metadata-only refresh) — no source code, 
- LEARN: CONFIRMED @ NVD: 0 CVEs published for Whale in 2026 — no public disclosures for versions 4.35.352 through 4.38.386, confirming a 6-month vulnerability disclosur

## RANKED HYPOTHESES 2026-08-07 20:58:17 UTC
- [60] Whale: Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14 (from reports/hypotheses-nemotron3.txt)
- [58] Whale: Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9) (from reports/hypotheses-bigpickle.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: Acquire latest Whale desktop installer (non-Naver mirror) and Android XAPK 3.9.14.9; extract and decompile; static grep for sync module strings: "passphr
- NEXT(hypotheses-laguna.txt): [PROBE]: Download Whale desktop installer stub (11.6MB) from pstatic.net CDN (identified by bigpickle recon as the browser package source); extract with 7z/unzi
- LEARN: REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope
- LEARN: ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
- LEARN: ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
- LEARN: ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
- LEARN: REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests ava
- LEARN: REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
- LEARN: ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest
- LEARN: ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358, CSP bypass via specific scheme) confirmed fixed in v4.33.325.17 (Oct 2025); latest v4.38.386.14 
- LEARN: CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system meta
- LEARN: CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowled
