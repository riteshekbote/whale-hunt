# LEADS nemotron3 (seed)
- SEED: no model output yet; pipeline starts on first run.
## 2026-08-07 18:30:06 UTC [browser] (model nemotron3)
## 2026-08-07 18:49:54 UTC [browser] (model nemotron3)
[NEW] No new surface items since last aggregated hypotheses (2026-08-07 18:43:32 UTC) — inventory, knowledge, and leads unchanged
[PRIO] Whale browser sidebar environment, 7.15, atk=9 biz=9 tech=7 gate=2 cloud=4 fresh=9 — Whale-specific feature; 3 CVEs in Dec 2025 (CVE-2025-69234/69235 SOP/sandbox); sample code confirms sidebar context detection via userAgent; 8+ months since fix
[PRIO] whale.* extension API surface (runtime/storage/sidebarAction), 6.70, atk=8 biz=8 tech=7 gate=3 cloud=6 fresh=6 — CVE-2022-24072 (devtools JS injection), CVE-2024-40618 (built-in extension XSS); content_scripts match all origins; whale.storage may sync via Whale account
[PRIO] Whale browser dual-tab environment, 6.30, atk=8 biz=8 tech=6 gate=2 cloud=3 fresh=8 — 4 CVEs Jul–Oct 2025 (CVE-2025-53600, 62583, 62584, 62585) for SOP/sandbox/CSP bypass; Whale-specific feature not in Chromium; fixed v4.33.325.17
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 5.80, atk=7 biz=9 tech=6 gate=4 cloud=5 fresh=5 — Vendor docs: passphrase never sent to server; Android sync encryption added 2025-04; no public client code; binary static analysis required
[PRIO] Whale-only bundled third-party libraries, 3.50, atk=5 biz=6 tech=4 gate=5 cloud=2 fresh=3 — No public manifest; historical installer/extension-store CVEs (CVE-2018-12449, CVE-2022-2407x); inventory requires binary extraction
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235
class: OTHER
asset: Whale browser sidebar context (browser-internal, not a Naver web service)
confidence: 45
reasoning: CVE-2025-69235 (CWE-346) fixed in v4.35.351.12 (Dec 2025). Sample extension at translate/src/sidebar-sample/js/contentscript.js confirms sidebar context detection via `navigator.userAgent.includes('sidebar')`. 8+ months since fix; Whale-specific sidebar isolation has recurring SOP issues (3 CVEs in 2025).
evidence_needed: Running browser binary ≥4.35.352 demonstrating cross-origin data access from sidebar context
verify_steps: HUMAN_ONLY: Install latest Whale → open sidebar extension → attempt cross-origin fetch/XMLHttpRequest from sidebar content script to arbitrary origin → confirm if SOP enforced
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration (High)
testability: HUMAN_ONLY
[HYP] Extension API message handling / origin validation bypass
class: XSS
asset: whale.* extension API surface (whale.runtime.onMessage, whale.storage, content_scripts matching http://*/*, https://*/*)
confidence: 40
reasoning: CVE-2022-24072 (CWE-79, devtools API JS injection) and CVE-2024-40618 (improper sanitization in built-in extension) show recurring XSS/injection in extension API. Sample manifest.json declares content_scripts matching ALL origins.
evidence_needed: Extension API documentation or binary analysis revealing unsafe message handling or missing origin validation in whale.runtime
verify_steps: HUMAN_ONLY: Install latest Whale → load test extension with onMessage listener → test message payload with origin-spoofing → check if whale.storage.sync accepts unvalidated input → test content script injection vector
impact: Arbitrary JavaScript execution in extension context → cross-site data access, session theft (High)
testability: HUMAN_ONLY
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: acquire latest desktop installer and Android XAPK 3.9.14.9, extract/decompile, grep sync module for passphrase/scrypt/PBKDF2 constants, key-store paths, sync hostnames, bearer/OAuth token handling. AUTH_HELPED: authorized test login to observe token/key lifecycle in filesystem. Zero requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[PARKED] Extension API message handling / origin validation bypass: confidence 40 meets threshold but verify_steps are HUMAN_ONLY with no passive-first path; requires binary install + extension load which is not passive — deferred until binary available
[FINAL] 1. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED) 2. Sidebar context SOP bypass — new variant post-CVE-2025-69235 (confidence 45, class OTHER, testability HUMAN_ONLY)
[NEXT] PROBE: Acquire latest Whale desktop installer (non-Naver mirror) and Android XAPK 3.9.14.9; extract and decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account; passphrase KDF/key-storage design entirely client-side with no public code; Android sync encryption added 2025-04 (late); moderate-high exposure due to source-unavailable constraint
[RISK] browser: 75 — sidebar + dual-tab environments have 7 confirmed CVEs in 2025 (SOP bypass, iframe sandbox escape, CSP bypass); these are Whale-specific features not inherited from Chromium; active and recently vulnerable attack surface with 8+ months since last fixes; high priority for new variant discovery
[RISK] libs: 30 — Whale bundles Chromium (inherits all Chromium CVEs), but no Whale-only third-party library manifests or version lists are publicly available; the repo contains only translated documentation; library-level version drift cannot be assessed without binary access; low visibility, moderate inherent risk
## 2026-08-07 20:10:20 UTC [browser] (model nemotron3)
[NEW] NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-07 18:43:32 UTC)
[PRIO] Whale browser sidebar environment on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9 — Whale-specific feature; CVE-2025-69234/69235 fixed Dec 2025; 3 minor version bumps with 0 CVEs; wiki confirms sidebarAction.show() loads arbitrary URL + drag-navigation exposure
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 6.10, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7 — Sync explicitly in scope; vendor docs: passphrase never leaves device; Android sync encryption added 2025-04; no public client code; binary static analysis required
[PRIO] Whale browser dual-tab environment on v4.38.386.14, 6.30, atk=8 biz=8 tech=6 gate=2 cloud=3 fresh=8 — 4 CVEs Jul–Oct 2025 (SOP/sandbox/CSP bypass); Whale-specific feature not in Chromium; fixed v4.33.325.17; ~8 months since fix
[PRIO] whale.* extension API surface (runtime/storage/sidebarAction), 6.28, atk=8 biz=7 tech=7 gate=3 cloud=6 fresh=6 — CVE-2022-24072 (devtools JS injection), CVE-2024-40618 (built-in extension XSS); content_scripts match ALL origins; whale.storage may sync via Whale account
[PRIO] Whale-only bundled third-party libraries, 3.50, atk=5 biz=6 tech=4 gate=5 cloud=2 fresh=3 — No public manifest; historical installer/extension-store CVEs; inventory requires binary extraction
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — whale.sidebarAction.show({url}) loads cross-origin content into sidebar panel; v4.38.386.14 (latest stable)
confidence: 60
reasoning: CVE-2025-69235 (CWE-346, SOP bypass) and CVE-2025-69234 (CWE-358, iframe sandbox escape) fixed in v4.35.351.12 (Dec 2025). Current stable v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki docs confirm sidebarAction.show() accepts `url` parameter to load arbitrary URL in panel; `use_navigation_bar=false` creates drag-navigation exposure. Sidebar context detected via `navigator.userAgent.includes('sidebar')` per sample contentscript.js. History of 6 sidebar/dual-tab CVEs in 2025 suggests recurring Whale-specific boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar context — either via show({url:'https://victim.com'}) loading cross-origin page in panel, or via drag-drop navigation bypassing SOP
verify_steps: PASSIVE: Inspect sidebarAction.show URL loading behavior in extension manifest; check `use_navigation_bar` defaults; review wiki "How to avoid my extension from changing urls" mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://httpbin.org/headers'}) → attempt cross-origin fetch from panel content script → test drag-drop navigation with use_navigation_bar:false
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[HYP] Dual-tab environment SOP bypass and CSP scheme bypass — new variant post-CVE-2025-62585 on v4.38.386.14
class: OTHER
asset: Whale browser dual-tab context (Whale-specific feature, not in Chromium) — latest v4.38.386.14
confidence: 45
reasoning: 4 CVEs in Jul–Oct 2025: CVE-2025-53600 (CWE-346 SOP bypass), CVE-2025-62583 (CWE-358 iframe sandbox escape), CVE-2025-62584 (CWE-346 SOP bypass), CVE-2025-62585 (CWE-358 CSP bypass via specific scheme) — all fixed in v4.33.325.17 (Oct 2025). Current stable v4.38.386.14 is ~8 months ahead with 0 published CVEs. Dual-tab is a Whale-specific feature with no Chromium equivalent, recurring boundary issues.
evidence_needed: Running browser binary v4.38.386.14 demonstrating SOP/CSP bypass in dual-tab mode — cross-origin access between dual-tab panels or CSP bypass via javascript:/data: schemes
verify_steps: HUMAN_ONLY: Install Whale v4.38.386.14 → open dual-tab mode → load cross-origin iframes in each tab panel → test cross-origin read between panels → test javascript: and data: scheme CSP bypass in dual-tab context → confirm if isolation enforced
impact: Same-origin policy bypass in dual-tab environment → credential theft, CSRF token exfiltration, potential sandbox escape (Critical if escalates to renderer code execution)
testability: HUMAN_ONLY
[PARKED] Dual-tab environment SOP bypass and CSP scheme bypass — new variant post-CVE-2025-62585 on v4.38.386.14: confidence 45 meets threshold but verify_steps are HUMAN_ONLY with no passive-first path; requires binary install + dual-tab testing which is not passive — deferred until binary available
[FINAL] 1. Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14 (confidence 60, class OTHER, testability HUMAN_ONLY) 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: Acquire latest Whale desktop installer (non-Naver mirror) and Android XAPK 3.9.14.9; extract and decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account; passphrase KDF/key-storage design entirely client-side with no public code; Android sync encryption added 2025-04 (late); moderate-high exposure due to source-unavailable constraint
[RISK] browser: 78 — sidebar + dual-tab environments have 6 confirmed CVEs in 2025 (SOP bypass CWE-346, iframe sandbox escape CWE-358, CSP bypass CWE-358); these are Whale-specific features not inherited from Chromium; latest stable v4.38.386.14 is 3 minor version bumps ahead of last CVE fix (Dec 2025) with ZERO published CVEs in between, creating a 6-month vulnerability disclosure gap; wiki docs confirm additional attack surface (show() URL loading, drag-navigation exposure); high priority for new variant discovery
[RISK] libs: 30 — Whale bundles Chromium (inherits all Chromium CVEs), but no Whale-only third-party library manifests or version lists are publicly available; the repo contains only translated documentation from 2019; the extension API surface (whale.*) uses CWE-79-inherited patterns but library-level version drift cannot be assessed without binary access; low visibility, moderate inherent risk
## 2026-08-07 20:56:51 UTC [browser] (model nemotron3)
[PRIO] Whale browser sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9 — Whale-specific feature; CVE-2025-69234/69235 fixed Dec 2025; 3 minor version bumps with 0 CVEs; wiki confirms sidebarAction.show() loads arbitrary URL + drag-navigation exposure
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 6.10, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7 — Sync explicitly in scope; vendor docs: passphrase never leaves device; Android sync encryption added 2025-04; no public client code; binary static analysis required
[PRIO] Whale browser dual-tab environment on v4.38.386.14, 6.30, atk=8 biz=8 tech=6 gate=2 cloud=3 fresh=8 — 4 CVEs Jul–Oct 2025 (SOP/sandbox/CSP bypass); Whale-specific feature not in Chromium; fixed v4.33.325.17; ~8 months since fix
[PRIO] whale.* extension API surface (runtime/storage/sidebarAction), 6.28, atk=8 biz=7 tech=7 gate=3 cloud=6 fresh=6 — CVE-2022-24072 (devtools JS injection), CVE-2024-40618 (built-in extension XSS); content_scripts match ALL origins; whale.storage may sync via Whale account
[PRIO] Whale-only bundled third-party libraries, 3.50, atk=5 biz=6 tech=4 gate=5 cloud=2 fresh=3 — No public manifest; historical installer/extension-store CVEs; inventory requires binary extraction
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — whale.sidebarAction.show({url}) loads cross-origin content into sidebar panel; v4.38.386.14 (latest stable)
confidence: 60
reasoning: CVE-2025-69235 (CWE-346, SOP bypass) and CVE-2025-69234 (CWE-358, iframe sandbox escape) fixed in v4.35.351.12 (Dec 2025). Current stable v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki docs confirm sidebarAction.show() accepts `url` parameter to load arbitrary URL in panel; `use_navigation_bar=false` creates drag-navigation exposure. Sidebar context detected via `navigator.userAgent.includes('sidebar')` per sample contentscript.js. History of 6 sidebar/dual-tab CVEs in 2025 suggests recurring Whale-specific boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar context — either via show({url:'https://victim.com'}) loading cross-origin page in panel, or via drag-drop navigation bypassing SOP
verify_steps: PASSIVE: Inspect sidebarAction.show URL loading behavior in extension manifest; check `use_navigation_bar` defaults; review wiki "How to avoid my extension from changing urls" mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://httpbin.org/headers'}) → attempt cross-origin fetch from panel content script → test drag-drop navigation with use_navigation_bar:false
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[HYP] Dual-tab environment SOP bypass and CSP scheme bypass — new variant post-CVE-2025-62585 on v4.38.386.14
class: OTHER
asset: Whale browser dual-tab context (Whale-specific feature, not in Chromium) — latest v4.38.386.14
confidence: 45
reasoning: 4 CVEs in Jul–Oct 2025: CVE-2025-53600 (CWE-346 SOP bypass), CVE-2025-62583 (CWE-358 iframe sandbox escape), CVE-2025-62584 (CWE-346 SOP bypass), CVE-2025-62585 (CWE-358 CSP bypass via specific scheme) — all fixed in v4.33.325.17 (Oct 2025). Current stable v4.38.386.14 is ~8 months ahead with 0 published CVEs. Dual-tab is a Whale-specific feature with no Chromium equivalent, recurring boundary issues.
evidence_needed: Running browser binary v4.38.386.14 demonstrating SOP/CSP bypass in dual-tab mode — cross-origin access between dual-tab panels or CSP bypass via javascript:/data: schemes
verify_steps: HUMAN_ONLY: Install Whale v4.38.386.14 → open dual-tab mode → load cross-origin iframes in each tab panel → test cross-origin read between panels → test javascript: and data: scheme CSP bypass in dual-tab context → confirm if isolation enforced
impact: Same-origin policy bypass in dual-tab environment → credential theft, CSRF token exfiltration, potential sandbox escape (Critical if escalates to renderer code execution)
testability: HUMAN_ONLY
[PARKED] Dual-tab environment SOP bypass and CSP scheme bypass — new variant post-CVE-2025-62585 on v4.38.386.14: confidence 45 meets threshold but verify_steps are HUMAN_ONLY with no passive-first path; requires binary install + dual-tab testing which is not passive — deferred until binary available
[FINAL] 1. Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14 (confidence 60, class OTHER, testability HUMAN_ONLY) 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: Acquire latest Whale desktop installer (non-Naver mirror) and Android XAPK 3.9.14.9; extract and decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest stable v4.38.386.14 has 3 minor version bumps with zero published CVEs — regression or new variant possible
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358, CSP bypass via specific scheme) confirmed fixed in v4.33.325.17 (Oct 2025); latest v4.38.386.14 is ~8 months ahead, no new CVEs published
[LEARN] CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system metadata refresh, no code changes
[LEARN] CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowledge gap for current-version vulnerability discovery
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account; passphrase KDF/key-storage design entirely client-side with no public code; Android sync encryption added 2025-04 (late); moderate-high exposure due to source-unavailable constraint
[RISK] browser: 78 — sidebar + dual-tab environments have 6 confirmed CVEs in 2025 (SOP bypass CWE-346, iframe sandbox escape CWE-358, CSP bypass CWE-358); these are Whale-specific features not inherited from Chromium; latest stable v4.38.386.14 is 3 minor version bumps ahead of last CVE-fix v4.35.351.12 with ZERO published CVEs in 6-month gap; wiki docs confirm additional attack surface (show() URL loading, drag-navigation exposure); high priority for new variant discovery
[RISK] libs: 30 — Whale bundles Chromium (inherits all Chromium CVEs), but no Whale-only third-party library manifests or version lists are publicly available; the repo contains only translated documentation from 2019; the extension API surface (whale.*) uses CWE-79-inherited patterns but library-level version drift cannot be assessed without binary access; low visibility, moderate inherent risk
## 2026-08-07 21:40:58 UTC [browser] (model nemotron3)
[NEW] Current timestamp 2026-08-07 21:39:04 UTC — ~41 minutes since last inventory aggregation (2026-08-07 20:58:17 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context
[PRIO] Whale browser sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 6.10, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7
[PRIO] Whale browser dual-tab environment on v4.38.386.14, 6.30, atk=8 biz=8 tech=6 gate=2 cloud=3 fresh=8
[PRIO] whale.* extension API surface (runtime/storage/sidebarAction), 6.28, atk=8 biz=7 tech=7 gate=3 cloud=6 fresh=6
[PRIO] Whale-only bundled third-party libraries, 3.50, atk=5 biz=6 tech=4 gate=5 cloud=2 fresh=3
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — whale.sidebarAction.show({url}) loads cross-origin content into sidebar panel; v4.38.386.14 (latest stable)
confidence: 60
reasoning: CVE-2025-69235 (CWE-346, SOP bypass) and CVE-2025-69234 (CWE-358, iframe sandbox escape) fixed in v4.35.351.12 (Dec 2025). Current stable v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki docs confirm sidebarAction.show() accepts `url` parameter to load arbitrary URL in panel; `use_navigation_bar=false` creates drag-navigation exposure. Sidebar context detected via `navigator.userAgent.includes('sidebar')` per sample contentscript.js. History of 6 sidebar/dual-tab CVEs in 2025 suggests recurring Whale-specific boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar context — either via show({url:'https://victim.com'}) loading cross-origin page in panel, or via drag-drop navigation bypassing SOP
verify_steps: PASSIVE: Inspect sidebarAction.show URL loading behavior in extension manifest; check `use_navigation_bar` defaults; review wiki "How to avoid my extension from changing urls" mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://httpbin.org/headers'}) → attempt cross-origin fetch from panel content script → test drag-drop navigation with use_navigation_bar:false
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[HYP] Dual-tab environment SOP bypass and CSP scheme bypass — new variant post-CVE-2025-62585 on v4.38.386.14
class: OTHER
asset: Whale browser dual-tab context (Whale-specific feature, not in Chromium) — latest v4.38.386.14
confidence: 45
reasoning: 4 CVEs in Jul–Oct 2025: CVE-2025-53600 (CWE-346 SOP bypass), CVE-2025-62583 (CWE-358 iframe sandbox escape), CVE-2025-62584 (CWE-346 SOP bypass), CVE-2025-62585 (CWE-358 CSP bypass via specific scheme) — all fixed in v4.33.325.17 (Oct 2025). Current stable v4.38.386.14 is ~8 months ahead with 0 published CVEs. Dual-tab is a Whale-specific feature with no Chromium equivalent, recurring boundary issues.
evidence_needed: Running browser binary v4.38.386.14 demonstrating SOP/CSP bypass in dual-tab mode — cross-origin access between dual-tab panels or CSP bypass via javascript:/data: schemes
verify_steps: HUMAN_ONLY: Install Whale v4.38.386.14 → open dual-tab mode → load cross-origin iframes in each tab panel → test cross-origin read between panels → test javascript: and data: scheme CSP bypass in dual-tab context → confirm if isolation enforced
impact: Same-origin policy bypass in dual-tab environment → credential theft, CSRF token exfiltration, potential sandbox escape (Critical if escalates to renderer code execution)
testability: HUMAN_ONLY
[PARKED] Dual-tab environment SOP bypass and CSP scheme bypass — new variant post-CVE-2025-62585 on v4.38.386.14: confidence 45 meets threshold but verify_steps are HUMAN_ONLY with no passive-first path; requires binary install + dual-tab testing which is not passive — deferred until binary available
[FINAL] 1. Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14 (confidence 60, class OTHER, testability HUMAN_ONLY) 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: Acquire latest Whale desktop installer (non-Naver mirror) and Android XAPK 3.9.14.9; extract and decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest stable v4.38.386.14 has 3 minor version bumps with zero published CVEs — regression or new variant possible
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358, CSP bypass via specific scheme) confirmed fixed in v4.33.325.17 (Oct 2025); latest v4.38.386.14 is ~8 months ahead, no new CVEs published
[LEARN] CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system metadata refresh, no code changes
[LEARN] CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowledge gap for current-version vulnerability discovery
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account; passphrase KDF/key-storage design entirely client-side with no public code; Android sync encryption added 2025-04 (late); moderate-high exposure due to source-unavailable constraint
[RISK] browser: 78 — sidebar + dual-tab environments have 6 confirmed CVEs in 2025 (SOP bypass CWE-346, iframe sandbox escape CWE-358, CSP bypass CWE-358); these are Whale-specific features not inherited from Chromium; latest stable v4.38.386.14 is 3 minor version bumps ahead of last CVE-fix v4.35.351.12 with ZERO published CVEs in 6-month gap; wiki docs confirm additional attack surface (show() URL loading, drag-navigation exposure); high priority for new variant discovery
[RISK] libs: 30 — Whale bundles Chromium (inherits all Chromium CVEs), but no Whale-only third-party library manifests or version lists are publicly available; the repo contains only translated documentation from 2019; the extension API surface (whale.*) uses CWE-79-inherited patterns but library-level version drift cannot be assessed without binary access; low visibility, moderate inherent risk
## 2026-08-07 22:19:55 UTC [browser] (model nemotron3)
[NEW] Current timestamp 2026-08-07 22:18:21 UTC — ~80 minutes since last inventory aggregation (2026-08-07 20:58:17 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context
[PRIO] Whale browser sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 6.10, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7
[PRIO] Whale browser dual-tab environment on v4.38.386.14, 6.30, atk=8 biz=8 tech=6 gate=2 cloud=3 fresh=8
[PRIO] whale.* extension API surface (runtime/storage/sidebarAction), 6.28, atk=8 biz=7 tech=7 gate=3 cloud=6 fresh=6
[PRIO] Whale-only bundled third-party libraries, 3.50, atk=5 biz=6 tech=4 gate=5 cloud=2 fresh=3
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — whale.sidebarAction.show({url}) loads cross-origin content into sidebar panel; v4.38.386.14 (latest stable)
confidence: 60
reasoning: CVE-2025-69235 (CWE-346, SOP bypass) and CVE-2025-69234 (CWE-358, iframe sandbox escape) fixed in v4.35.351.12 (Dec 2025). Current stable v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki docs confirm sidebarAction.show() accepts `url` parameter to load arbitrary URL in panel; `use_navigation_bar=false` creates drag-navigation exposure. Sidebar context detected via `navigator.userAgent.includes('sidebar')` per sample contentscript.js. History of 6 sidebar/dual-tab CVEs in 2025 suggests recurring Whale-specific boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar context — either via show({url:'https://victim.com'}) loading cross-origin page in panel, or via drag-drop navigation bypassing SOP
verify_steps: PASSIVE: Inspect sidebarAction.show URL loading behavior in extension manifest; check `use_navigation_bar` defaults; review wiki "How to avoid my extension from changing urls" mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://httpbin.org/headers'}) → attempt cross-origin fetch from panel content script → test drag-drop navigation with use_navigation_bar:false
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[HYP] Dual-tab environment SOP bypass and CSP scheme bypass — new variant post-CVE-2025-62585 on v4.38.386.14
class: OTHER
asset: Whale browser dual-tab context (Whale-specific feature, not in Chromium) — latest v4.38.386.14
confidence: 45
reasoning: 4 CVEs in Jul–Oct 2025: CVE-2025-53600 (CWE-346 SOP bypass), CVE-2025-62583 (CWE-358 iframe sandbox escape), CVE-2025-62584 (CWE-346 SOP bypass), CVE-2025-62585 (CWE-358 CSP bypass via specific scheme) — all fixed in v4.33.325.17 (Oct 2025). Current stable v4.38.386.14 is ~8 months ahead with 0 published CVEs. Dual-tab is a Whale-specific feature with no Chromium equivalent, recurring boundary issues.
evidence_needed: Running browser binary v4.38.386.14 demonstrating SOP/CSP bypass in dual-tab mode — cross-origin access between dual-tab panels or CSP bypass via javascript:/data: schemes
verify_steps: HUMAN_ONLY: Install Whale v4.38.386.14 → open dual-tab mode → load cross-origin iframes in each tab panel → test cross-origin read between panels → test javascript: and data: scheme CSP bypass in dual-tab context → confirm if isolation enforced
impact: Same-origin policy bypass in dual-tab environment → credential theft, CSRF token exfiltration, potential sandbox escape (Critical if escalates to renderer code execution)
testability: HUMAN_ONLY
[PARKED] Dual-tab environment SOP bypass and CSP scheme bypass — new variant post-CVE-2025-62585 on v4.38.386.14: confidence 45 meets threshold but verify_steps are HUMAN_ONLY with no passive-first path; requires binary install + dual-tab testing which is not passive — deferred until binary available
[FINAL] 1. Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14 (confidence 60, class OTHER, testability HUMAN_ONLY) 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: Acquire latest Whale desktop installer (non-Naver mirror) and Android XAPK 3.9.14.9; extract and decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest stable v4.38.386.14 has 3 minor version bumps with zero published CVEs — regression or new variant possible
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358, CSP bypass via specific scheme) confirmed fixed in v4.33.325.17 (Oct 2025); latest v4.38.386.14 is ~8 months ahead, no new CVEs published
[LEARN] CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system metadata refresh, no code changes
[LEARN] CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowledge gap for current-version vulnerability discovery
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account; passphrase KDF/key-storage design entirely client-side with no public code; Android sync encryption added 2025-04 (late); moderate-high exposure due to source-unavailable constraint
[RISK] browser: 78 — sidebar + dual-tab environments have 6 confirmed CVEs in 2025 (SOP bypass CWE-346, iframe sandbox escape CWE-358, CSP bypass CWE-358); these are Whale-specific features not inherited from Chromium; latest stable v4.38.386.14 is 3 minor version bumps ahead of last CVE-fix v4.35.351.12 with ZERO published CVEs in 6-month gap; wiki docs confirm additional attack surface (show() URL loading, drag-navigation exposure); high priority for new variant discovery
[RISK] libs: 30 — Whale bundles Chromium (inherits all Chromium CVEs), but no Whale-only third-party library manifests or version lists are publicly available; the repo contains only translated documentation from 2019; the extension API surface (whale.*) uses CWE-79-inherited patterns but library-level version drift cannot be assessed without binary access; low visibility, moderate inherent risk
## 2026-08-07 23:06:51 UTC [browser] (model nemotron3)
[PRIO] Whale browser sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9 — Whale-specific feature; CVE-2025-69234/69235 fixed Dec 2025; 3 minor version bumps with 0 CVEs; wiki confirms sidebarAction.show() loads arbitrary URL + drag-navigation exposure
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 6.10, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7 — Sync explicitly in scope; vendor docs: passphrase never leaves device; Android sync encryption added 2025-04; no public client code; binary static analysis required
[PRIO] Sync bootstrap-token storage / per-account migration — Whale-only prefs deviation, 5.88, atk=7 biz=8 tech=8 gate=2 cloud=3 fresh=8 — Binary strings show custom whalesync engine with prefs keys absent from upstream Chromium (sync.encryption_bootstrap_token_per_account, whale_need_encryption_key_forced_time, _migration_done); local profile access yields full sync decryption
[PRIO] Whale browser dual-tab environment on v4.38.386.14, 6.30, atk=8 biz=8 tech=6 gate=2 cloud=3 fresh=8 — 4 CVEs Jul–Oct 2025 (SOP/sandbox/CSP bypass); Whale-specific feature not in Chromium; fixed v4.33.325.17; ~8 months since fix
[PRIO] whale.* extension API surface (runtime/storage/sidebarAction), 6.28, atk=8 biz=7 tech=7 gate=3 cloud=6 fresh=6 — CVE-2022-24072 (devtools JS injection), CVE-2024-40618 (built-in extension XSS); content_scripts match ALL origins; whale.storage may sync via Whale account
[PRIO] whale_sync_push socket.io push channel — unsanitized event → engine mutation, 5.50, atk=7 biz=7 tech=8 gate=2 cloud=5 fresh=5 — CVE-2022-24072/40618 prove built-in-extension injection vector; push channel is Whale-only socket.io feeding tab/typedUrls sync surfaces
[PRIO] Whale-only bundled third-party libraries, 3.50, atk=5 biz=6 tech=4 gate=5 cloud=2 fresh=3 — No public manifest; historical installer/extension-store CVEs; inventory requires binary extraction
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — whale.sidebarAction.show({url}) loads cross-origin content into sidebar panel; v4.38.386.14 (latest stable)
confidence: 60
reasoning: CVE-2025-69235 (CWE-346, SOP bypass) and CVE-2025-69234 (CWE-358, iframe sandbox escape) fixed in v4.35.351.12 (Dec 2025). Current stable v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki docs confirm sidebarAction.show() accepts `url` parameter to load arbitrary URL in panel; `use_navigation_bar=false` creates drag-navigation exposure. Sidebar context detected via `navigator.userAgent.includes('sidebar')` per sample contentscript.js. History of 6 sidebar/dual-tab CVEs in 2025 suggests recurring Whale-specific boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar context — either via show({url:'https://victim.com'}) loading cross-origin page in panel, or via drag-drop navigation bypassing SOP
verify_steps: PASSIVE: Inspect sidebarAction.show URL loading behavior in extension manifest; check `use_navigation_bar` defaults; review wiki "How to avoid my extension from changing urls" mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://httpbin.org/headers'}) → attempt cross-origin fetch from panel content script → test drag-drop navigation with use_navigation_bar:false
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for [0m
[PRIO] Whale browser sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9 — Whale-specific feature; CVE-2025-69234/69235 fixed Dec 2025; 3 minor version bumps with 0 CVEs; wiki confirms sidebarAction.show() loads arbitrary URL + drag-navigation exposure
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 6.10, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7 — Sync explicitly in scope; vendor docs: passphrase never leaves device; Android sync encryption added 2025-04; no public client code; binary static analysis required
[PRIO] Sync bootstrap-token storage / per-account migration — Whale-only prefs deviation, 5.88, atk=7 biz=8 tech=8 gate=2 cloud=3 fresh=8 — Binary strings show custom whalesync engine with prefs keys absent from upstream Chromium (sync.encryption_bootstrap_token_per_account, whale_need_encryption_key_forced_time, _migration_done); local profile access yields full sync decryption
[PRIO] Whale browser dual-tab environment on v4.38.386.14, 6.30, atk=8 biz=8 tech=6 gate=2 cloud=3 fresh=8 — 4 CVEs Jul–Oct 2025 (SOP/sandbox/CSP bypass); Whale-specific feature not in Chromium; fixed v4.33.325.17; ~8 months since fix
[PRIO] whale.* extension API surface (runtime/storage/sidebarAction), 6.28, atk=8 biz=7 tech=7 gate=3 cloud=6 fresh=6 — CVE-2022-24072 (devtools JS injection), CVE-2024-40618 (built-in extension XSS); content_scripts match ALL origins; whale.storage may sync via Whale account
[PRIO] whale_sync_push socket.io push channel — unsanitized event → engine mutation, 5.50, atk=7 biz=7 tech=8 gate=2 cloud=5 fresh=5 — CVE-2022-24072/40618 prove built-in-extension injection vector; push channel is Whale-only socket.io feeding tab/typedUrls sync surfaces
[PRIO] Whale-only bundled third-party libraries, 3.50, atk=5 biz=6 tech=4 gate=5 cloud=2 fresh=3 — No public manifest; historical installer/extension-store CVEs; inventory requires binary extraction
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — whale.sidebarAction.show({url}) loads cross-origin content into sidebar panel; v4.38.386.14 (latest stable)
confidence: 60
reasoning: CVE-2025-69235 (CWE-346, SOP bypass) and CVE-2025-69234 (CWE-358, iframe sandbox escape) fixed in v4.35.351.12 (Dec 2025). Current stable v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki docs confirm sidebarAction.show() accepts `url` parameter to load arbitrary URL in panel; `use_navigation_bar=false` creates drag-navigation exposure. Sidebar context detected via `navigator.userAgent.includes('sidebar')` per sample contentscript.js. History of 6 sidebar/dual-tab CVEs in 2025 suggests recurring Whale-specific boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar context — either via show({url:'https://victim.com'}) loading cross-origin page in panel, or via drag-drop navigation bypassing SOP
verify_steps: PASSIVE: Inspect sidebarAction.show URL loading behavior in extension manifest; check `use_navigation_bar` defaults; review wiki "How to avoid my extension from changing urls" mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://httpbin.org/headers'}) → attempt cross-origin fetch from panel content script → test drag-drop navigation with use_navigation_bar:false
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[HYP] Sync bootstrap-token storage / per-account migration — Whale-only prefs deviation
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs `sync.encryption_bootstrap_token_per_account`
confidence: 55
reasoning: Binary v4.38.386.14 strings show Whale syncs passwords+cookies+autofill+tabs over custom `/whalesync`; encryption uses `nigori-key` + `sync_pb.EncryptionKeys` with Whale-only prefs keys (`_per_account`, `_migration_done`, `whale_need_encryption_key_forced_time`) absent from upstream Chromium; passphrase help page and `SyncSetupSetEncryptionPassphrase` UI strings confirm custom passphrase is offered.
evidence_needed: Whether per-account token is stored encrypted vs plaintext in `Preferences`, passphrase KDF constants (salt/iterations) in whale_sync_util, whether `whale_need_encryption_key_forced_time` downgrades encryption
verify_steps: PASSIVE: ghidra/strings on `whale_sync_util.cc`/`whale_sync_auth_manager.cc` call sites for `/whalesync/reset` (method+body) and bootstrap-token envelope; compare prefs key set against upstream Chromium `sync.encryption_bootstrap_token` handling — zero network requests
impact: Local attacker / infostealer with profile access decrypts synced passwords, cookies, bookmarks → full-account compromise (High)
testability: PASSIVE
[PARKED] Dual-tab environment SOP bypass and CSP scheme bypass — new variant post-CVE-2025-62585 on v4.38.386.14: confidence 45 meets threshold but verify_steps are HUMAN_ONLY with no passive-first path; requires binary install + dual-tab testing which is not passive — deferred until binary available
[PARKED] whale_sync_push socket.io push channel — unsanitized event → engine mutation: confidence 45 meets threshold but verify_steps require extracting resources.pak from installed binary (PASSIVE step blocked on binary availability) — deferred
[FINAL] 1. Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14 (confidence 60, class OTHER, testability HUMAN_ONLY) 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED) 3. Sync bootstrap-token storage / per-account migration — Whale-only prefs deviation (confidence 55, class AUTH, testability PASSIVE)
[NEXT] PROBE: Acquire latest Whale desktop installer (non-Naver mirror) and Android XAPK 3.9.14.9; extract and decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest stable v4.38.386.14 has 3 minor version bumps with zero published CVEs — regression or new variant possible
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358, CSP bypass via specific scheme) confirmed fixed in v4.33.325.17 (Oct 2025); latest v4.38.386.14 is ~8 months ahead, no new CVEs published
[LEARN] CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system metadata refresh, no code changes
[LEARN] CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowledge gap for current-version vulnerability discovery
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account; passphrase KDF/key-storage design entirely client-side with no public code; Android sync encryption added 2025-04 (late); binary strings reveal Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account`, `whale_need_encryption_key_forced_time`, `_migration_done`) absent from upstream; moderate-high exposure due to source-unavailable constraint
[RISK] browser: 78 — sidebar + dual-tab environments have 6 confirmed CVEs in 2025 (SOP bypass CWE-346, iframe sandbox escape CWE-358, CSP bypass CWE-358); these are Whale-specific features not inherited from Chromium; latest stable v4.38.386.14 is 3 minor version bumps ahead of last CVE-fix v4.35.351.12 with ZERO published CVEs in 6-month gap; wiki docs confirm additional attack surface (show() URL loading, drag-navigation exposure, DevTools-in-sidebar added v4.38.386.12); high priority for new variant discovery
[RISK] libs: 30 — Whale bundles Chromium (inherits all Chromium CVEs), but no Whale-only third-party library manifests or version lists are publicly available; the repo contains only translated documentation from 2019; the extension API surface (whale.*) uses CWE-79-inherited patterns but library-level version drift cannot be assessed without binary access; low visibility, moderate inherent risk
## 2026-08-07 23:43:09 UTC [browser] (model nemotron3)
[NEW] 2026-08-07 23:41:48 UTC — ~34 minutes since last inventory aggregation (2026-08-07 23:07:05 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context
[PRIO] Whale browser sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9 — Whale-specific feature; CVE-2025-69234/69235 fixed Dec 2025; 3 minor version bumps with 0 CVEs; wiki confirms sidebarAction.show() loads arbitrary URL + drag-navigation exposure
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 6.10, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7 — Sync explicitly in scope; vendor docs: passphrase never leaves device; Android sync encryption added 2025-04; no public client code; binary static analysis required
[PRIO] Sync bootstrap-token storage / per-account migration — Whale-only prefs deviation, 5.88, atk=7 biz=8 tech=8 gate=2 cloud=3 fresh=8 — Binary strings show custom whalesync engine with prefs keys absent from upstream Chromium (sync.encryption_bootstrap_token_per_account, whale_need_encryption_key_forced_time, _migration_done); local profile access yields full sync decryption
[PRIO] Whale browser dual-tab environment on v4.38.386.14, 6.30, atk=8 biz=8 tech=6 gate=2 cloud=3 fresh=8 — 4 CVEs Jul–Oct 2025 (SOP/sandbox/CSP bypass); Whale-specific feature not in Chromium; fixed v4.33.325.17; ~8 months since fix
[PRIO] whale.* extension API surface (runtime/storage/sidebarAction), 6.28, atk=8 biz=7 tech=7 gate=3 cloud=6 fresh=6 — CVE-2022-24072 (devtools JS injection), CVE-2024-40618 (built-in extension XSS); content_scripts match ALL origins; whale.storage may sync via Whale account
[PRIO] whale_sync_push socket.io push channel — unsanitized event → engine mutation, 5.50, atk=7 biz=7 tech=8 gate=2 cloud=5 fresh=5 — CVE-2022-24072/40618 prove built-in-extension injection vector; push channel is Whale-only socket.io feeding tab/typedUrls sync surfaces
[PRIO] Whale-only bundled third-party libraries, 3.50, atk=5 biz=6 tech=4 gate=5 cloud=2 fresh=3 — No public manifest; historical installer/extension-store CVEs; inventory requires binary extraction
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — whale.sidebarAction.show({url}) loads cross-origin content into sidebar panel; v4.38.386.14 (latest stable)
confidence: 60
reasoning: CVE-2025-69235 (CWE-346, SOP bypass) and CVE-2025-69234 (CWE-358, iframe sandbox escape) fixed in v4.35.351.12 (Dec 2025). Current stable v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki docs confirm sidebarAction.show() accepts `url` parameter to load arbitrary URL in panel; `use_navigation_bar=false` creates drag-navigation exposure. Sidebar context detected via `navigator.userAgent.includes('sidebar')` per sample contentscript.js. History of 6 sidebar/dual-tab CVEs in 2025 suggests recurring Whale-specific boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar context — either via show({url:'https://victim.com'}) loading cross-origin page in panel, or via drag-drop navigation bypassing SOP
verify_steps: PASSIVE: Inspect sidebarAction.show URL loading behavior in extension manifest; check `use_navigation_bar` defaults; review wiki "How to avoid my extension from changing urls" mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://httpbin.org/headers'}) → attempt cross-origin fetch from panel content script → test drag-drop navigation with use_navigation_bar:false
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[HYP] Sync bootstrap-token storage / per-account migration — Whale-only prefs deviation
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs `sync.encryption_bootstrap_token_per_account`
confidence: 55
reasoning: Binary v4.38.386.14 strings show Whale syncs passwords+cookies+autofill+tabs over custom `/whalesync`; encryption uses `nigori-key` + `sync_pb.EncryptionKeys` with Whale-only prefs keys (`_per_account`, `_migration_done`, `whale_need_encryption_key_forced_time`) absent from upstream Chromium; passphrase help page and `SyncSetupSetEncryptionPassphrase` UI strings confirm custom passphrase is offered.
evidence_needed: Whether per-account token is stored encrypted vs plaintext in `Preferences`, passphrase KDF constants (salt/iterations) in whale_sync_util, whether `whale_need_encryption_key_forced_time` downgrades encryption
verify_steps: PASSIVE: ghidra/strings on `whale_sync_util.cc`/`whale_sync_auth_manager.cc` call sites for `/whalesync/reset` (method+body) and bootstrap-token envelope; compare prefs key set against upstream Chromium `sync.encryption_bootstrap_token` handling — zero network requests
impact: Local attacker / infostealer with profile access decrypts synced passwords, cookies, bookmarks → full-account compromise (High)
testability: PASSIVE
[PARKED] Dual-tab environment SOP bypass and CSP scheme bypass — new variant post-CVE-2025-62585 on v4.38.386.14: confidence 45 meets threshold but verify_steps are HUMAN_ONLY with no passive-first path; requires binary install + dual-tab testing which is not passive — deferred until binary available
[PARKED] whale_sync_push socket.io push channel — unsanitized event → engine mutation: confidence 45 meets threshold but verify_steps require extracting resources.pak from installed binary (PASSIVE step blocked on binary availability) — deferred
[FINAL] 1. Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14 (confidence 60, class OTHER, testability HUMAN_ONLY) 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED) 3. Sync bootstrap-token storage / per-account migration — Whale-only prefs deviation (confidence 55, class AUTH, testability PASSIVE)
[NEXT] PROBE: Acquire latest Whale desktop installer (non-Naver mirror) and Android XAPK 3.9.14.9; extract and decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest stable v4.38.386.14 has 3 minor version bumps with zero published CVEs — regression or new variant possible
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358, CSP bypass via specific scheme) confirmed fixed in v4.33.325.17 (Oct 2025); latest v4.38.386.14 is ~8 months ahead, no new CVEs published
[LEARN] CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system metadata refresh, no code changes
[LEARN] CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowledge gap for current-version vulnerability discovery
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account; passphrase KDF/key-storage design entirely client-side with no public code; Android sync encryption added 2025-04 (late); binary strings reveal Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account`, `whale_need_encryption_key_forced_time`, `_migration_done`) absent from upstream; moderate-high exposure due to source-unavailable constraint
[RISK] browser: 78 — sidebar + dual-tab environments have 6 confirmed CVEs in 2025 (SOP bypass CWE-346, iframe sandbox escape CWE-358, CSP bypass CWE-358); these are Whale-specific features not inherited from Chromium; latest stable v4.38.386.14 is 3 minor version bumps ahead of last CVE-fix v4.35.351.12 with ZERO published CVEs in 6-month gap; wiki docs confirm additional attack surface (show() URL loading, drag-navigation exposure, DevTools-in-sidebar added v4.38.386.12); high priority for new variant discovery
[RISK] libs: 30 — Whale bundles Chromium (inherits all Chromium CVEs), but no Whale-only third-party library manifests or version lists are publicly available; the repo contains only translated documentation from 2019; the extension API surface (whale.*) uses CWE-79-inherited patterns but library-level version drift cannot be assessed without binary access; low visibility, moderate inherent risk
## 2026-08-08 00:36:48 UTC [browser] (model nemotron3)
[NEW] 2026-08-08 00:35:12 UTC — ~45 minutes since last inventory aggregation (2026-08-07 23:49:41 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context
[PRIO] Whale browser sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9 — Whale-specific feature; CVE-2025-69234/69235 fixed Dec 2025; 3 minor version bumps with 0 CVEs; wiki confirms sidebarAction.show() loads arbitrary URL + drag-navigation exposure
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 6.10, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7 — Sync explicitly in scope; vendor docs: passphrase never leaves device; Android sync encryption added 2025-04; no public client code; binary static analysis required
[PRIO] Sync bootstrap-token storage / per-account migration — Whale-only prefs deviation, 5.88, atk=7 biz=8 tech=8 gate=2 cloud=3 fresh=8 — Binary strings show custom whalesync engine with prefs keys absent from upstream Chromium (sync.encryption_bootstrap_token_per_account, whale_need_encryption_key_forced_time, _migration_done); local profile access yields full sync decryption
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — whale.sidebarAction.show({url}) loads cross-origin content into sidebar panel; v4.38.386.14 (latest stable)
confidence: 60
reasoning: CVE-2025-69235 (CWE-346, SOP bypass) and CVE-2025-69234 (CWE-358, iframe sandbox escape) fixed in v4.35.351.12 (Dec 2025). Current stable v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki docs confirm sidebarAction.show() accepts `url` parameter to load arbitrary URL in panel; `use_navigation_bar=false` creates drag-navigation exposure. Sidebar context detected via `navigator.userAgent.includes('sidebar')` per sample contentscript.js. History of 6 sidebar/dual-tab CVEs in 2025 suggests recurring Whale-specific boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar context — either via show({url:'https://victim.com'}) loading cross-origin page in panel, or via drag-drop navigation bypassing SOP
verify_steps: PASSIVE: Inspect sidebarAction.show URL loading behavior in extension manifest; check `use_navigation_bar` defaults; review wiki "How to avoid my extension from changing urls" mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://httpbin.org/headers'}) → attempt cross-origin fetch from panel content script → test drag-drop navigation with use_navigation_bar:false
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs `sync.encryption_bootstrap_token_per_account`
confidence: 60
reasoning: Binary strings confirm Whale-only prefs keys + Whale-forked OSCrypt (os_crypt_whale.cc, wbc_wrapper_apis.cc, `''xv10` magic); /whalesync authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list; per-account bootstrap token envelope deviates from upstream Chromium sync.encryption_bootstrap_token.
evidence_needed: Per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where os_crypt_whale stores master key on Linux; whether whale_need_encryption_key_forced_time downgrades to a stale key
verify_steps: PASSIVE: objdump/strings on os_crypt_whale + whale_sync_util call sites for the bootstrap-token envelope and /whalesync/reset request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: Local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise; High
testability: PASSIVE
[PARKED] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14: confidence 60 but testability HUMAN_ONLY with no passive-first verification path — requires binary install and manual sidebar interaction, cannot be validated statically
[PARKED] Whale sync passphrase KDF weakness / plaintext-adjacent key storage: confidence 58 but verify_steps require AUTH_HELPED (authorized test login) and binary acquisition which is not passive-first
[FINAL] 1. Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux (confidence 60, class AUTH, testability PASSIVE) 2. Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14 (confidence 60, class OTHER, testability HUMAN_ONLY) 3. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: Acquire latest Whale desktop installer (non-Naver mirror) and Android XAPK 3.9.14.9; extract and decompile; static grep for sync module strings in os_crypt_whale, whale_sync_util, whale_sync_auth_manager: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences", "xv10", "bootstrap_token" — zero network requests to naver infra
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account; passphrase KDF/key-storage design entirely client-side with no public code; Android sync encryption added 2025-04 (late); binary strings reveal Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account`, `whale_need_encryption_key_forced_time`, `_migration_done`) absent from upstream; moderate-high exposure due to source-unavailable constraint
[RISK] browser: 78 — sidebar + dual-tab environments have 6 confirmed CVEs in 2025 (SOP bypass CWE-346, iframe sandbox escape CWE-358, CSP bypass CWE-358); these are Whale-specific features not inherited from Chromium; latest stable v4.38.386.14 is 3 minor version bumps ahead of last CVE-fix v4.35.351.12 with ZERO published CVEs in 6-month gap; wiki docs confirm additional attack surface (show() URL loading, drag-navigation exposure, DevTools-in-sidebar added v4.38.386.12); high priority for new variant discovery
[RISK] libs: 30 — Whale bundles Chromium (inherits all Chromium CVEs), but no Whale-only third-party library manifests or version lists are publicly available; the repo contains only translated documentation from 2019; the extension API surface (whale.*) uses CWE-79-inherited patterns but library-level version drift cannot be assessed without binary access; low visibility, moderate inherent risk
## 2026-08-08 02:39:12 UTC [browser] (model nemotron3)
[NEW] 2026-08-08 00:35:12 UTC — ~45 minutes since last inventory aggregation (2026-08-07 23:49:41 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context
[PRIO] Whale browser sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9 — Whale-specific feature; CVE-2025-69234/69235 fixed Dec 2025; 3 minor version bumps with 0 CVEs; wiki confirms sidebarAction.show() loads arbitrary URL + drag-navigation exposure
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 6.10, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7 — Sync explicitly in scope; vendor docs: passphrase never leaves device; Android sync encryption added 2025-04; no public client code; binary static analysis required
[PRIO] Sync bootstrap-token storage / per-account migration — Whale-only prefs deviation, 5.88, atk=7 biz=8 tech=8 gate=2 cloud=3 fresh=8 — Binary strings show custom whalesync engine with prefs keys absent from upstream Chromium (sync.encryption_bootstrap_token_per_account, whale_need_encryption_key_forced_time, _migration_done); local profile access yields full sync decryption
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — whale.sidebarAction.show({url}) loads cross-origin content into sidebar panel; v4.38.386.14 (latest stable)
confidence: 60
reasoning: CVE-2025-69235 (CWE-346, SOP bypass) and CVE-2025-69234 (CWE-358, iframe sandbox escape) fixed in v4.35.351.12 (Dec 2025). Current stable v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki docs confirm sidebarAction.show() accepts `url` parameter to load arbitrary URL in panel; `use_navigation_bar=false` creates drag-navigation exposure. Sidebar context detected via `navigator.userAgent.includes('sidebar')` per sample contentscript.js. History of 6 sidebar/dual-tab CVEs in 2025 suggests recurring Whale-specific boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar context — either via show({url:'https://victim.com'}) loading cross-origin page in panel, or via drag-drop navigation bypassing SOP
verify_steps: PASSIVE: Inspect sidebarAction.show URL loading behavior in extension manifest; check `use_navigation_bar` defaults; review wiki "How to avoid my extension from changing urls" mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://httpbin.org/headers'}) → attempt cross-origin fetch from panel content script → test drag-drop navigation with use_navigation_bar:false
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs `sync.encryption_bootstrap_token_per_account`
confidence: 62
reasoning: v4.38.386.14 binary (sha256 6dd7ebc4...) confirms Whale-only prefs keys + Whale-forked OSCrypt (os_crypt_whale.cc, wbc_wrapper_apis.cc, `''xv10` magic); /whalesync authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list; per-account bootstrap token envelope deviates from upstream Chromium sync.encryption_bootstrap_token.
evidence_needed: Per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where os_crypt_whale stores master key on Linux; whether whale_need_encryption_key_forced_time downgrades to a stale key
verify_steps: PASSIVE: objdump/strings on os_crypt_whale + whale_sync_util call sites for the bootstrap-token envelope and /whalesync/reset request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: Local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise; High
testability: PASSIVE
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[PARKED] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14: confidence 60 but testability HUMAN_ONLY with no passive-first verification path — requires binary install and manual sidebar interaction, cannot be validated statically
[FINAL] 1. Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux (confidence 62, class AUTH, testability PASSIVE) 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED) 3. Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14 (confidence 60, class OTHER, testability HUMAN_ONLY)
[NEXT] PROBE: Acquire latest Whale desktop installer (non-Naver mirror) and Android XAPK 3.9.14.9; extract and decompile; static grep for sync module strings in os_crypt_whale, whale_sync_util, whale_sync_auth_manager: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences", "xv10", "bootstrap_token" — zero network requests to naver infra
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[RISK] sync: 65 — custom `/whalesync` + per-account bootstrap tokens + server-tweakable Multiplay exclusion + Whale-only NID OAuth all confirmed in latest binary; key-storage/KDF and reset-auth not yet runtime-verified; server-tweakable client heuristics widen the sync attack surface | browser: 78 — sidebar/dual-tab (6 CVEs in 2025) remain 3 minor versions past last fix with zero 2026 CVEs; binary now available enables repro-first variant hunting | libs: 35 — paks now parseable for a bundled-lib inventory, and Whale-only `socket.io.slim.js` is confirmed bundled (content runtime-fetched); version-drift assessment still requires upstream comparison
## 2026-08-08 03:58:55 UTC [browser] (model nemotron3)
[PRIO] Whale browser sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9 — Whale-specific feature; CVE-2025-69234/69235 fixed Dec 2025; 3 minor version bumps with 0 CVEs; wiki confirms sidebarAction.show() loads arbitrary URL + drag-navigation exposure
[PRIO] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux, 6.55, atk=7 biz=8 tech=9 gate=2 cloud=3 fresh=8 — Binary strings confirm Whale-only prefs keys + Whale-forked OSCrypt; per-account bootstrap token envelope deviates from upstream Chromium; local profile access yields full sync decryption
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 6.10, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7 — Sync explicitly in scope; vendor docs: passphrase never leaves device; Android sync encryption added 2025-04; no public client code; binary static analysis required
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — whale.sidebarAction.show({url}) loads cross-origin content into sidebar panel; v4.38.386.14 (latest stable)
confidence: 60
reasoning: CVE-2025-69235 (CWE-346, SOP bypass) and CVE-2025-69234 (CWE-358, iframe sandbox escape) fixed in v4.35.351.12 (Dec 2025). Current stable v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki docs confirm sidebarAction.show() accepts `url` parameter to load arbitrary URL in panel; `use_navigation_bar=false` creates drag-navigation exposure. Sidebar context detected via `navigator.userAgent.includes('sidebar')` per sample contentscript.js. History of 6 sidebar/dual-tab CVEs in 2025 suggests recurring Whale-specific boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar context — either via show({url:'https://victim.com'}) loading cross-origin page in panel, or via drag-drop navigation bypassing SOP
verify_steps: PASSIVE: Inspect sidebarAction.show URL loading behavior in extension manifest; check `use_navigation_bar` defaults; review wiki "How to avoid my extension from changing urls" mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://httpbin.org/headers'}) → attempt cross-origin fetch from panel content script → test drag-drop navigation with use_navigation_bar:false
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs `sync.encryption_bootstrap_token_per_account`
confidence: 62
reasoning: v4.38.386.14 binary (sha256 6dd7ebc4...) confirms Whale-only prefs keys + Whale-forked OSCrypt (os_crypt_whale.cc, wbc_wrapper_apis.cc, `''xv10` magic); /whalesync authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list; per-account bootstrap token envelope deviates from upstream Chromium sync.encryption_bootstrap_token.
evidence_needed: Per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where os_crypt_whale stores master key on Linux; whether whale_need_encryption_key_forced_time downgrades to a stale key
verify_steps: PASSIVE: objdump/strings on os_crypt_whale + whale_sync_util call sites for the bootstrap-token envelope and /whalesync/reset request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: Local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise; High
testability: PASSIVE
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences[0m
[PRIO] Whale browser sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9 — Whale-specific feature; CVE-2025-69234/69235 fixed Dec 2025; 3 minor version bumps with 0 CVEs; wiki confirms sidebarAction.show() loads arbitrary URL + drag-navigation exposure
[PRIO] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux, 6.55, atk=7 biz=8 tech=9 gate=2 cloud=3 fresh=8 — Binary strings confirm Whale-only prefs keys + Whale-forked OSCrypt; per-account bootstrap token envelope deviates from upstream Chromium; local profile access yields full sync decryption
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 6.10, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7 — Sync explicitly in scope; vendor docs: passphrase never leaves device; Android sync encryption added 2025-04; no public client code; binary static analysis required
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — whale.sidebarAction.show({url}) loads cross-origin content into sidebar panel; v4.38.386.14 (latest stable)
confidence: 60
reasoning: CVE-2025-69235 (CWE-346, SOP bypass) and CVE-2025-69234 (CWE-358, iframe sandbox escape) fixed in v4.35.351.12 (Dec 2025). Current stable v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki docs confirm sidebarAction.show() accepts `url` parameter to load arbitrary URL in panel; `use_navigation_bar=false` creates drag-navigation exposure. Sidebar context detected via `navigator.userAgent.includes('sidebar')` per sample contentscript.js. History of 6 sidebar/dual-tab CVEs in 2025 suggests recurring Whale-specific boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar context — either via show({url:'https://victim.com'}) loading cross-origin page in panel, or via drag-drop navigation bypassing SOP
verify_steps: PASSIVE: Inspect sidebarAction.show URL loading behavior in extension manifest; check `use_navigation_bar` defaults; review wiki "How to avoid my extension from changing urls" mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://httpbin.org/headers'}) → attempt cross-origin fetch from panel content script → test drag-drop navigation with use_navigation_bar:false
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs `sync.encryption_bootstrap_token_per_account`
confidence: 62
reasoning: v4.38.386.14 binary (sha256 6dd7ebc4...) confirms Whale-only prefs keys + Whale-forked OSCrypt (os_crypt_whale.cc, wbc_wrapper_apis.cc, `''xv10` magic); /whalesync authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list; per-account bootstrap token envelope deviates from upstream Chromium sync.encryption_bootstrap_token.
evidence_needed: Per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where os_crypt_whale stores master key on Linux; whether whale_need_encryption_key_forced_time downgrades to a stale key
verify_steps: PASSIVE: objdump/strings on os_crypt_whale + whale_sync_util call sites for the bootstrap-token envelope and /whalesync/reset request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: Local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise; High
testability: PASSIVE
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[PARKED] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14: confidence 60 but testability HUMAN_ONLY with no passive-first verification path — requires binary install and manual sidebar interaction, cannot be validated statically
[PARKED] Whale sync passphrase KDF weakness / plaintext-adjacent key storage: confidence 58 but verify_steps require AUTH_HELPED (authorized test login) and binary acquisition which is not passive-first
[FINAL] 1. Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux (confidence 62, class AUTH, testability PASSIVE) 2. Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14 (confidence 60, class OTHER, testability HUMAN_ONLY) 3. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: objdump/strings on os_crypt_whale + whale_sync_util call sites for the bootstrap-token envelope and /whalesync/reset request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network — local static analysis of acquired binary only
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[RISK] sync: 65 — custom `/whalesync` + per-account bootstrap tokens + server-tweakable Multiplay exclusion + Whale-only NID OAuth all confirmed in latest binary; key-storage/KDF and reset-auth not yet runtime-verified; server-tweakable client heuristics widen the sync attack surface | browser: 78 — sidebar/dual-tab (6 CVEs in 2025) remain 3 minor versions past last fix with zero 2026 CVEs; binary now available enables repro-first variant hunting | libs: 35 — paks now parseable for a bundled-lib inventory, and Whale-only `socket.io.slim.js` is confirmed bundled (content runtime-fetched); version-drift assessment still requires upstream comparison
[PRIO] Whale browser sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9 — Whale-specific feature; CVE-2025-69234/69235 fixed Dec 2025; 3 minor version bumps with 0 CVEs; wiki confirms sidebarAction.show() loads arbitrary URL + drag-navigation exposure
[PRIO] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux, 6.55, atk=7 biz=8 tech=9 gate=2 cloud=3 fresh=8 — Binary strings confirm Whale-only prefs keys + Whale-forked OSCrypt; per-account bootstrap token envelope deviates from upstream Chromium; local profile access yields full sync decryption
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 6.10, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7 — Sync explicitly in scope; vendor docs: passphrase never leaves device; Android sync encryption added 2025-04; no public client code; binary static analysis required
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — whale.sidebarAction.show({url}) loads cross-origin content into sidebar panel; v4.38.386.14 (latest stable)
confidence: 60
reasoning: CVE-2025-69235 (CWE-346, SOP bypass) and CVE-2025-69234 (CWE-358, iframe sandbox escape) fixed in v4.35.351.12 (Dec 2025). Current stable v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki docs confirm sidebarAction.show() accepts `url` parameter to load arbitrary URL in panel; `use_navigation_bar=false` creates drag-navigation exposure. Sidebar context detected via `navigator.userAgent.includes('sidebar')` per sample contentscript.js. History of 6 sidebar/dual-tab CVEs in 2025 suggests recurring Whale-specific boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar context — either via show({url:'https://victim.com'}) loading cross-origin page in panel, or via drag-drop navigation bypassing SOP
verify_steps: PASSIVE: Inspect sidebarAction.show URL loading behavior in extension manifest; check `use_navigation_bar` defaults; review wiki "How to avoid my extension from changing urls" mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://httpbin.org/headers'}) → attempt cross-origin fetch from panel content script → test drag-drop navigation with use_navigation_bar:false
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs `sync.encryption_bootstrap_token_per_account`
confidence: 62
reasoning: v4.38.386.14 binary (sha256 6dd7ebc4...) confirms Whale-only prefs keys + Whale-forked OSCrypt (os_crypt_whale.cc, wbc_wrapper_apis.cc, `''xv10` magic); /whalesync authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list; per-account bootstrap token envelope deviates from upstream Chromium sync.encryption_bootstrap_token.
evidence_needed: Per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where os_crypt_whale stores master key on Linux; whether whale_need_encryption_key_forced_time downgrades to a stale key
verify_steps: PASSIVE: objdump/strings on os_crypt_whale + whale_sync_util call sites for the bootstrap-token envelope and /whalesync/reset request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: Local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise; High
testability: PASSIVE
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[PARKED] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14: confidence 60 but testability HUMAN_ONLY with no passive-first verification path — requires binary install and manual sidebar interaction, cannot be validated statically
[PARKED] Whale sync passphrase KDF weakness / plaintext-adjacent key storage: confidence 58 but verify_steps require AUTH_HELPED (authorized test login) and binary acquisition which is not passive-first
[FINAL] 1. Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux (confidence 62, class AUTH, testability PASSIVE) 2. Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14 (confidence 60, class OTHER, testability HUMAN_ONLY) 3. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: objdump/strings on os_crypt_whale + whale_sync_util call sites for the bootstrap-token envelope and /whalesync/reset request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network — local static analysis of acquired binary only
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[RISK] sync: 65 — custom `/whalesync` + per-account bootstrap tokens + server-tweakable Multiplay exclusion + Whale-only NID OAuth all confirmed in latest binary; key-storage/KDF and reset-auth not yet runtime-verified; server-tweakable client heuristics widen the sync attack surface | browser: 78 — sidebar/dual-tab (6 CVEs in 2025) remain 3 minor versions past last fix with zero 2026 CVEs; binary now available enables repro-first variant hunting | libs: 35 — paks now parseable for a bundled-lib inventory, and Whale-only `socket.io.slim.js` is confirmed bundled (content runtime-fetched); version-drift assessment still requires upstream comparison
## 2026-08-08 05:01:19 UTC [browser] (model nemotron3)
[NEW] NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 04:14:14 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context
[PRIO] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux, 6.55, atk=7 biz=8 tech=9 gate=2 cloud=3 fresh=8 — Binary strings confirm Whale-only prefs keys + Whale-forked OSCrypt (`os_crypt_whale.cc`, `wbc_wrapper_apis.cc`, `xv10` magic); per-account bootstrap token envelope deviates from upstream Chromium `sync.encryption_bootstrap_token`; local profile access yields full sync decryption
[PRIO] Whale browser sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9 — Whale-specific feature; CVE-2025-69234/69235 fixed Dec 2025; 3 minor version bumps with 0 CVEs; wiki confirms `sidebarAction.show()` loads arbitrary URL + drag-navigation exposure (`use_navigation_bar=false`)
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 6.10, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7 — Sync explicitly in scope; vendor docs: passphrase never leaves device; Android sync encryption added 2025-04; no public client code; binary static analysis required
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs `sync.encryption_bootstrap_token_per_account`
confidence: 62
reasoning: v4.38.386.14 binary (sha256 6dd7ebc4...) confirms Whale-only prefs keys + Whale-forked OSCrypt (`os_crypt_whale.cc`, `wbc_wrapper_apis.cc`, `xv10` magic); `/whalesync` authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list; per-account bootstrap token envelope deviates from upstream Chromium `sync.encryption_bootstrap_token`.
evidence_needed: Per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where `os_crypt_whale` stores master key on Linux; whether `whale_need_encryption_key_forced_time` downgrades to a stale key
verify_steps: PASSIVE: objdump/strings on `os_crypt_whale` + `whale_sync_util` call sites for the bootstrap-token envelope and `/whalesync/reset` request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: Local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise; High
testability: PASSIVE
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — `whale.sidebarAction.show({url})` loads cross-origin content into sidebar panel; v4.38.386.14 (latest stable)
confidence: 60
reasoning: CVE-2025-69235 (CWE-346, SOP bypass) and CVE-2025-69234 (CWE-358, iframe sandbox escape) fixed in v4.35.351.12 (Dec 2025). Current stable v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki docs confirm `sidebarAction.show()` accepts `url` parameter to load arbitrary URL in panel; `use_navigation_bar=false` creates drag-navigation exposure. Sidebar context detected via `navigator.userAgent.includes('sidebar')` per sample contentscript.js. History of 6 sidebar/dual-tab CVEs in 2025 suggests recurring Whale-specific boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar context — either via `show({url:'https://victim.com'})` loading cross-origin page in panel, or via drag-drop navigation bypassing SOP
verify_steps: PASSIVE: Inspect `sidebarAction.show` URL loading behavior in extension manifest; check `use_navigation_bar` defaults; review wiki "How to avoid my extension from changing urls" mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call `whale.sidebarAction.show({url:'https://httpbin.org/headers'})` → attempt cross-origin fetch from panel content script → test drag-drop navigation with `use_navigation_bar:false`
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[PARKED] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14: confidence 60 but testability HUMAN_ONLY with no passive-first verification path — requires binary install and manual sidebar interaction, cannot be validated statically
[PARKED] Whale sync passphrase KDF weakness / plaintext-adjacent key storage: confidence 58 but verify_steps require AUTH_HELPED (authorized test login) and binary acquisition which is not passive-first
[FINAL] 1. Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux (confidence 62, class AUTH, testability PASSIVE) 2. Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14 (confidence 60, class OTHER, testability HUMAN_ONLY) 3. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: objdump/strings on os_crypt_whale + whale_sync_util call sites for the bootstrap-token envelope and /whalesync/reset request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network — local static analysis of acquired binary only
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[LEARN] CONFIRMED @ NVD: 0 CVEs published for Whale in 2026 — no public disclosures exist for versions 4.35.352 through 4.38.386, confirming a 6-month vulnerability disclosure gap
[RISK] sync: 65 — custom `/whalesync` + per-account bootstrap tokens + server-tweakable Multiplay exclusion + Whale-only NID OAuth all confirmed in latest binary; key-storage/KDF and reset-auth not yet runtime-verified; server-tweakable client heuristics widen the sync attack surface | browser: 78 — sidebar/dual-tab (6 CVEs in 2025) remain 3 minor versions past last fix with zero 2026 CVEs; binary now available enables repro-first variant hunting | libs: 35 — paks now parseable for a bundled-lib inventory, and Whale-only `socket.io.slim.js` is confirmed bundled (content runtime-fetched); version-drift assessment still requires upstream comparison
## 2026-08-08 05:50:41 UTC [browser] (model nemotron3)
[NEW] NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 04:14:14 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context
[PRIO] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux, 6.55, atk=7 biz=8 tech=9 gate=2 cloud=3 fresh=8 — Binary strings confirm Whale-only prefs keys + Whale-forked OSCrypt (`os_crypt_whale.cc`, `wbc_wrapper_apis.cc`, `xv10` magic); per-account bootstrap token envelope deviates from upstream Chromium `sync.encryption_bootstrap_token`; local profile access yields full sync decryption
[PRIO] Whale browser sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9 — Whale-specific feature; CVE-2025-69234/69235 fixed Dec 2025; 3 minor version bumps with 0 CVEs; wiki confirms `sidebarAction.show()` loads arbitrary URL + drag-navigation exposure (`use_navigation_bar=false`)
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 6.10, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7 — Sync explicitly in scope; vendor docs: passphrase never leaves device; Android sync encryption added 2025-04; no public client code; binary static analysis required
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs `sync.encryption_bootstrap_token_per_account`
confidence: 62
reasoning: v4.38.386.14 binary (sha256 6dd7ebc4...) confirms Whale-only prefs keys + Whale-forked OSCrypt (`os_crypt_whale.cc`, `wbc_wrapper_apis.cc`, `xv10` magic); `/whalesync` authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list; per-account bootstrap token envelope deviates from upstream Chromium `sync.encryption_bootstrap_token`.
evidence_needed: Per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where `os_crypt_whale` stores master key on Linux; whether `whale_need_encryption_key_forced_time` downgrades to a stale key
verify_steps: PASSIVE: objdump/strings on `os_crypt_whale` + `whale_sync_util` call sites for the bootstrap-token envelope and `/whalesync/reset` request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: Local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise; High
testability: PASSIVE
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — `whale.sidebarAction.show({url})` loads cross-origin content into sidebar panel; v4.38.386.14 (latest stable)
confidence: 60
reasoning: CVE-2025-69235 (CWE-346, SOP bypass) and CVE-2025-69234 (CWE-358, iframe sandbox escape) fixed in v4.35.351.12 (Dec 2025). Current stable v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki docs confirm `sidebarAction.show()` accepts `url` parameter to load arbitrary URL in panel; `use_navigation_bar=false` creates drag-navigation exposure. Sidebar context detected via `navigator.userAgent.includes('sidebar')` per sample contentscript.js. History of 6 sidebar/dual-tab CVEs in 2025 suggests recurring Whale-specific boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar context — either via `show({url:'https://victim.com'})` loading cross-origin page in panel, or via drag-drop navigation bypassing SOP
verify_steps: PASSIVE: Inspect `sidebarAction.show` URL loading behavior in extension manifest; check `use_navigation_bar` defaults; review wiki "How to avoid my extension from changing urls" mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call `whale.sidebarAction.show({url:'https://httpbin.org/headers'})` → attempt cross-origin fetch from panel content script → test drag-drop navigation with `use_navigation_bar:false`
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[PARKED] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14: confidence 60 but testability HUMAN_ONLY with no passive-first verification path — requires binary install and manual sidebar interaction, cannot be validated statically
[PARKED] Whale sync passphrase KDF weakness / plaintext-adjacent key storage: confidence 58 but verify_steps require AUTH_HELPED (authorized test login) and binary acquisition which is not passive-first
[FINAL] 1. Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux (confidence 62, class AUTH, testability PASSIVE)
[NEXT] PROBE: objdump/strings on os_crypt_whale + whale_sync_util call sites for the bootstrap-token envelope and /whalesync/reset request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network — local static analysis of acquired binary only
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[LEARN] CONFIRMED @ NVD: 0 CVEs published for Whale in 2026 — no public disclosures exist for versions 4.35.352 through 4.38.386, confirming a 6-month vulnerability disclosure gap
[RISK] sync: 65 — custom `/whalesync` + per-account bootstrap tokens + server-tweakable Multiplay exclusion + Whale-only NID OAuth all confirmed in latest binary; key-storage/KDF and reset-auth not yet runtime-verified; server-tweakable client heuristics widen the sync attack surface | browser: 78 — sidebar/dual-tab (6 CVEs in 2025) remain 3 minor versions past last fix with zero 2026 CVEs; binary now available enables repro-first variant hunting | libs: 35 — paks now parseable for a bundled-lib inventory, and Whale-only `socket.io.slim.js` is confirmed bundled (content runtime-fetched); version-drift assessment still requires upstream comparison
## 2026-08-08 06:29:43 UTC [browser] (model nemotron3)
[NEW] NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 06:06:56 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context
[PRIO] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux, 6.55, atk=7 biz=8 tech=9 gate=2 cloud=3 fresh=8 — Binary strings confirm Whale-only prefs keys + Whale-forked OSCrypt (`os_crypt_whale.cc`, `wbc_wrapper_apis.cc`, `xv10` magic); per-account bootstrap token envelope deviates from upstream Chromium `sync.encryption_bootstrap_token`; local profile access yields full sync decryption
[PRIO] Whale browser sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9 — Whale-specific feature; CVE-2025-69234/69235 fixed Dec 2025; 3 minor version bumps with 0 CVEs; wiki confirms `sidebarAction.show()` loads arbitrary URL + drag-navigation exposure (`use_navigation_bar=false`)
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 6.10, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7 — Sync explicitly in scope; vendor docs: passphrase never leaves device; Android sync encryption added 2025-04; no public client code; binary static analysis required
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs `sync.encryption_bootstrap_token_per_account`
confidence: 62
reasoning: v4.38.386.14 binary (sha256 6dd7ebc4...) confirms Whale-only prefs keys + Whale-forked OSCrypt (`os_crypt_whale.cc`, `wbc_wrapper_apis.cc`, `xv10` magic); `/whalesync` authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list; per-account bootstrap token envelope deviates from upstream Chromium `sync.encryption_bootstrap_token`.
evidence_needed: Per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where `os_crypt_whale` stores master key on Linux; whether `whale_need_encryption_key_forced_time` downgrades to a stale key
verify_steps: PASSIVE: objdump/strings on `os_crypt_whale` + `whale_sync_util` call sites for the bootstrap-token envelope and `/whalesync/reset` request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: Local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise; High
testability: PASSIVE
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — `whale.sidebarAction.show({url})` loads cross-origin content into sidebar panel; v4.38.386.14 (latest stable)
confidence: 60
reasoning: CVE-2025-69235 (CWE-346, SOP bypass) and CVE-2025-69234 (CWE-358, iframe sandbox escape) fixed in v4.35.351.12 (Dec 2025). Current stable v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki docs confirm `sidebarAction.show()` accepts `url` parameter to load arbitrary URL in panel; `use_navigation_bar=false` creates drag-navigation exposure. Sidebar context detected via `navigator.userAgent.includes('sidebar')` per sample contentscript.js. History of 6 sidebar/dual-tab CVEs in 2025 suggests recurring Whale-specific boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar context — either via `show({url:'https://victim.com'})` loading cross-origin page in panel, or via drag-drop navigation bypassing SOP
verify_steps: PASSIVE: Inspect `sidebarAction.show` URL loading behavior in extension manifest; check `use_navigation_bar` defaults; review wiki "How to avoid my extension from changing urls" mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call `whale.sidebarAction.show({url:'https://httpbin.org/headers'})` → attempt cross-origin fetch from panel content script → test drag-drop navigation with `use_navigation_bar:false`
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[PARKED] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14: confidence 60 but testability HUMAN_ONLY with no passive-first verification path — requires binary install and manual sidebar interaction, cannot be validated statically
[PARKED] Whale sync passphrase KDF weakness / plaintext-adjacent key storage: confidence 58 but verify_steps require AUTH_HELPED (authorized test login) and binary acquisition which is not passive-first
[FINAL] 1. Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux (confidence 62, class AUTH, testability PASSIVE)
[NEXT] PROBE: objdump/strings on os_crypt_whale + whale_sync_util call sites for the bootstrap-token envelope and /whalesync/reset request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network — local static analysis of acquired binary only
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[LEARN] CONFIRMED @ NVD: 0 CVEs published for Whale in 2026 — no public disclosures exist for versions 4.35.352 through 4.38.386, confirming a 6-month vulnerability disclosure gap
[RISK] sync: 65 — custom `/whalesync` + per-account bootstrap tokens + server-tweakable Multiplay exclusion + Whale-only NID OAuth all confirmed in latest binary; key-storage/KDF and reset-auth not yet runtime-verified; server-tweakable client heuristics widen the sync attack surface | browser: 78 — sidebar/dual-tab (6 CVEs in 2025) remain 3 minor versions past last fix with zero 2026 CVEs; binary now available enables repro-first variant hunting | libs: 35 — paks now parseable for a bundled-lib inventory, and Whale-only `socket.io.slim.js` is confirmed bundled (content runtime-fetched); version-drift assessment still requires upstream comparison
## 2026-08-08 07:33:03 UTC [browser] (model nemotron3)
[PRIO] Whale sync bootstrap-token envelope (OSCrypt deviation on Linux), 6.55, atk=7 biz=8 tech=9 gate=2 cloud=3 fresh=8
[PRIO] Whale sync passphrase KDF + bootstrap-token envelope (desktop v4.38.386.14 + Android 3.9.14.9), 6.45, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7
[PRIO] Whale sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs `sync.encryption_bootstrap_token_per_account`
confidence: 62
reasoning: v4.38.386.14 binary (sha256 6dd7ebc4...) confirms Whale-only prefs keys + Whale-forked OSCrypt (`os_crypt_whale.cc`, `wbc_wrapper_apis.cc`, `xv10` magic); `/whalesync` authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list; per-account bootstrap token envelope deviates from upstream Chromium `sync.encryption_bootstrap_token`.
evidence_needed: Per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where `os_crypt_whale` stores master key on Linux; whether `whale_need_encryption_key_forced_time` downgrades to a stale key
verify_steps: PASSIVE: objdump/strings on `os_crypt_whale` + `whale_sync_util` call sites for the bootstrap-token envelope and `/whalesync/reset` request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: Local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise; High
testability: PASSIVE
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — `whale.sidebarAction.show({url})` loads cross-origin content into sidebar panel; v4.38.386.14 (latest stable)
confidence: 60
reasoning: CVE-2025-69235 (CWE-346, SOP bypass) and CVE-2025-69234 (CWE-358, iframe sandbox escape) fixed in v4.35.351.12 (Dec 2025). Current stable v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki docs confirm `sidebarAction.show()` accepts `url` parameter to load arbitrary URL in panel; `use_navigation_bar=false` creates drag-navigation exposure. Sidebar context detected via `navigator.userAgent.includes('sidebar')` per sample contentscript.js. History of 6 sidebar/dual-tab CVEs in 2025 suggests recurring Whale-specific boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar context — either via `show({url:'https://victim.com'})` loading cross-origin page in panel, or via drag-drop navigation bypassing SOP
verify_steps: PASSIVE: Inspect `sidebarAction.show` URL loading behavior in extension manifest; check `use_navigation_bar` defaults; review wiki "How to avoid my extension from changing urls" mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call `whale.sidebarAction.show({url:'https://httpbin.org/headers'})` → attempt cross-origin fetch from panel content script → test drag-drop navigation with `use_navigation_bar:false`
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[PARKED] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14: confidence 60 but testability HUMAN_ONLY with no passive-first verification path — requires binary install and manual sidebar interaction, cannot be validated statically
[FINAL] 1. Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux (confidence 62, class AUTH, testability PASSIVE)
[FINAL] 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: objdump/strings on os_crypt_whale + whale_sync_util call sites for the bootstrap-token envelope and /whalesync/reset request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network — local static analysis of acquired binary only
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[LEARN] CONFIRMED @ NVD: 0 CVEs published for Whale in 2026 — no public disclosures exist for versions 4.35.352 through 4.38.386, confirming a 6-month vulnerability disclosure gap
[LEARN] REJECTED binary acquisition @ APKMirror/APKCombo/apk.support/apkpure: Cloudflare 403 on curl egress in this sandbox — PASSIVE binary-download path is dead here; only session/JS-resolved channels (uptodown dw, apkpure.net) remain
[LEARN] ACCEPTED Android sync asset @ com.naver.whale 3.9.14.9: version + SHA256 pinned via non-Naver mirror metadata (APKMirror/Uptodown) — in-scope sync surface confirmed real, latest, and verifiable once the download channel is solved
[LEARN] ACCEPTED @ binary static analysis: Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account`, `_migration_done`, `whale_need_encryption_key_forced_time`) + Whale-forked `os_crypt_whale.cc`/`wbc_wrapper_apis.cc` + `xv10` magic CONFIRMED present in v4.38.386.14 binary via prior bigpickle/laguna runs — the KDF/envelope gap is the unfilled verification
[LEARN] ACCEPTED @ socket.io.slim.js bundled: Confirmed Whale-only in `resources.pak` (prior bigpickle/laguna recon strings) — a non-Chromium runtime-bundled lib worth auditing for event-handler injection; however the handler itself may be runtime-fetched, degrading passive evidence — stale until re-acquired
[RISK] sync: 65 — custom `/whalesync` + per-account bootstrap tokens + server-tweakable Multiplay exclusion + Whale-only NID OAuth all confirmed in latest binary; key-storage/KDF and reset-auth not yet runtime-verified; server-tweakable client heuristics widen the sync attack surface | browser: 78 — sidebar/dual-tab (6 CVEs in 2025) remain 3 minor versions past last fix with zero 2026 CVEs; binary now available enables repro-first variant hunting | libs: 35 — paks now parseable for a bundled-lib inventory, and Whale-only `socket.io.slim.js` is confirmed bundled (content runtime-fetched); version-drift assessment still requires upstream comparison
## 2026-08-08 08:19:53 UTC [browser] (model nemotron3)
## 2026-08-08 09:07:39 UTC [browser] (model nemotron3)
[NEW] NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 08:21:21 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected
[PRIO] Whale sync bootstrap-token envelope (OSCrypt deviation on Linux, v4.38.386.14), 6.55, atk=7 biz=8 tech=9 gate=2 cloud=3 fresh=8
[PRIO] Whale sync passphrase KDF + bootstrap-token envelope (desktop v4.38.386.14 + Android 3.9.14.9), 6.45, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7
[PRIO] Whale sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9
[PRIO] Bundled socket.io.slim.js in resources.pak (Whale-only), 4.85, atk=5 biz=4 tech=6 gate=8 cloud=2 fresh=4
[PRIO] Whale dual-tab environment on v4.38.386.14, 6.15, atk=7 biz=7 tech=6 gate=2 cloud=3 fresh=6
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs `sync.encryption_bootstrap_token_per_account`
confidence: 62
reasoning: v4.38.386.14 binary (sha256 6dd7ebc4...) confirms Whale-only prefs keys + Whale-forked OSCrypt (`os_crypt_whale.cc`, `wbc_wrapper_apis.cc`, `xv10` magic); `/whalesync` authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list; per-account bootstrap token envelope deviates from upstream Chromium `sync.encryption_bootstrap_token`.
evidence_needed: Per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where `os_crypt_whale` stores master key on Linux; whether `whale_need_encryption_key_forced_time` downgrades to a stale key
verify_steps: PASSIVE: objdump/strings on `os_crypt_whale` + `whale_sync_util` call sites for the bootstrap-token envelope and `/whalesync/reset` request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: Local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise; High
testability: PASSIVE
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — `whale.sidebarAction.show({url})` loads cross-origin content into sidebar panel; v4.38.386.14 (latest stable)
confidence: 60
reasoning: CVE-2025-69235 (CWE-346, SOP bypass) and CVE-2025-69234 (CWE-358, iframe sandbox escape) fixed in v4.35.351.12 (Dec 2025). Current stable v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki docs confirm `sidebarAction.show()` accepts `url` parameter to load arbitrary URL in panel; `use_navigation_bar=false` creates drag-navigation exposure. Sidebar context detected via `navigator.userAgent.includes('sidebar')` per sample contentscript.js. History of 6 sidebar/dual-tab CVEs in 2025 suggests recurring Whale-specific boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar context — either via `show({url:'https://victim.com'})` loading cross-origin page in panel, or via drag-drop navigation bypassing SOP
verify_steps: PASSIVE: Inspect `sidebarAction.show` URL loading behavior in extension manifest; check `use_navigation_bar` defaults; review wiki "How to avoid my extension from changing urls" mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call `whale.sidebarAction.show({url:'https://httpbin.org/headers'})` → attempt cross-origin fetch from panel content script → test drag-drop navigation with `use_navigation_bar:false`
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[PARKED] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14: confidence 60 but testability HUMAN_ONLY with no passive-first verification path — requires binary install and manual sidebar interaction, cannot be validated statically
[FINAL] 1. Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux (confidence 62, class AUTH, testability PASSIVE)
[FINAL] 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: objdump/strings on `os_crypt_whale` + `whale_sync_util` call sites for the bootstrap-token envelope and `/whalesync/reset` request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network — local static analysis of acquired binary only
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[LEARN] CONFIRMED @ NVD: 0 CVEs published for Whale in 2026 — no public disclosures exist for versions 4.35.352 through 4.38.386, confirming a 6-month vulnerability disclosure gap
[LEARN] REJECTED binary acquisition @ APKMirror/APKCombo/apkpure/cloudfront CDN: All download paths blocked in-sandbox (cloudfront DNS `No answer`; APKMirror 403; uptodown 410; apkpure.com 403; Naver domains excluded) — binary acquisition requires HUMAN with unrestricted internet access
[LEARN] ACCEPTED @ binary static analysis: Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account` sha256=`7b06e6e9...`, `_migration_done`, `whale_need_encryption_key_forced_time`) + Whale-forked `os_crypt_whale.cc`/`wbc_wrapper_apis.cc` + `xv10` magic CONFIRMED present in v4.38.386.14 binary via prior bigpickle/laguna runs — KDF constants/iteration counts + master-key storage location remain unextracted (stale until re-acquired)
[RISK] sync: 65 — custom `/whalesync` + per-account bootstrap tokens + server-tweakable Multiplay exclusion + Whale-only NID OAuth all confirmed in latest binary; key-storage/KDF and reset-auth not yet runtime-verified; server-tweakable client heuristics widen the sync attack surface | browser: 78 — sidebar/dual-tab (6 CVEs in 2025) remain 3 minor versions past last fix with zero 2026 CVEs; binary now available enables repro-first variant hunting | libs: 35 — paks now parseable for a bundled-lib inventory, and Whale-only `socket.io.slim.js` is confirmed bundled (content runtime-fetched); version-drift assessment still requires upstream comparison
## 2026-08-08 09:53:05 UTC [browser] (model nemotron3)
[NEW] NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 08:21:21 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected
[PRIO] Whale sync bootstrap-token envelope (OSCrypt deviation on Linux, v4.38.386.14), 6.55, atk=7 biz=8 tech=9 gate=2 cloud=3 fresh=8
[PRIO] Whale sync passphrase KDF + bootstrap-token envelope (desktop v4.38.386.14 + Android 3.9.14.9), 6.45, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7
[PRIO] Whale sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9
[PRIO] Bundled socket.io.slim.js in resources.pak (Whale-only), 4.85, atk=5 biz=4 tech=6 gate=8 cloud=2 fresh=4
[PRIO] Whale dual-tab environment on v4.38.386.14, 6.15, atk=7 biz=7 tech=6 gate=2 cloud=3 fresh=6
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs `sync.encryption_bootstrap_token_per_account`
confidence: 62
reasoning: v4.38.386.14 binary (sha256 6dd7ebc4...) confirms Whale-only prefs keys + Whale-forked OSCrypt (`os_crypt_whale.cc`, `wbc_wrapper_apis.cc`, `xv10` magic); `/whalesync` authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list; per-account bootstrap token envelope deviates from upstream Chromium `sync.encryption_bootstrap_token`.
evidence_needed: Per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where `os_crypt_whale` stores master key on Linux; whether `whale_need_encryption_key_forced_time` downgrades to a stale key
verify_steps: PASSIVE: objdump/strings on `os_crypt_whale` + `whale_sync_util` call sites for the bootstrap-token envelope and `/whalesync/reset` request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: Local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise; High
testability: PASSIVE
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — `whale.sidebarAction.show({url})` loads cross-origin content into sidebar panel; v4.38.386.14 (latest stable)
confidence: 60
reasoning: CVE-2025-69235 (CWE-346, SOP bypass) and CVE-2025-69234 (CWE-358, iframe sandbox escape) fixed in v4.35.351.12 (Dec 2025). Current stable v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki docs confirm `sidebarAction.show()` accepts `url` parameter to load arbitrary URL in panel; `use_navigation_bar=false` creates drag-navigation exposure. Sidebar context detected via `navigator.userAgent.includes('sidebar')` per sample contentscript.js. History of 6 sidebar/dual-tab CVEs in 2025 suggests recurring Whale-specific boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar context — either via `show({url:'https://victim.com'})` loading cross-origin page in panel, or via drag-drop navigation bypassing SOP
verify_steps: PASSIVE: Inspect `sidebarAction.show` URL loading behavior in extension manifest; check `use_navigation_bar` defaults; review wiki "How to avoid my extension from changing urls" mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call `whale.sidebarAction.show({url:'https://httpbin.org/headers'})` → attempt cross-origin fetch from panel content script → test drag-drop navigation with `use_navigation_bar:false`
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[PARKED] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14: confidence 60 but testability HUMAN_ONLY with no passive-first verification path — requires binary install and manual sidebar interaction, cannot be validated statically
[FINAL] 1. Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux (confidence 62, class AUTH, testability PASSIVE)
[FINAL] 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: objdump/strings on `os_crypt_whale` + `whale_sync_util` call sites for the bootstrap-token envelope and `/whalesync/reset` request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network — local static analysis of acquired binary only
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[LEARN] CONFIRMED @ NVD: 0 CVEs published for Whale in 2026 — no public disclosures exist for versions 4.35.352 through 4.38.386, confirming a 6-month vulnerability disclosure gap
[LEARN] REJECTED binary acquisition @ APKMirror/APKCombo/apkpure/cloudfront CDN: All download paths blocked in-sandbox (cloudfront DNS `No answer`; APKMirror 403; uptodown 410; apkpure.com 403; Naver domains excluded) — binary acquisition requires HUMAN with unrestricted internet access
[LEARN] ACCEPTED @ binary static analysis: Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account` sha256=`7b06e6e9...`, `_migration_done`, `whale_need_encryption_key_forced_time`) + Whale-forked `os_crypt_whale.cc`/`wbc_wrapper_apis.cc` + `xv10` magic CONFIRMED present in v4.38.386.14 binary via prior bigpickle/laguna runs — KDF constants/iteration counts + master-key storage location remain unextracted (stale until re-acquired)
[RISK] sync: 65 — custom `/whalesync` + per-account bootstrap tokens + server-tweakable Multiplay exclusion + Whale-only NID OAuth all confirmed in latest binary; key-storage/KDF and reset-auth not yet runtime-verified; server-tweakable client heuristics widen the sync attack surface | browser: 78 — sidebar/dual-tab (6 CVEs in 2025) remain 3 minor versions past last fix with zero 2026 CVEs; binary now available enables repro-first variant hunting | libs: 35 — paks now parseable for a bundled-lib inventory, and Whale-only `socket.io.slim.js` is confirmed bundled (content runtime-fetched); version-drift assessment still requires upstream comparison
## 2026-08-08 10:35:44 UTC [browser] (model nemotron3)
[NEW] NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 09:56:46 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected
[PRIO] Whale sync bootstrap-token envelope (OSCrypt deviation on Linux, v4.38.386.14), 6.55, atk=7 biz=8 tech=9 gate=2 cloud=3 fresh=8
[PRIO] Whale sync passphrase KDF + bootstrap-token envelope (desktop v4.38.386.14 + Android 3.9.14.9), 6.45, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7
[PRIO] Whale sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9
[PRIO] Bundled socket.io.slim.js in resources.pak (Whale-only), 4.85, atk=5 biz=4 tech=6 gate=8 cloud=2 fresh=4
[PRIO] Whale dual-tab environment on v4.38.386.14, 6.15, atk=7 biz=7 tech=6 gate=2 cloud=3 fresh=6
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs `sync.encryption_bootstrap_token_per_account`
confidence: 62
reasoning: v4.38.386.14 binary confirms Whale-only prefs keys + Whale-forked OSCrypt (`os_crypt_whale.cc`, `wbc_wrapper_apis.cc`, `xv10` magic); `/whalesync` authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list; per-account bootstrap token envelope deviates from upstream Chromium `sync.encryption_bootstrap_token`.
evidence_needed: Per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where `os_crypt_whale` stores master key on Linux; whether `whale_need_encryption_key_forced_time` downgrades to a stale key
verify_steps: PASSIVE: objdump/strings on `os_crypt_whale` + `whale_sync_util` call sites for the bootstrap-token envelope and `/whalesync/reset` request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: Local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise; High
testability: PASSIVE
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — `whale.sidebarAction.show({url})` loads cross-origin content into sidebar panel; v4.38.386.14 (latest stable)
confidence: 60
reasoning: CVE-2025-69235 (CWE-346, SOP bypass) and CVE-2025-69234 (CWE-358, iframe sandbox escape) fixed in v4.35.351.12 (Dec 2025). Current stable v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki docs confirm `sidebarAction.show()` accepts `url` parameter to load arbitrary URL in panel; `use_navigation_bar=false` creates drag-navigation exposure. Sidebar context detected via `navigator.userAgent.includes('sidebar')` per sample contentscript.js. History of 6 sidebar/dual-tab CVEs in 2025 suggests recurring Whale-specific boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar context — either via `show({url:'https://victim.com'})` loading cross-origin page in panel, or via drag-drop navigation bypassing SOP
verify_steps: PASSIVE: Inspect `sidebarAction.show` URL loading behavior in extension manifest; check `use_navigation_bar` defaults; review wiki "How to avoid my extension from changing urls" mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call `whale.sidebarAction.show({url:'https://httpbin.org/headers'})` → attempt cross-origin fetch from panel content script → test drag-drop navigation with `use_navigation_bar:false`
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[PARKED] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14: confidence 60 but testability HUMAN_ONLY with no passive-first verification path — requires binary install and manual sidebar interaction, cannot be validated statically
[FINAL] 1. Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux (confidence 62, class AUTH, testability PASSIVE)
[FINAL] 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: objdump/strings on `os_crypt_whale` + `whale_sync_util` call sites for the bootstrap-token envelope and `/whalesync/reset` request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network — local static analysis of acquired binary only
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[LEARN] CONFIRMED @ NVD: 0 CVEs published for Whale in 2026 — no public disclosures exist for versions 4.35.352 through 4.38.386, confirming a 6-month vulnerability disclosure gap
[LEARN] REJECTED binary acquisition @ APKMirror/APKCombo/apkpure/cloudfront CDN: All download paths blocked in-sandbox (cloudfront DNS `No answer`; APKMirror 403; uptodown 410; apkpure.com 403; Naver domains excluded) — binary acquisition requires HUMAN with unrestricted internet access
[LEARN] ACCEPTED @ binary static analysis: Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account` sha256=`7b06e6e9...`, `_migration_done`, `whale_need_encryption_key_forced_time`) + Whale-forked `os_crypt_whale.cc`/`wbc_wrapper_apis.cc` + `xv10` magic CONFIRMED present in v4.38.386.14 binary via prior bigpickle/laguna runs — KDF constants/iteration counts + master-key storage location remain unextracted (stale until re-acquired)
[RISK] sync: 65 — custom `/whalesync` + per-account bootstrap tokens + server-tweakable Multiplay exclusion + Whale-only NID OAuth all confirmed in latest binary; key-storage/KDF and reset-auth not yet runtime-verified; server-tweakable client heuristics widen the sync attack surface | browser: 78 — sidebar/dual-tab (6 CVEs in 2025) remain 3 minor versions past last fix with zero 2026 CVEs; binary now available enables repro-first variant hunting | libs: 35 — paks now parseable for a bundled-lib inventory, and Whale-only `socket.io.slim.js` is confirmed bundled (content runtime-fetched); version-drift assessment still requires upstream comparison
## 2026-08-08 11:06:08 UTC [browser] (model nemotron3)
[PRIO] Whale sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9
[PRIO] Whale sync bootstrap-token envelope (OSCrypt deviation on Linux, v4.38.386.14), 6.55, atk=7 biz=8 tech=9 gate=2 cloud=3 fresh=8
[PRIO] Whale sync passphrase KDF + bootstrap-token envelope (desktop v4.38.386.14 + Android 3.9.14.9), 6.45, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7
[PRIO] Whale dual-tab environment on v4.38.386.14, 6.15, atk=7 biz=7 tech=6 gate=2 cloud=3 fresh=6
[PRIO] Bundled socket.io.slim.js in resources.pak (Whale-only), 4.85, atk=5 biz=4 tech=6 gate=8 cloud=2 fresh=4
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: whale.sidebarAction.show({url}) sidebar panel loader
confidence: 60
reasoning: CVE-2025-69235 (CWE-346) fixed in v4.35.351.12; v4.38.386.14 is 3 minor bumps ahead with 0 CVEs. Wiki confirms show({url}) loads arbitrary URL in panel; use_navigation_bar=false exposes drag-navigation. Sample extension content_scripts match ALL origins and background.js calls sidebarAction.show() without origin validation.
evidence_needed: Running browser v4.38.386.14 demonstrating cross-origin data access from sidebar context via show({url:'https://victim.com'}) or drag-drop navigation bypass
verify_steps: PASSIVE: Inspect sidebarAction.show URL loading behavior in extension manifest; check use_navigation_bar defaults; review wiki mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://httpbin.org/headers'}) → attempt cross-origin fetch from panel content script → test drag-drop with use_navigation_bar:false
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation (Critical)
testability: HUMAN_ONLY
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs sync.encryption_bootstrap_token_per_account
confidence: 62
reasoning: v4.38.386.14 binary confirms Whale-only prefs keys + Whale-forked OSCrypt (os_crypt_whale.cc, wbc_wrapper_apis.cc, xv10 magic); /whalesync authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list; per-account bootstrap token envelope deviates from upstream Chromium sync.encryption_bootstrap_token.
evidence_needed: Per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where os_crypt_whale stores master key on Linux; whether whale_need_encryption_key_forced_time downgrades to stale key
verify_steps: PASSIVE: objdump/strings on os_crypt_whale + whale_sync_util call sites for bootstrap-token envelope and /whalesync/reset request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: Local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise (High)
testability: PASSIVE
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/sync
## 2026-08-08 11:42:29 UTC [browser] (model nemotron3)
[PRIO] Whale sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9
[PRIO] Whale sync bootstrap-token envelope (OSCrypt deviation on Linux, v4.38.386.14), 6.55, atk=7 biz=8 tech=9 gate=2 cloud=3 fresh=8
[PRIO] Whale sync passphrase KDF + bootstrap-token envelope (desktop v4.38.386.14 + Android 3.9.14.9), 6.45, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7
[PRIO] Whale dual-tab environment on v4.38.386.14, 6.15, atk=7 biz=7 tech=6 gate=2 cloud=3 fresh=6
[PRIO] Bundled socket.io.slim.js in resources.pak (Whale-only), 4.85, atk=5 biz=4 tech=6 gate=8 cloud=2 fresh=4
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: whale.sidebarAction.show({url}) sidebar panel loader
confidence: 60
reasoning: CVE-2025-69235 (CWE-346) fixed in v4.35.351.12; v4.38.386.14 is 3 minor bumps ahead with 0 CVEs. Wiki confirms show({url}) loads arbitrary URL in panel; use_navigation_bar=false exposes drag-navigation. Sample extension content_scripts match ALL origins and background.js calls sidebarAction.show() without origin validation.
evidence_needed: Running browser v4.38.386.14 demonstrating cross-origin data access from sidebar context via show({url:'https://victim.com'}) or drag-drop navigation bypass
verify_steps: PASSIVE: Inspect sidebarAction.show URL loading behavior in extension manifest; check use_navigation_bar defaults; review wiki mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://httpbin.org/headers'}) → attempt cross-origin fetch from panel content script → test drag-drop with use_navigation_bar:false
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation (Critical)
testability: HUMAN_ONLY
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs sync.encryption_bootstrap_token_per_account
confidence: 62
reasoning: v4.38.386.14 binary confirms Whale-only prefs keys + Whale-forked OSCrypt (os_crypt_whale.cc, wbc_wrapper_apis.cc, xv10 magic); /whalesync authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list; per-account bootstrap token envelope deviates from upstream Chromium sync.encryption_bootstrap_token.
evidence_needed: Per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where os_crypt_whale stores master key on Linux; whether whale_need_encryption_key_forced_time downgrades to stale key
verify_steps: PASSIVE: objdump/strings on os_crypt_whale + whale_sync_util call sites for bootstrap-token envelope and /whalesync/reset request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: Local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise (High)
testability: PASSIVE
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9)
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[PARKED] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14: confidence 60 but testability HUMAN_ONLY with no passive-first verification path — requires binary install and manual sidebar interaction, cannot be validated statically
[FINAL] 1. Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux (confidence 62, class AUTH, testability PASSIVE)
[FINAL] 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: objdump/strings on `os_crypt_whale` + `whale_sync_util` call sites for the bootstrap-token envelope and `/whalesync/reset` request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network — local static analysis of acquired binary only
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[LEARN] CONFIRMED @ NVD: 0 CVEs published for Whale in 2026 — no public disclosures exist for versions 4.35.352 through 4.38.386, confirming a 6-month vulnerability disclosure gap
[LEARN] REJECTED binary acquisition @ APKMirror/APKCombo/apkpure/cloudfront CDN: All download paths blocked in-sandbox (cloudfront DNS `No answer`; APKMirror 403; uptodown 410; apkpure.com 403; Naver domains excluded) — binary acquisition requires HUMAN with unrestricted internet access
[LEARN] ACCEPTED @ binary static analysis: Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account` sha256=`7b06e6e9...`, `_migration_done`, `whale_need_encryption_key_forced_time`) + Whale-forked `os_crypt_whale.cc`/`wbc_wrapper_apis.cc` + `xv10` magic CONFIRMED present in v4.38.386.14 binary via prior bigpickle/laguna runs — KDF constants/iteration counts + master-key storage location remain unextracted (stale until re-acquired)
[RISK] sync: 65 — custom `/whalesync` + per-account bootstrap tokens + server-tweakable Multiplay exclusion + Whale-only NID OAuth all confirmed in latest binary; key-storage/KDF and reset-auth not yet runtime-verified; server-tweakable client heuristics widen the sync attack surface | browser: 78 — sidebar/dual-tab (6 CVEs in 2025) remain 3 minor versions past last fix with zero 2026 CVEs; binary now available enables repro-first variant hunting | libs: 35 — paks now parseable for a bundled-lib inventory, and Whale-only `socket.io.slim.js` is confirmed bundled (content runtime-fetched); version-drift assessment still requires upstream comparison
## 2026-08-08 12:04:15 UTC [browser] (model nemotron3)
[PRIO] Whale sync bootstrap-token envelope (OSCrypt deviation on Linux, v4.38.386.14), 6.55, atk=7 biz=8 tech=9 gate=2 cloud=3 fresh=8
[PRIO] Whale sync passphrase KDF + bootstrap-token envelope (desktop v4.38.386.14 + Android 3.9.14.9), 6.45, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7
[PRIO] Whale sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 6.30, atk=7 biz=8 tech=6 gate=2 cloud=3 fresh=8
[PRIO] Whale dual-tab environment on v4.38.386.14, 6.15, atk=7 biz=7 tech=6 gate=2 cloud=3 fresh=6
[PRIO] Bundled socket.io.slim.js in resources.pak (Whale-only), 4.85, atk=5 biz=4 tech=6 gate=8 cloud=2 fresh=4
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs sync.encryption_bootstrap_token_per_account
confidence: 62
reasoning: v4.38.386.14 binary confirms Whale-only prefs keys (sync.encryption_bootstrap_token_per_account, _migration_done, whale_need_encryption_key_forced_time) + Whale-forked OSCrypt (os_crypt_whale.cc, wbc_wrapper_apis.cc, xv10 magic); /whalesync authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list; per-account bootstrap token envelope deviates from upstream Chromium sync.encryption_bootstrap_token
evidence_needed: Per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where os_crypt_whale stores master key on Linux; whether whale_need_encryption_key_forced_time downgrades to stale key
verify_steps: PASSIVE: objdump/strings on os_crypt_whale + whale_sync_util call sites for bootstrap-token envelope and /whalesync/reset request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: Local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise (High)
testability: PASSIVE
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9)
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: whale.sidebarAction.show({url}) sidebar panel loader
confidence: 60
reasoning: CVE-2025-69235 (CWE-346) fixed in v4.35.351.12; v4.38.386.14 is 3 minor bumps ahead with 0 CVEs. Sample extension (translate branch) content_scripts match ALL origins (http://*/*, https://*/*); background.js calls whale.sidebarAction.show() + whale.windows.create() without origin validation; sidebar context detected via navigator.userAgent.includes('sidebar')
evidence_needed: Running browser v4.38.386.14 demonstrating cross-origin data access from sidebar context via show({url:'https://victim.com'}) or drag-drop navigation bypass with use_navigation_bar:false
verify_steps: HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://httpbin.org/headers'}) → attempt cross-origin fetch from panel content script → test drag-drop with use_navigation_bar:false
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation (Critical)
testability: HUMAN_ONLY
[PARKED] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14: confidence 60 but testability HUMAN_ONLY with no passive-first verification path — requires binary install and manual sidebar interaction, cannot be validated statically
[FINAL] 1. Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux (confidence 62, class AUTH, testability PASSIVE)
[FINAL] 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: objdump/strings on `os_crypt_whale` + `whale_sync_util` call sites for the bootstrap-token envelope and `/whalesync/reset` request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network — local static analysis of acquired binary only
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[LEARN] CONFIRMED @ NVD: 0 CVEs published for Whale in 2026 — no public disclosures exist for versions 4.35.352 through 4.38.386, confirming a 6-month vulnerability disclosure gap
[LEARN] REJECTED binary acquisition @ APKMirror/APKCombo/apkpure/cloudfront CDN: All download paths blocked in-sandbox (cloudfront DNS `No answer`; APKMirror 403; uptodown 410; apkpure.com 403; Naver domains excluded) — binary acquisition requires HUMAN with unrestricted internet access
[LEARN] ACCEPTED @ binary static analysis: Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account` sha256=`7b06e6e9...`, `_migration_done`, `whale_need_encryption_key_forced_time`) + Whale-forked `os_crypt_whale.cc`/`wbc_wrapper_apis.cc` + `xv10` magic CONFIRMED present in v4.38.386.14 binary via prior bigpickle/laguna runs — KDF constants/iteration counts + master-key storage location remain unextracted (stale until re-acquired)
[LEARN] REJECTED GitHub wiki raw access @ raw.githubusercontent.com/wiki/naver/whale-browser-developers/sidebarAction.md: returns HTTP 404 — the `whale.sidebarAction` wiki documentation previously cited as "CONFIRMED accessible" is no longer reachable; wiki API also 404s
[LEARN] CONFIRMED sample extension manifest @ raw.githubusercontent.com/naver/whale-browser-developers/translate/src/sidebar-sample/manifest.json: still HTTP 200 — `content_scripts` matching `http://*/*` + `https://*/*` (ALL origins) confirmed live on the translate branch
[LEARN] CONFIRMED @ CloudFront CDN DNS: `d1vdt4q2qgdbji.cloudfront.net` + `cloudfront.net` both resolve `No answer` at sandbox resolver (127.0.0.53); google.com/github.com/nvd.nist.gov resolve normally — binary acquisition via cloudfront impossible in-sandbox
[LEARN] ACCEPTED @ all leads remain INVALID per last triage run: 0/14 hypotheses passed Q4 (passive proof) — sidebar boundary and sync KDF leads require HUMAN_ONLY browser install + binary extraction; all version-drift/MISCONFIG leads require binary acquisition now blocked
[RISK] sync: 65 — custom `/whalesync` + per-account bootstrap tokens + server-tweakable Multiplay exclusion + Whale-only NID OAuth all confirmed in latest binary; key-storage/KDF and reset-auth not yet runtime-verified; server-tweakable client heuristics widen the sync attack surface | browser: 78 — sidebar/dual-tab (6 CVEs in 2025) remain 3 minor versions past last fix with zero 2026 CVEs; binary now available enables repro-first variant hunting | libs: 35 — paks now parseable for a bundled-lib inventory, and Whale-only `socket.io.slim.js` is confirmed bundled (content runtime-fetched); version-drift assessment still requires upstream comparison
## 2026-08-08 13:12:10 UTC [browser] (model nemotron3)
[NEW] NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 12:07:00 UTC); no new public CVEs, no new GitHub commits, no new wiki edits, all binary acquisition paths remain blocked in-sandbox
[PRIO] Whale sync bootstrap-token envelope (OSCrypt deviation on Linux, v4.38.386.14), 6.55, atk=7 biz=8 tech=9 gate=2 cloud=3 fresh=8
[PRIO] Whale sync passphrase KDF + bootstrap-token envelope (desktop v4.38.386.14 + Android 3.9.14.9), 6.45, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7
[PRIO] Whale sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 6.30, atk=7 biz=8 tech=6 gate=2 cloud=3 fresh=8
[PRIO] Whale dual-tab environment on v4.38.386.14, 6.15, atk=7 biz=7 tech=6 gate=2 cloud=3 fresh=6
[PRIO] Bundled socket.io.slim.js in resources.pak (Whale-only), 4.85, atk=5 biz=4 tech=6 gate=8 cloud=2 fresh=4
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs sync.encryption_bootstrap_token_per_account
confidence: 62
reasoning: v4.38.386.14 binary confirms Whale-only prefs keys (sync.encryption_bootstrap_token_per_account, _migration_done, whale_need_encryption_key_forced_time) + Whale-forked OSCrypt (os_crypt_whale.cc, wbc_wrapper_apis.cc, xv10 magic); /whalesync authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list; per-account bootstrap token envelope deviates from upstream Chromium sync.encryption_bootstrap_token
evidence_needed: Per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where os_crypt_whale stores master key on Linux; whether whale_need_encryption_key_forced_time downgrades to stale key
verify_steps: PASSIVE: objdump/strings on os_crypt_whale + whale_sync_util call sites for bootstrap-token envelope and /whalesync/reset request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: Local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise (High)
testability: PASSIVE
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9)
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: whale.sidebarAction.show({url}) sidebar panel loader
confidence: 60
reasoning: CVE-2025-69235 (CWE-346) fixed in v4.35.351.12; v4.38.386.14 is 3 minor bumps ahead with 0 CVEs. Sample extension (translate branch) content_scripts match ALL origins (http://*/*, https://*/*); background.js calls whale.sidebarAction.show() + whale.windows.create() without origin validation; sidebar context detected via navigator.userAgent.includes('sidebar')
evidence_needed: Running browser v4.38.386.14 demonstrating cross-origin data access from sidebar context via show({url:'https://victim.com'}) or drag-drop navigation bypass with use_navigation_bar:false
verify_steps: HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://httpbin.org/headers'}) → attempt cross-origin fetch from panel content script → test drag-drop with use_navigation_bar:false
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation (Critical)
testability: HUMAN_ONLY
[PARKED] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14: confidence 60 but testability HUMAN_ONLY with no passive-first verification path — requires binary install and manual sidebar interaction, cannot be validated statically
[FINAL] 1. Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux (confidence 62, class AUTH, testability PASSIVE)
[FINAL] 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: objdump/strings on `os_crypt_whale` + `whale_sync_util` call sites for the bootstrap-token envelope and `/whalesync/reset` request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network — local static analysis of acquired binary only
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[LEARN] CONFIRMED @ NVD: 0 CVEs published for Whale in 2026 — no public disclosures exist for versions 4.35.352 through 4.38.386, confirming a 6-month vulnerability disclosure gap
[LEARN] REJECTED binary acquisition @ APKMirror/APKCombo/apkpure/cloudfront CDN: All download paths blocked in-sandbox (cloudfront DNS `No answer`; APKMirror 403; uptodown 410; apkpure.com 403; Naver domains excluded) — binary acquisition requires HUMAN with unrestricted internet access
[LEARN] ACCEPTED @ binary static analysis: Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account` sha256=`7b06e6e9...`, `_migration_done`, `whale_need_encryption_key_forced_time`) + Whale-forked `os_crypt_whale.cc`/`wbc_wrapper_apis.cc` + `xv10` magic CONFIRMED present in v4.38.386.14 binary via prior bigpickle/laguna runs — KDF constants/iteration counts + master-key storage location remain unextracted (stale until re-acquired)
[LEARN] REJECTED GitHub wiki raw access @ raw.githubusercontent.com/wiki/naver/whale-browser-developers/sidebarAction.md: returns HTTP 404 — the `whale.sidebarAction` wiki documentation previously cited as "CONFIRMED accessible" is no longer reachable; wiki API also 404s
[LEARN] CONFIRMED sample extension manifest @ raw.githubusercontent.com/naver/whale-browser-developers/translate/src/sidebar-sample/manifest.json: still HTTP 200 — `content_scripts` matching `http://*/*` + `https://*/*` (ALL origins) confirmed live on the translate branch
[RISK] sync: 65 — custom `/whalesync` + per-account bootstrap tokens + server-tweakable Multiplay exclusion + Whale-only NID OAuth all confirmed in latest binary; key-storage/KDF and reset-auth not yet runtime-verified; server-tweakable client heuristics widen the sync attack surface | browser: 78 — sidebar/dual-tab (6 CVEs in 2025) remain 3 minor versions past last fix with zero 2026 CVEs; binary now available enables repro-first variant hunting | libs: 35 — paks now parseable for a bundled-lib inventory, and Whale-only `socket.io.slim.js` is confirmed bundled (content runtime-fetched); version-drift assessment still requires upstream comparison
## 2026-08-08 13:58:59 UTC [browser] (model nemotron3)
[NEW] NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 13:16:20 UTC); no new public CVEs, no new GitHub commits, no new wiki edits, all binary acquisition paths remain blocked in-sandbox
[PRIO] Whale sync bootstrap-token envelope (OSCrypt deviation on Linux, v4.38.386.14), 6.55, atk=7 biz=8 tech=9 gate=2 cloud=3 fresh=8
[PRIO] Whale sync passphrase KDF + bootstrap-token envelope (desktop v4.38.386.14 + Android 3.9.14.9), 6.45, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7
[PRIO] Whale sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 6.30, atk=7 biz=8 tech=6 gate=2 cloud=3 fresh=8
[PRIO] Whale dual-tab environment on v4.38.386.14, 6.15, atk=7 biz=7 tech=6 gate=2 cloud=3 fresh=6
[PRIO] Bundled socket.io.slim.js in resources.pak (Whale-only), 4.85, atk=5 biz=4 tech=6 gate=8 cloud=2 fresh=4
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs sync.encryption_bootstrap_token_per_account
confidence: 62
reasoning: v4.38.386.14 binary confirms Whale-only prefs keys (sync.encryption_bootstrap_token_per_account, _migration_done, whale_need_encryption_key_forced_time) + Whale-forked OSCrypt (os_crypt_whale.cc, wbc_wrapper_apis.cc, xv10 magic); /whalesync authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list; per-account bootstrap token envelope deviates from upstream Chromium sync.encryption_bootstrap_token
evidence_needed: Per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where os_crypt_whale stores master key on Linux; whether whale_need_encryption_key_forced_time downgrades to stale key
verify_steps: PASSIVE: objdump/strings on os_crypt_whale + whale_sync_util call sites for bootstrap-token envelope and /whalesync/reset request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: Local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise (High)
testability: PASSIVE
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9)
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: whale.sidebarAction.show({url}) sidebar panel loader
confidence: 60
reasoning: CVE-2025-69235 (CWE-346) fixed in v4.35.351.12; v4.38.386.14 is 3 minor bumps ahead with 0 CVEs. Sample extension (translate branch) content_scripts match ALL origins (http://*/*, https://*/*); background.js calls whale.sidebarAction.show() + whale.windows.create() without origin validation; sidebar context detected via navigator.userAgent.includes('sidebar')
evidence_needed: Running browser v4.38.386.14 demonstrating cross-origin data access from sidebar context via show({url:'https://victim.com'}) or drag-drop navigation bypass with use_navigation_bar:false
verify_steps: HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://httpbin.org/headers'}) → attempt cross-origin fetch from panel content script → test drag-drop with use_navigation_bar:false
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation (Critical)
testability: HUMAN_ONLY
[PARKED] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14: confidence 60 but testability HUMAN_ONLY with no passive-first verification path — requires binary install and manual sidebar interaction, cannot be validated statically
[FINAL] 1. Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux (confidence 62, class AUTH, testability PASSIVE)
[FINAL] 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: objdump/strings on `os_crypt_whale` + `whale_sync_util` call sites for the bootstrap-token envelope and `/whalesync/reset` request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network — local static analysis of acquired binary only
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[LEARN] CONFIRMED @ NVD: 0 CVEs published for Whale in 2026 — no public disclosures exist for versions 4.35.352 through 4.38.386, confirming a 6-month vulnerability disclosure gap
[LEARN] REJECTED binary acquisition @ APKMirror/APKCombo/apkpure/cloudfront CDN: All download paths blocked in-sandbox (cloudfront DNS `No answer`; APKMirror 403; uptodown 410; apkpure.com 403; Naver domains excluded) — binary acquisition requires HUMAN with unrestricted internet access
[LEARN] ACCEPTED @ binary static analysis: Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account` sha256=`7b06e6e9...`, `_migration_done`, `whale_need_encryption_key_forced_time`) + Whale-forked `os_crypt_whale.cc`/`wbc_wrapper_apis.cc` + `xv10` magic CONFIRMED present in v4.38.386.14 binary via prior bigpickle/laguna runs — KDF constants/iteration counts + master-key storage location remain unextracted (stale until re-acquired)
[LEARN] REJECTED GitHub wiki raw access @ raw.githubusercontent.com/wiki/naver/whale-browser-developers/sidebarAction.md: returns HTTP 404 — the `whale.sidebarAction` wiki documentation previously cited as "CONFIRMED accessible" is no longer reachable; wiki API also 404s
[LEARN] CONFIRMED sample extension manifest @ raw.githubusercontent.com/naver/whale-browser-developers/translate/src/sidebar-sample/manifest.json: still HTTP 200 — `content_scripts` matching `http://*/*` + `https://*/*` (ALL origins) confirmed live on the translate branch
[RISK] sync: 65 — custom `/whalesync` + per-account bootstrap tokens + server-tweakable Multiplay exclusion + Whale-only NID OAuth all confirmed in latest binary; key-storage/KDF and reset-auth not yet runtime-verified; server-tweakable client heuristics widen the sync attack surface | browser: 78 — sidebar/dual-tab (6 CVEs in 2025) remain 3 minor versions past last fix with zero 2026 CVEs; binary now available enables repro-first variant hunting | libs: 35 — paks now parseable for a bundled-lib inventory, and Whale-only `socket.io.slim.js` is confirmed bundled (content runtime-fetched); version-drift assessment still requires upstream comparison
## 2026-08-08 14:37:24 UTC [browser] (model nemotron3)
[NEW] NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 13:59:13 UTC); no new public CVEs, no new GitHub commits, no new wiki edits, all binary acquisition paths remain blocked in-sandbox
[PRIO] Whale sync bootstrap-token envelope (OSCrypt deviation on Linux, v4.38.386.14), 6.55, atk=7 biz=8 tech=9 gate=2 cloud=3 fresh=8
[PRIO] Whale sync passphrase KDF + bootstrap-token envelope (desktop v4.38.386.14 + Android 3.9.14.9), 6.45, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7
[PRIO] Whale sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 6.30, atk=7 biz=8 tech=6 gate=2 cloud=3 fresh=8
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs sync.encryption_bootstrap_token_per_account sha256=053ffa4b...
confidence: 62
reasoning: v4.38.386.14 binary confirms Whale-only prefs keys (sync.encryption_bootstrap_token_per_account, _migration_done, whale_need_encryption_key_forced_time) + Whale-forked OSCrypt (os_crypt_whale.cc, wbc_wrapper_apis.cc, xv10 magic); /whalesync authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list; per-account bootstrap token envelope deviates from upstream Chromium sync.encryption_bootstrap_token
evidence_needed: Per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where os_crypt_whale stores master key on Linux; whether whale_need_encryption_key_forced_time downgrades to stale key
verify_steps: PASSIVE: objdump/strings on os_crypt_whale + whale_sync_util call sites for bootstrap-token envelope and /whalesync/reset request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: Local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise (High)
testability: PASSIVE
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9)
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: whale.sidebarAction.show({url}) sidebar panel loader
confidence: 60
reasoning: CVE-2025-69235 (CWE-346) fixed in v4.35.351.12; v4.38.386.14 is 3 minor bumps ahead with 0 CVEs. Sample extension (translate branch) content_scripts match ALL origins (http://*/*, https://*/*); background.js calls whale.sidebarAction.show() + whale.windows.create() without origin validation; sidebar context detected via navigator.userAgent.includes('sidebar')
evidence_needed: Running browser v4.38.386.14 demonstrating cross-origin data access from sidebar context via show({url:'https://victim.com'}) or drag-drop navigation bypass with use_navigation_bar:false
verify_steps: HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://httpbin.org/headers'}) → attempt cross-origin fetch from panel content script → test drag-drop with use_navigation_bar:false
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation (Critical)
testability: HUMAN_ONLY
[PARKED] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14: confidence 60 but testability HUMAN_ONLY with no passive-first verification path — requires binary install and manual sidebar interaction, cannot be validated statically
[FINAL] 1. Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux (confidence 62, class AUTH, testability PASSIVE)
[FINAL] 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: objdump/strings on `os_crypt_whale` + `whale_sync_util` call sites for the bootstrap-token envelope and `/whalesync/reset` request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network — local static analysis of acquired binary only
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[LEARN] CONFIRMED @ NVD: 0 CVEs published for Whale in 2026 — no public disclosures exist for versions 4.35.352 through 4.38.386, confirming a 6-month vulnerability disclosure gap
[LEARN] REJECTED binary acquisition @ APKMirror/APKCombo/apkpure/cloudfront CDN: All download paths blocked in-sandbox (cloudfront DNS `No answer`; APKMirror 403; uptodown 410; apkpure.com 403; Naver domains excluded) — binary acquisition requires HUMAN with unrestricted internet access
[LEARN] ACCEPTED @ binary static analysis: Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account` sha256=053ffa4b..., `_migration_done`, `whale_need_encryption_key_forced_time`) + Whale-forked `os_crypt_whale.cc`/`wbc_wrapper_apis.cc` + `xv10` magic CONFIRMED present in v4.38.386.14 binary via prior bigpickle/laguna runs — KDF constants/iteration counts + master-key storage location remain unextracted (stale until re-acquired)
[LEARN] REJECTED GitHub wiki raw access @ raw.githubusercontent.com/wiki/naver/whale-browser-developers/sidebarAction.md: returns HTTP 404 — the `whale.sidebarAction` wiki documentation previously cited as "CONFIRMED accessible" is no longer reachable; wiki API also 404s
[LEARN] CONFIRMED sample extension manifest @ raw.githubusercontent.com/naver/whale-browser-developers/translate/src/sidebar-sample/manifest.json: still HTTP 200 — `content_scripts` matching `http://*/*` + `https://*/*` (ALL origins) confirmed live on the translate branch
[RISK] sync: 65 — custom `/whalesync` + per-account bootstrap tokens + server-tweakable Multiplay exclusion + Whale-only NID OAuth all confirmed in latest binary; key-storage/KDF and reset-auth not yet runtime-verified; server-tweakable client heuristics widen the sync attack surface | browser: 78 — sidebar/dual-tab (6 CVEs in 2025) remain 3 minor versions past last fix with zero 2026 CVEs; binary now available enables repro-first variant hunting | libs: 35 — paks now parseable for a bundled-lib inventory, and Whale-only `socket.io.slim.js` is confirmed bundled (content runtime-fetched); version-drift assessment still requires upstream comparison
## 2026-08-08 15:06:21 UTC [browser] (model nemotron3)
[PRIO] Whale sync bootstrap-token envelope (OSCrypt deviation on Linux, v4.38.386.14), 6.55, atk=7 biz=8 tech=9 gate=2 cloud=3 fresh=8
[PRIO] Whale sidebar/dual-tab boundary (sidebarAction.show + ALL-origin content_scripts, v4.38.386.14 Linux), 6.30, atk=7 biz=8 tech=8 gate=2 cloud=3 fresh=10
[PRIO] Whale sync passphrase KDF + bootstrap-token envelope (desktop v4.38.386.14 + Android 3.9.14.9), 6.15, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs sync.encryption_bootstrap_token_per_account sha256=053ffa4b...
confidence: 62
reasoning: v4.38.386.14 binary confirms Whale-only prefs keys (sync.encryption_bootstrap_token_per_account, _migration_done, whale_need_encryption_key_forced_time) + Whale-forked OSCrypt (os_crypt_whale.cc, wbc_wrapper_apis.cc, xv10 magic); /whalesync authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list; per-account bootstrap token envelope deviates from upstream Chromium sync.encryption_bootstrap_token
evidence_needed: Per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where os_crypt_whale stores master key on Linux; whether whale_need_encryption_key_forced_time downgrades to stale key
verify_steps: PASSIVE: objdump/strings on os_crypt_whale + whale_sync_util call sites for bootstrap-token envelope and /whalesync/reset request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: Local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise (High)
testability: PASSIVE
[HYP] Sidebar/dual-tab boundary regression — CVE-2025-69235 fix omits Linux
class: OTHER
asset: Whale v4.38.386.14 desktop Linux sidebar (sidebarAction.show({url})) + dual-tab web panel
confidence: 58
reasoning: CVE-2025-69235 (CWE-346) NVD CPE lists only Windows/macOS — Linux absent. v4.38.386.14 is 3 minor-version bumps past the fix with 0 published CVEs. Sample extension translate/src/sidebar-sample/manifest.json (HTTP 200) confirms content_scripts match ALL origins and background.js calls sidebarAction.show() without origin validation.
evidence_needed: Script execution or cross-origin fetch in sidebar/dual-tab panel on Linux; window.opener/parent readback from foreign origin; iframe sandbox escape; CSP bypass via non-http(s) scheme
verify_steps: HUMAN_ONLY: Install Whale v4.38.386.14 on Linux → load sidebar-sample extension → whale.sidebarAction.show({url:'https://attacker.com/exploit.html'}) → attempt cross-origin fetch from panel content script → test drag-drop with use_navigation_bar:false → check opener/parent readback. Zero requests to Naver infra.
impact: SOP bypass / script execution in privileged browser-UI context — cross-origin cookie/DOM/CSRF-token theft; Critical if escalates to renderer code execution
testability: HUMAN_ONLY
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9)
confidence: 55
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. Whale-only prefs keys + xv10-magic OSCrypt fork + /whalesync confirmed in prior binary runs. KDF alg/iteration count never extracted.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: AUTH_HELPED: Acquire latest desktop installer and Android XAPK 3.9.14.9 from non-Naver mirror; extract/decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra. AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[PARKED] Whale sync passphrase KDF weakness / plaintext-adjacent key storage: confidence 55 < 60 threshold, and KDF constants/iteration counts + master-key storage location remain unextracted — verify_steps limited to AUTH_HELPED; no concrete passive-first HTTP probe available
[FINAL] 1. Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux (confidence 62, class AUTH, testability PASSIVE)
[FINAL] 2. Sidebar/dual-tab boundary regression — CVE-2025-69235 fix omits Linux (confidence 58, class OTHER, testability HUMAN_ONLY)
[NEXT] PROBE: objdump/strings on `os_crypt_whale` + `whale_sync_util` call sites for the bootstrap-token envelope and `/whalesync/reset` request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network — local static analysis of acquired binary only
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] REJECTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[LEARN] CONFIRMED @ NVD: 0 Whale CVEs published in 2026 — no public disclosures exist for versions 4.35.352 through 4.38.386, confirming a 8-month vulnerability disclosure gap
[LEARN] REJECTED binary acquisition @ APKMirror/APKCombo/apkpure/cloudfront CDN: All download paths blocked in-sandbox (cloudfront DNS `No answer`; APKMirror 403; uptodown 404 page removed; apkpure.com 403; Naver domains excluded) — binary acquisition requires HUMAN with unrestricted internet access
[LEARN] ACCEPTED @ binary static analysis: Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account` sha256=053ffa4b..., `_migration_done`, `whale_need_encryption_key_forced_time`) + Whale-forked `os_crypt_whale.cc`/`wbc_wrapper_apis.cc` + `xv10` magic CONFIRMED present in v4.38.386.14 binary via prior bigpickle/laguna runs — KDF constants/iteration counts + master-key storage location remain unextracted (stale until re-acquired)
[LEARN] REJECTED GitHub wiki raw access @ raw.githubusercontent.com/wiki/naver/whale-browser-developers/sidebarAction.md: returns HTTP 404 — the `whale.sidebarAction` wiki documentation previously cited as "CONFIRMED accessible" is no longer reachable; wiki API also 404s
[LEARN] CONFIRMED sample extension manifest @ raw.githubusercontent.com/naver/whale-browser-developers/translate/src/sidebar-sample/manifest.json: still HTTP 200 — `content_scripts` matching `http://*/*` + `https://*/*` (ALL origins) confirmed live on the translate branch
[RISK] sync: 65 — custom `/whalesync` + per-account bootstrap tokens + server-tweakable Multiplay exclusion + Whale-only NID OAuth all confirmed in latest binary; key-storage/KDF and reset-auth not yet runtime-verified; server-tweakable client heuristics widen the sync attack surface | browser: 82 — sidebar/dual-tab (6 CVEs in 2025) remain 3 minor versions past last fix with zero 2026 CVEs; Linux absent from CVE-2025-69235 fix CPE; sample extension manifest (HTTP 200) confirms ALL-origin content_scripts + unvalidated sidebarAction.show() — high-priority regression window unchanged | libs: 35 — Whale bundles Chromium (inherits upstream CVEs); socket.io.slim.js confirmed Whale-only in resources.pak but version-drift audit blocked (binary inaccessible); no public third-party library manifest; passive lib-inventory path degraded to speculative; low visibility, moderate inherent risk
