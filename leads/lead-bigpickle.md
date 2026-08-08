# LEADS bigpickle (seed)
- SEED: no model output yet; pipeline starts on first run.
## 2026-08-07 18:31:54 UTC [sync] (model bigpickle)
[HYP] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9)
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase is never sent to/stored on the Naver server and must be re-entered on every new device → client-side key derivation + local key store. Android only added sync encryption in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists (org scan), so binary must be analyzed statically.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: acquire latest desktop installer and Android XAPK 3.9.14.9 (apkpure blocked curl 403; try APKMirror or JS-resolved official link), extract/decompile, grep sync module for passphrase/scrypt/PBKDF2 constants, key-store paths, sync hostnames, bearer/OAuth token handling. AUTH_HELPED: authorized test login to observe token/key lifecycle in filesystem. Zero requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade; High
testability: AUTH_HELPED
[HYP] Security-boundary enforcement in Whale-only sidebar/dual-tab environments on latest desktop (variant hunting)
class: OTHER
asset: Latest desktop Whale (>=4.35.351.12) sidebar and dual-tab web panels
confidence: 45
reasoning: 2025-26 CVE family shows sidebar/dual-tab (Whale-only) repeatedly broke SOP, iframe sandbox, CSP (CVE-2025-69234/5, 62583/4/5, 53600), each fixed a release later; recurrence across 3 releases signals a systemic boundary weak spot; adjacent features (mobile window, quicksearch, scrapbook) may carry variants.
evidence_needed: crafted HTML that escapes iframe sandbox or bypasses SOP/CSP inside sidebar/dual-tab web panel on the LATEST build
verify_steps: AUTH_HELPED: install latest desktop Whale, open crafted HTML in sidebar and dual-tab web panels, test sandbox escape / cross-origin read / CSP bypass; repro-first on latest build; no server interaction.
impact: sandbox escape / SOP bypass from a webpage → arbitrary script execution or cross-origin data theft in browser UI; Critical if escalates to renderer code execution
testability: AUTH_HELPED
[HYP] Whale-only bundled third-party libs: version drift vs upstream with known CVEs
class: MISCONFIG
asset: Whale desktop/mobile bundled third-party libs (inventory not yet built)
confidence: 35
reasoning: historical installers/extension-store bugs (CVE-2018-12449, CVE-2022-2407x); no current inventory; only generic diff-against-upstream verify path
evidence_needed: bundled lib manifest + upstream version comparison
verify_steps: PASSIVE: build inventory from extracted binary, compare versions to upstream known-CVE tables
impact: outdated lib with public exploit → local/remote compromise; Medium-High
testability: PASSIVE
## 2026-08-07 18:49:48 UTC [sync] (model bigpickle)
## 2026-08-07 20:05:01 UTC [sync] (model bigpickle)
[HYP] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9)
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase is never sent to/stored on the Naver server and must be re-entered on every new device → client-side key derivation + local key store. Android only added sync encryption in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists (org scan), so binary must be analyzed statically.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: acquire latest desktop installer and Android XAPK 3.9.14.9 (apkpure blocked curl 403; try APKMirror or JS-resolved official link), extract/decompile, grep sync module for passphrase/scrypt/PBKDF2 constants, key-store paths, sync hostnames, bearer/OAuth token handling. AUTH_HELPED: authorized test login to observe token/key lifecycle in filesystem. Zero requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade; High
testability: AUTH_HELPED
[HYP] Security-boundary enforcement in Whale-only sidebar/dual-tab environments on latest desktop (variant hunting)
class: OTHER
asset: Latest desktop Whale (>=4.35.351.12) sidebar and dual-tab web panels
confidence: 45
reasoning: 2025-26 CVE family shows sidebar/dual-tab (Whale-only) repeatedly broke SOP, iframe sandbox, CSP (CVE-2025-69234/5, 62583/4/5, 53600), each fixed a release later; recurrence across 3 releases signals a systemic boundary weak spot; adjacent features (mobile window, quicksearch, scrapbook) may carry variants.
evidence_needed: crafted HTML that escapes iframe sandbox or bypasses SOP/CSP inside sidebar/dual-tab web panel on the LATEST build
verify_steps: AUTH_HELPED: install latest desktop Whale, open crafted HTML in sidebar and dual-tab web panels, test sandbox escape / cross-origin read / CSP bypass; repro-first on latest build; no server interaction.
impact: sandbox escape / SOP bypass from a webpage → arbitrary script execution or cross-origin data theft in browser UI; Critical if escalates to renderer code execution
testability: AUTH_HELPED
[HYP] Whale-only bundled third-party libs: version drift vs upstream with known CVEs
class: MISCONFIG
asset: Whale desktop/mobile bundled third-party libs (inventory not yet built)
confidence: 35
reasoning: historical installers/extension-store bugs (CVE-2018-12449, CVE-2022-2407x); no current inventory; only generic diff-against-upstream verify path
evidence_needed: bundled lib manifest + upstream version comparison
verify_steps: PASSIVE: build inventory from extracted binary, compare versions to upstream known-CVE tables
impact: outdated lib with public exploit → local/remote compromise; Medium-High
testability: PASSIVE
[NEW] 2026-08-07 REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope
[NEW] 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[NEW] 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[NEW] 2026-08-07 ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[NEW] 2026-08-07 REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[NEW] 2026-08-07 REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[NEW] 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest stable v4.38.386.14 has 3 minor version bumps with zero published CVEs — regression or new variant possible
[NEW] 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358, CSP bypass via specific scheme) confirmed fixed in v4.33.325.17 (Oct 2025); latest v4.38.386.14 is ~8 months ahead, no new CVEs published
[NEW] 2026-08-07 CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system metadata refresh, no code changes
[NEW] 2026-08-07 CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowledge gap for current-version vulnerability discovery
[CHANGED] 2026-08-07 Latest known affected version from CVEs: 4.35.351.12 (CVE-2025-69234/69235, Dec 2025); no 2026 CVEs published yet — version increment to v4.38.386.14; 3 minor version bumps since last CVE-fix version
[PRIO] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14, score: 6.8 (attack_surface:8, business_value:9, tech_exposure:7, gate_ease:2, cloud_surface:3, freshness:9)
[PRIO] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9), score: 6.1 (attack_surface:6, business_value:8, tech_exposure:7, gate_ease:3, cloud_surface:4, freshness:7)
[PRIO] Whale-only bundled libs: version drift vs upstream with known CVEs, score: 5.85 (attack_surface:7, business_value:4, tech_exposure:8, gate_ease:6, cloud_surface:2, freshness:8)
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: XSS
asset: sidebarAction.show URL loading
confidence: 60
reasoning: Wiki whale.sidebarAction docs reveal `show()` accepts a `url` parameter; `use_navigation_bar` defaults true but when false drag events navigate to other websites; CVE-2025-69235 confirmed SOP bypass, new variant post-CVE-2025-69235 on v4.38.386.14 (3 minor version bumps, zero published CVEs)
evidence_needed: sidebarAction.show URL loading, use_navigation_bar behavior, extension API docs
verify_steps: PASSIVE: Inspect sidebarAction.show URL loading in extension manifest; check `use_navigation_bar` defaults
impact: context isolation bypass, user tracking via drag events, privacy violation
testability: PASSIVE
[HYP] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9)
class: AUTH
asset: whale-sync client (internal sync endpoint)
confidence: 55
reasoning: Reports/hypotheses-bigpickle.txt detail passphrase/key-derivation and local key-storage design for desktop and Android 3.9.14.9; static analysis of whale-sync client code available
evidence_needed: whale-sync client source code, key storage mechanisms
verify_steps: AUTH_HELPED: Inspect whale-sync client for key derivation functions
impact: credential exposure, account compromise
testability: AUTH_HELPED
[HYP] Whale-only bundled libs: version drift vs upstream with known CVEs
class: MISCONFIG
asset: whale-only third-party libraries
confidence: 45
reasoning: Inventory confirms bundled third-party libs; diff against upstream reveals version drift; no published CVEs in latest version v4.38.386.14 but gaps in 2026
evidence_needed: bundled library manifests, upstream comparison results
verify_steps: PASSIVE: Diff bundled library versions against upstream
impact: potential zero-day in bundled library, upgrade risk
testability: PASSIVE
[PARKED] Whale-only bundled libs: version drift vs upstream with known CVEs — limited static analysis availability (no bundled library manifests in whale-browser-developers repo); weak evidence_needed for verification
[FINAL]
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
[HYP] Scrapbook shared-category invite-link authorization + Multiplay session scoping
class: AUTH
asset: Whale sync data-plane — Scrapbook shared categories via invitation link; Multiplay real-time tab/scroll sharing (desktop v4.38.386.14)
confidence: 46
reasoning: v4.38 added "copy invite link without joining" (Multiplay) and Scrapbook shared categories "coming soon"; host's already-open logged-in tabs (URLs/DOM) are shared in real time; invite-token entropy/scope/revocation is undocumented and the browser is closed-source; program rewards sync bugs.
evidence_needed: on latest build, a second authorized account joining via invite link enumerates host's other open tab URLs/DOM or shares beyond the invited category; invite link replay/reuse after revocation
verify_steps: AUTH_HELPED: two-account session on v4.38.386.14; join via copied invite link; record what a joiner can observe (host tab URL list, scroll, DOM, page content) and whether links work after host revokes/leaves; capture session payloads file-local only; zero requests to naver sync infra
impact: cross-session disclosure of host's sensitive open-tab data (PII/session tokens) or shared-data theft beyond intended category; High
testability: AUTH_HELPED
[HYP] Sidebar boundary enforcement on latest — variant incl. DevTools-in-sidebar window
class: OTHER
asset: Whale sidebar web-panel + devtools-in-sidebar window (v4.38.386.14)
confidence: 50
reasoning: 6 Whale-only sidebar/dual-tab CVEs in 2025 (SOP, iframe-sandbox, CSP); 4.38.386.12 (2026-06-18) added Chrome Side Panel spec + DevTools-in-sidebar onto that exact surface; CVE-2022-24072 shows the devtools API is a past injection vector; $4k SOP reward tier.
evidence_needed: crafted HTML/web-panel escaping sidebar iframe sandbox or reading cross-origin data, or JS injection via the devtools-in-sidebar window, on LATEST build
verify_steps: AUTH_HELPED: install v4.38.386.14, load crafted extension/page into sidebar web-panel and devtools-in-sidebar; test sandbox escape / cross-origin XHR-fetch / CSP bypass; repro-first on latest; zero server interaction
impact: cross-origin data theft or script execution in browser UI; High–Critical (escalation to renderer)
testability: AUTH_HELPED
[HYP] Sync passphrase KDF / client key-storage design (desktop stub + Android)
class: AUTH
asset: Whale sync client key-derivation and local key-store (desktop; com.naver.whale Android 3.9.14.9) — naver infra, static analysis only
confidence: 42
reasoning: vendor help center: passphrase never sent/stored server-side, re-entered per device → client-side KDF + local key store; Android sync encryption added only 3.8.6.2 (2025-04); desktop installer is a 11.6MB stub (browser package fetched at install), Android APK blocked → full binary static analysis not yet possible.
evidence_needed: KDF algorithm + iteration counts, per-OS key/passphrase storage paths, whether key/token ever leaves device in reset/recovery flows
verify_steps: PASSIVE: download WhaleSetup.exe + beta, extract stub, inventory sync module strings/URLs; then acquire the versioned browser package the stub fetches at install time (same pstatic.net CDN, no naver web-service probing); grep for passphrase/PBKDF2/scrypt/sync/token constants. AUTH_HELPED: authorized test login to observe token/key filesystem lifecycle
impact: weak KDF or plaintext-adjacent key storage → local attacker/infostealer decrypts synced bookmarks+site passwords → PII cascade; High
testability: AUTH_HELPED
## 2026-08-07 20:57:31 UTC [sync] (model bigpickle)
[HYP] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9)
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase is never sent to/stored on the Naver server and must be re-entered on every new device → client-side key derivation + local key store. Android only added sync encryption in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists (org scan), so binary must be analyzed statically.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: acquire latest desktop installer and Android XAPK 3.9.14.9 (apkpure blocked curl 403; try APKMirror or JS-resolved official link), extract/decompile, grep sync module for passphrase/scrypt/PBKDF2 constants, key-store paths, sync hostnames, bearer/OAuth token handling. AUTH_HELPED: authorized test login to observe token/key lifecycle in filesystem. Zero requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade; High
testability: AUTH_HELPED
[HYP] Security-boundary enforcement in Whale-only sidebar/dual-tab environments on latest desktop (variant hunting)
class: OTHER
asset: Latest desktop Whale (>=4.35.351.12) sidebar and dual-tab web panels
confidence: 45
reasoning: 2025-26 CVE family shows sidebar/dual-tab (Whale-only) repeatedly broke SOP, iframe sandbox, CSP (CVE-2025-69234/5, 62583/4/5, 53600), each fixed a release later; recurrence across 3 releases signals a systemic boundary weak spot; adjacent features (mobile window, quicksearch, scrapbook) may carry variants.
evidence_needed: crafted HTML that escapes iframe sandbox or bypasses SOP/CSP inside sidebar/dual-tab web panel on the LATEST build
verify_steps: AUTH_HELPED: install latest desktop Whale, open crafted HTML in sidebar and dual-tab web panels, test sandbox escape / cross-origin read / CSP bypass; repro-first on latest build; no server interaction.
impact: sandbox escape / SOP bypass from a webpage → arbitrary script execution or cross-origin data theft in browser UI; Critical if escalates to renderer code execution
testability: AUTH_HELPED
[HYP] Whale-only bundled third-party libs: version drift vs upstream with known CVEs
class: MISCONFIG
asset: Whale desktop/mobile bundled third-party libs (inventory not yet built)
confidence: 35
reasoning: historical installers/extension-store bugs (CVE-2018-12449, CVE-2022-2407x); no current inventory; only generic diff-against-upstream verify path
evidence_needed: bundled lib manifest + upstream version comparison
verify_steps: PASSIVE: build inventory from extracted binary, compare versions to upstream known-CVE tables
impact: outdated lib with public exploit → local/remote compromise; Medium-High
testability: PASSIVE
[HYP] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9)
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase is never sent to/stored on the Naver server and must be re-entered on every new device → client-side key derivation + local key store. Android only added sync encryption in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists (org scan), so binary must be analyzed statically.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: acquire latest desktop installer and Android XAPK 3.9.14.9 (apkpure blocked curl 403; try APKMirror or JS-resolved official link), extract/decompile, grep sync module for passphrase/scrypt/PBKDF2 constants, key-store paths, sync hostnames, bearer/OAuth token handling. AUTH_HELPED: authorized test login to observe token/key lifecycle in filesystem. Zero requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade; High
testability: AUTH_HELPED
[HYP] Security-boundary enforcement in Whale-only sidebar/dual-tab environments on latest desktop (variant hunting)
class: OTHER
asset: Latest desktop Whale (>=4.35.351.12) sidebar and dual-tab web panels
confidence: 45
reasoning: 2025-26 CVE family shows sidebar/dual-tab (Whale-only) repeatedly broke SOP, iframe sandbox, CSP (CVE-2025-69234/5, 62583/4/5, 53600), each fixed a release later; recurrence across 3 releases signals a systemic boundary weak spot; adjacent features (mobile window, quicksearch, scrapbook) may carry variants.
evidence_needed: crafted HTML that escapes iframe sandbox or bypasses SOP/CSP inside sidebar/dual-tab web panel on the LATEST build
verify_steps: AUTH_HELPED: install latest desktop Whale, open crafted HTML in sidebar and dual-tab web panels, test sandbox escape / cross-origin read / CSP bypass; repro-first on latest build; no server interaction.
impact: sandbox escape / SOP bypass from a webpage → arbitrary script execution or cross-origin data theft in browser UI; Critical if escalates to renderer code execution
testability: AUTH_HELPED
[HYP] Whale-only bundled third-party libs: version drift vs upstream with known CVEs
class: MISCONFIG
asset: Whale desktop/mobile bundled third-party libs (inventory not yet built)
confidence: 35
reasoning: historical installers/extension-store bugs (CVE-2018-12449, CVE-2022-2407x); no current inventory; only generic diff-against-upstream verify path
evidence_needed: bundled lib manifest + upstream version comparison
verify_steps: PASSIVE: build inventory from extracted binary, compare versions to upstream known-CVE tables
impact: outdated lib with public exploit → local/remote compromise; Medium-High
testability: PASSIVE
[NEW] 2026-08-07 REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope
[NEW] 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[NEW] 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[NEW] 2026-08-07 ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[NEW] 2026-08-07 REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[NEW] 2026-08-07 REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[NEW] 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest stable v4.38.386.14 has 3 minor version bumps with zero published CVEs — regression or new variant possible
[NEW] 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358, CSP bypass via specific scheme) confirmed fixed in v4.33.325.17 (Oct 2025); latest v4.38.386.14 is ~8 months ahead, no new CVEs published
[NEW] 2026-08-07 CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system metadata refresh, no code changes
[NEW] 2026-08-07 CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowledge gap for current-version vulnerability discovery
[CHANGED] 2026-08-07 Latest known affected version from CVEs: 4.35.351.12 (CVE-2025-69234/69235, Dec 2025); no 2026 CVEs published yet — version increment to v4.38.386.14; 3 minor version bumps since last CVE-fix version
[PRIO] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14, score: 6.8 (attack_surface:8, business_value:9, tech_exposure:7, gate_ease:2, cloud_surface:3, freshness:9)
[PRIO] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9), score: 6.1 (attack_surface:6, business_value:8, tech_exposure:7, gate_ease:3, cloud_surface:4, freshness:7)
[PRIO] Whale-only bundled libs: version drift vs upstream with known CVEs, score: 5.85 (attack_surface:7, business_value:4, tech_exposure:8, gate_ease:6, cloud_surface:2, freshness:8)
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: XSS
asset: sidebarAction.show URL loading
confidence: 60
reasoning: Wiki whale.sidebarAction docs reveal `show()` accepts a `url` parameter; `use_navigation_bar` defaults true but when false drag events navigate to other websites; CVE-2025-69235 confirmed SOP bypass, new variant post-CVE-2025-69235 on v4.38.386.14 (3 minor version bumps, zero published CVEs)
evidence_needed: sidebarAction.show URL loading, use_navigation_bar behavior, extension API docs
verify_steps: PASSIVE: Inspect sidebarAction.show URL loading in extension manifest; check `use_navigation_bar` defaults
impact: context isolation bypass, user tracking via drag events, privacy violation
testability: PASSIVE
[HYP] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9)
class: AUTH
asset: whale-sync client (internal sync endpoint)
confidence: 55
reasoning: Reports/hypotheses-bigpickle.txt detail passphrase/key-derivation and local key-storage design for desktop and Android 3.9.14.9; static analysis of whale-sync client code available
evidence_needed: whale-sync client source code, key storage mechanisms
verify_steps: AUTH_HELPED: Inspect whale-sync client for key derivation functions
impact: credential exposure, account compromise
testability: AUTH_HELPED
[HYP] Whale-only bundled libs: version drift vs upstream with known CVEs
class: MISCONFIG
asset: whale-only third-party libraries
confidence: 45
reasoning: Inventory confirms bundled third-party libs; diff against upstream reveals version drift; no published CVEs in latest version v4.38.386.14 but gaps in 2026
evidence_needed: bundled library manifests, upstream comparison results
verify_steps: PASSIVE: Diff bundled library versions against upstream
impact: potential zero-day in bundled library, upgrade risk
testability: PASSIVE
[HYP] Multiplay session scoping — URL-only sharing boundary bypass (login-page exclusion, token-bearing URL sync)
class: AUTH
asset: Whale Multiplay session (invite link, real-time tab/URL/scroll sync, v4.38.386.14; help.whale.naver.com/ko/desktop/multiplay)
confidence: 50
reasoning: Vendor now documents the boundary: only URLs sync, participants render their own account's screen, login pages are auto-detected and excluded. The exclusion is a client-side heuristic and URLs are synced verbatim (incl. query strings); any participant can navigate a shared tab. Boundary is falsifiable on latest; invite-link scope/expiry still undocumented.
evidence_needed: on latest, whether a joiner receives host's excluded login-page URLs or token-bearing query strings (via tab list, URL sync, or "공유 받은 탭을 벗어났습니다" notifications); whether login-page exclusion is evadable by non-login pages carrying session params
verify_steps: AUTH_HELPED: two authorized accounts on v4.38.386.14; host opens a login-gated page and a page with a token in the query string; joiner joins via copied invite link; capture file-local what the joiner observes (tab URLs, scroll, DOM, notifications); test link reuse after host leaves space (auto-delete); zero requests to naver sync infra beyond the client's own session
impact: cross-session disclosure of host's open-tab URLs incl. token-bearing query strings / session identifiers; Medium-High
testability: AUTH_HELPED
[HYP] Sync passphrase KDF / client key-storage — Android-specific weakness (custom mobile impl)
class: AUTH
asset: Whale sync client key-derivation and local key-store (desktop; com.naver.whale Android 3.9.14.9) — naver infra, static analysis only
confidence: 55
reasoning: Primary sources (help.whale.naver.com ko/en): passphrase "will neither be sent to NAVER nor saved on its server", required per new device, reset flow deletes server data + logs out all devices → client-side KDF confirmed. Desktop likely inherits Chromium's audited sync-passphrase scrypt (fork), but Android only added "synchronization encryption" in 3.8.6.2 (2025-04) — a late, possibly custom mobile impl worth static analysis.
evidence_needed: KDF algorithm + iteration counts (esp. Android dex), per-OS key/passphrase storage (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key ever leaves device in reset/recovery flows
verify_steps: AUTH_HELPED: acquire Android XAPK 3.9.14.9 (apkpure/APKMirror both 403 to curl — needs alternate mirror) + desktop package; decompile; grep sync module for passphrase/scrypt/PBKDF2 constants and key-store paths; authorized login to observe token/key filesystem lifecycle; zero requests to naver sync infra
impact: weak KDF or plaintext-adjacent key storage → local attacker/infostealer decrypts synced bookmarks+site passwords → PII cascade; High
testability: AUTH_HELPED
[HYP] Sidebar boundary on latest — variant incl. DevTools-in-sidebar window
class: OTHER
asset: Whale sidebar web-panel + devtools-in-sidebar window (v4.38.386.14)
confidence: 50
reasoning: 6 Whale-only sidebar/dual-tab CVEs in 2025 (SOP, iframe-sandbox, CSP); v4.38.386.12 changelog confirms Chrome Side Panel spec + DevTools-in-sidebar shipped onto that exact surface; CVE-2022-24072 shows the devtools API is a past injection vector.
evidence_needed: crafted HTML/web-panel escaping sidebar iframe sandbox or reading cross-origin data, or JS injection via the devtools-in-sidebar window, on LATEST build
verify_steps: AUTH_HELPED: install v4.38.386.14, load crafted extension/page into sidebar web-panel and devtools-in-sidebar; test sandbox escape / cross-origin XHR-fetch / CSP bypass; repro-first on latest; zero server interaction
impact: cross-origin data theft or script execution in browser UI; High–Critical (escalation to renderer)
testability: AUTH_HELPED
## 2026-08-07 21:40:18 UTC [sync] (model bigpickle)
## 2026-08-07 22:21:50 UTC [sync] (model bigpickle)
[HYP] Sync bootstrap-token storage / per-account migration — Whale-only prefs deviation
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs `sync.encryption_bootstrap_token_per_account`
confidence: 55
reasoning: Binary v4.38.386.14 strings show Whale syncs passwords+cookies+autofill+tabs over custom `/whalesync`; encryption uses `nigori-key` + `sync_pb.EncryptionKeys` with Whale-only prefs keys (`_per_account`, `_migration_done`, `whale_need_encryption_key_forced_time`) absent from upstream Chromium; passphrase help page (help.whale.naver.com/desktop/sync_passphrase) and `SyncSetupSetEncryptionPassphrase` UI strings confirm custom passphrase is offered
evidence_needed: whether per-account token is stored encrypted vs plaintext in `Preferences`, passphrase KDF constants (salt/iterations) in whale_sync_util, whether `whale_need_encryption_key_forced_time` downgrades encryption
verify_steps: PASSIVE: ghidra/strings on `whale_sync_util.cc`/`whale_sync_auth_manager.cc` call sites for `/whalesync/reset` (method+body) and bootstrap-token envelope; compare prefs key set against upstream Chromium `sync.encryption_bootstrap_token` handling
impact: local attacker / infostealer with profile access decrypts synced passwords, cookies, bookmarks → full-account compromise; High
testability: PASSIVE
[HYP] whale_sync_push socket.io push channel — unsanitized event → engine mutation (injection precedent)
class: XSS
asset: whale_sync_push extension (service_worker.js + socket.io.slim.js), bundled Whale-only
confidence: 45
reasoning: CVE-2022-24072 / CVE-2024-40618 prove built-in-extension processing is a prior Whale injection vector; push channel is socket.io (unusual in browser core, Whale-only), and its events feed tab/typedUrls sync surfaces; payloads arriving via a WebSocket/engine.io transport create a trust boundary worth auditing
evidence_needed: onmessage/event handlers in the extracted service worker, whether remote events reach chrome.tabs/history or bypass message validation
verify_steps: PASSIVE: extract `whale_sync_push/*.js` from resources.pak; audit socket.io handlers for remote-origin event data reaching privileged APIs unsanitized
impact: remote push message mutating synced tabs/history or executing in extension context; Medium–High
testability: PASSIVE
[HYP] `/whalesync/reset` trigger — auth binding / confirmation weakness
class: BUSLOGIC
asset: api.whale.naver.com/whalesync/reset (client trigger = sync-wipe + log-out-all-devices per help doc)
confidence: 40
reasoning: client-initiated reset deletes server data and signs out every device; if the client sends only `NEO_SES` cookie (no reauth/confirm token), a stale/misused session or CSRFable request wipes sync state
evidence_needed: exact method, body, and headers the client sends to `/whalesync/reset`; whether reauth is required
verify_steps: PASSIVE: static call-site analysis of `/whalesync/reset` in `whale_sync_util` (request shape, auth headers); server probing out of scope
impact: sync data destruction, device log-out spam; DoS on account
testability: PASSIVE
[NEXT] PROBE: Extract `whale_sync_push/service_worker.js` + `socket.io.slim.js` from `/tmp/opencode/whale_x/opt/naver/whale/resources.pak` (local static), then audit socket.io onmessage handlers for remote event data reaching chrome.tabs/history APIs unsanitized — advances HYP-2 and the sync push surface with zero server interaction.
## 2026-08-07 23:05:37 UTC [sync] (model bigpickle)
[HYP] Sync bootstrap-token storage / per-account migration — Whale-only prefs deviation
class: AUTH
asset: whalesync client engine (api.whale.naver.com/whalesync) + profile prefs `sync.encryption_bootstrap_token_per_account`
confidence: 55
reasoning: Binary v4.38.386.14 strings show Whale syncs passwords+cookies+autofill+tabs over custom `/whalesync`; encryption uses `nigori-key` + `sync_pb.EncryptionKeys` with Whale-only prefs keys (`_per_account`, `_migration_done`, `whale_need_encryption_key_forced_time`) absent from upstream Chromium; passphrase help page (help.whale.naver.com/desktop/sync_passphrase) and `SyncSetupSetEncryptionPassphrase` UI strings confirm custom passphrase is offered
evidence_needed: whether per-account token is stored encrypted vs plaintext in `Preferences`, passphrase KDF constants (salt/iterations) in whale_sync_util, whether `whale_need_encryption_key_forced_time` downgrades encryption
verify_steps: PASSIVE: ghidra/strings on `whale_sync_util.cc`/`whale_sync_auth_manager.cc` call sites for `/whalesync/reset` (method+body) and bootstrap-token envelope; compare prefs key set against upstream Chromium `sync.encryption_bootstrap_token` handling
impact: local attacker / infostealer with profile access decrypts synced passwords, cookies, bookmarks → full-account compromise; High
testability: PASSIVE
[HYP] whale_sync_push socket.io push channel — unsanitized event → engine mutation (injection precedent)
class: XSS
asset: whale_sync_push extension (service_worker.js + socket.io.slim.js), bundled Whale-only
confidence: 45
reasoning: CVE-2022-24072 / CVE-2024-40618 prove built-in-extension processing is a prior Whale injection vector; push channel is socket.io (unusual in browser core, Whale-only), and its events feed tab/typedUrls sync surfaces; payloads arriving via a WebSocket/engine.io transport create a trust boundary worth auditing
evidence_needed: onmessage/event handlers in the extracted service worker, whether remote events reach chrome.tabs/history or bypass message validation
verify_steps: PASSIVE: extract `whale_sync_push/*.js` from resources.pak; audit socket.io handlers for remote-origin event data reaching privileged APIs unsanitized
impact: remote push message mutating synced tabs/history or executing in extension context; Medium–High
testability: PASSIVE
[HYP] `/whalesync/reset` trigger — auth binding / confirmation weakness
class: BUSLOGIC
asset: api.whale.naver.com/whalesync/reset (client trigger = sync-wipe + log-out-all-devices per help doc)
confidence: 40
reasoning: client-initiated reset deletes server data and signs out every device; if the client sends only `NEO_SES` cookie (no reauth/confirm token), a stale/misused session or CSRFable request wipes sync state
evidence_needed: exact method, body, and headers the client sends to `/whalesync/reset`; whether reauth is required
verify_steps: PASSIVE: static call-site analysis of `/whalesync/reset` in `whale_sync_util` (request shape, auth headers); server probing out of scope
impact: sync data destruction, device log-out spam; DoS on account
testability: PASSIVE
[NEXT] PROBE: Extract `whale_sync_push/service_worker.js` + `socket.io.slim.js` from `/tmp/opencode/whale_x/opt/naver/whale/resources.pak` (local static), then audit socket.io onmessage handlers for remote event data reaching chrome.tabs/history APIs unsanitized — advances HYP-2 and the sync push surface with zero server interaction.
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
[HYP] Sync bootstrap-token storage / per-account migration — Whale-only prefs deviation
class: AUTH
asset: whalesync client engine (`https://api.whale.naver.com/whalesync`) + profile `Preferences` keys `sync.encryption_bootstrap_token{,_per_account,_per_account_migration_done}`, `sync.whale_need_encryption_key_forced_time`
confidence: 62
reasoning: v4.38.386.14 binary (sha256 `6dd7ebc4...`) confirms the Whale-only prefs keys, `/whalesync`+`/whalesync/reset`, and `Sync.NigoriStorage{En,De}cryptionResult`/`sync_pb.EncryptionKeys` custom nigori storage; `sync.whale_need_encryption_key_forced_time` is a Whale-only rekey gate with undocumented downgrade semantics
evidence_needed: whether per-account bootstrap token is stored plaintext vs OSCrypt (`v10`/`v11` prefix) in `Preferences`, scrypt N/r/p constants reachable from `whale_sync_util`, whether `forced_time` weakens key freshness
verify_steps: PASSIVE: objdump/strings around `WhaleSyncAuthManager`/`whale_sync_util` for the bootstrap-token envelope and `/whalesync/reset` request shape; diff prefs set vs upstream Chromium `sync.encryption_bootstrap_token` (upstream stores OSCrypt-encrypted in same file); zero network
impact: local attacker/infostealer with profile access decrypts synced passwords, cookies, autofill → full-account compromise; High
testability: PASSIVE
[HYP] Multiplay login-page exclusion bypass — server-tweakable heuristic + token-bearing URL sync
class: AUTH
asset: Multiplay session (`https://multiplay.whale.naver.com/`, invite via `multiplayPrivate.getJoinUrl`, real-time tab/scroll sync via `multiplay_session_io`)
confidence: 58
reasoning: binary confirms exclusion list is server-fetched pref `whale.tweak.multiplay_login_pages` (ua_tweak.json), scroll sync injects DOM (`isWhaleMultiplayScroll`), join binds `sessionToken`+`multiplayId`; URLs sync verbatim (incl. query strings) while only listed login pages are filtered
evidence_needed: on v4.38.386.14 whether joiner receives host's login-gated URLs or token-bearing query strings; whether unlisted login-like pages (OAuth consent/SSO) leak session params; invite-link reuse after host leaves
verify_steps: AUTH_HELPED: two authorized accounts join via copied `getJoinUrl`; host opens login-gated page + token-in-query page; capture file-local joiner-observed tab URLs/scroll/DOM; test link replay post-revocation; zero requests beyond client's own session
impact: cross-session disclosure of host's open-tab URLs incl. token-bearing query strings / session ids; Medium-High
testability: AUTH_HELPED
[HYP] `/whalesync/reset` trigger — auth binding / confirmation weakness
class: BUSLOGIC
asset: `https://api.whale.naver.com/whalesync/reset` (client trigger = server data wipe + all-device logout)
confidence: 45
reasoning: `/whalesync/reset` confirmed in v4.38.386.14 binary; whale auth uses `/oauth2/v1/nid/{login,refresh}` tokens — if reset needs only a bearer/refresh token without reauth confirm, token theft or a CSRFable client request wipes sync state
evidence_needed: exact method, body, headers the client sends to `/whalesync/reset`; whether reauth/confirmation is required
verify_steps: PASSIVE: objdump call-site analysis of `/whalesync/reset` in the WhalesyncRequest/`whale_sync_util` path to recover request shape; server probing out of scope
impact: sync data destruction + all-device log-out on token theft; DoS on account
testability: PASSIVE
[NEXT] PROBE: objdump/strings disassembly of the `/whalesync/reset` + `sync.encryption_bootstrap_token_per_account` call sites in `/tmp/opencode/whale_x/extract/opt/naver/whale/whale` (sha256 `6dd7ebc4...`) to recover the reset request method/body/auth-header shape and the bootstrap-token envelope storage (plaintext vs OSCrypt `v10/v11`), plus enumerate remaining embedded extension API schemas (`multiplayPrivate`, sync-related) for invite/session token entropy — local static only, zero network
[RISK] sync: 65 — custom `/whalesync` + per-account bootstrap tokens + server-tweakable Multiplay exclusion + Whale-only NID OAuth all confirmed in latest binary; key-storage/KDF and reset-auth not yet runtime-verified; server-tweakable client heuristics widen the sync attack surface | browser: 78 — sidebar/dual-tab (6 CVEs in 2025) remain 3 minor versions past last fix with zero 2026 CVEs; binary now available enables repro-first variant hunting | libs: 35 — paks now parseable for a bundled-lib inventory, and Whale-only `socket.io.slim.js` is confirmed bundled (content runtime-fetched); version-drift assessment still requires upstream comparison
## 2026-08-07 23:49:29 UTC [sync] (model bigpickle)
[HYP] whale_sync_push socket.io push channel — server-controlled URL + unsanitized push message
class: XSS
asset: whale_sync_push component extension + utilityPrivate bridge (https://chat.whale.mu/) — Whale-only
confidence: 55
reasoning: binary embeds utilityPrivate.getPushServerURL (URL runtime-returned) + onPushUpdated(request_id,{message}); push_server_url_fetcher_base.cc logs "GetPushServerURL succeeded/failed"; service_worker.js+socket.io.slim.js are runtime-fetched, not in resources.pak; CVE-2022-24072/CVE-2024-40618 prove built-in-extension processing is a live Whale injection vector
evidence_needed: runtime push URL returned by getPushServerURL; whether socket.io message payload reaches chrome.tabs/history or extension internals unsanitized; whether URL-fetch endpoint is attacker-influenceable
verify_steps: AUTH_HELPED: authorized login → capture getPushServerURL response + runtime-fetched service_worker.js (file-local); audit socket.io onmessage handlers for unsanitized remote data reaching privileged APIs; zero out-of-scope probing
impact: remote push message mutating synced tabs/history or injection in extension context; Medium-High
testability: AUTH_HELPED
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: api.whale.naver.com/whalesync client engine + profile prefs sync.encryption_bootstrap_token_per_account{,_migration_done}, sync.whale_need_encryption_key_forced_time
confidence: 60
reasoning: strings confirm Whale-only prefs keys + Whale-forked OSCrypt (os_crypt_whale.cc, wbc_wrapper_apis.cc, `''xv10` magic); /whalesync authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list
evidence_needed: per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where os_crypt_whale stores master key on Linux; whether whale_need_encryption_key_forced_time downgrades to a stale key
verify_steps: PASSIVE: objdump/strings on os_crypt_whale + whale_sync_util call sites for the bootstrap-token envelope and /whalesync/reset request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise; High
testability: PASSIVE
[HYP] whaleonPrivate messaging channel — room/inviteCode/password authz
class: AUTH
asset: WhaleON messaging (whaleonPrivate.connectMessagingChannel{roomId,password,inviteCode,clientId}, pushMessage, onMessageReceived) — client static only
confidence: 45
reasoning: schema exposes inviteCode+password-bound room join and raw pushMessage{data,targets}+onMessageReceived{data}; real-time channel into browser; scope/entropy undocumented
evidence_needed: whether a replayed/guessed inviteCode or weak password joins a room and reads other participants' messages; whether pushMessage targets are validated
verify_steps: AUTH_HELPED: two authorized accounts join a study room; test inviteCode replay post-leave, empty password, cross-room message targeting; capture payloads file-local; zero requests beyond client's own session
impact: cross-session message disclosure or channel injection; Medium
testability: AUTH_HELPED
## 2026-08-08 00:36:11 UTC [sync] (model bigpickle)
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account; passphrase KDF/key-storage design entirely client-side with no public code; Android sync encryption added 2025-04 (late); moderate-high exposure due to source-unavailable constraint
[RISK] browser: 78 — sidebar + dual-tab environments have 6 confirmed CVEs in 2025 (SOP bypass CWE-346, iframe sandbox escape CWE-358, CSP bypass CWE-358); these are Whale-specific features not inherited from Chromium; latest stable v4.38.386.14 is 3 minor version bumps ahead of last CVE-fix v4.35.351.12 with ZERO published CVEs in 6-month gap; wiki docs confirm additional attack surface (show() URL loading, drag-navigation exposure); high priority for new variant discovery
[RISK] libs: 30 — Whale bundles Chromium (inherits all Chromium CVEs), but no Whale-only third-party library manifests or version lists are publicly available; the repo contains only translated documentation from 2019; the extension API surface (whale.*) uses CWE-79-inherited patterns but library-level version drift cannot be assessed without binary access; low visibility, moderate inherent risk
[HYP] Sync bootstrap-token storage / per-account migration — Whale-only prefs deviation
class: AUTH
asset: whalesync client engine (`https://api.whale.naver.com/whalesync`) + profile `Preferences` keys `sync.encryption_bootstrap_token{,_per_account,_per_account_migration_done}`, `sync.whale_need_encryption_key_forced_time`
confidence: 62
reasoning: v4.38.386.14 binary (sha256 `6dd7ebc4...`) confirms the Whale-only prefs keys, `/whalesync`+`/whalesync/reset`, and `Sync.NigoriStorage{En,De}cryptionResult`/`sync_pb.EncryptionKeys` custom nigori storage; `sync.whale_need_encryption_key_forced_time` is a Whale-only rekey gate with undocumented downgrade semantics
evidence_needed: whether per-account bootstrap token is stored plaintext vs OSCrypt (`v10`/`v11` prefix) in `Preferences`, scrypt N/r/p constants reachable from `whale_sync_util`, whether `forced_time` weakens key freshness
verify_steps: PASSIVE: objdump/strings around `WhaleSyncAuthManager`/`whale_sync_util` for the bootstrap-token envelope and `/whalesync/reset` request shape; diff prefs set vs upstream Chromium `sync.encryption_bootstrap_token` (upstream stores OSCrypt-encrypted in same file); zero network
impact: local attacker/infostealer with profile access decrypts synced passwords, cookies, autofill → full-account compromise; High
testability: PASSIVE
[HYP] Multiplay login-page exclusion bypass — server-tweakable heuristic + token-bearing URL sync
class: AUTH
asset: Multiplay session (`https://multiplay.whale.naver.com/`, invite via `multiplayPrivate.getJoinUrl`, real-time tab/scroll sync via `multiplay_session_io`)
confidence: 58
reasoning: binary confirms exclusion list is server-fetched pref `whale.tweak.multiplay_login_pages` (ua_tweak.json), scroll sync injects DOM (`isWhaleMultiplayScroll`), join binds `sessionToken`+`multiplayId`; URLs sync verbatim (incl. query strings) while only listed login pages are filtered
evidence_needed: on v4.38.386.14 whether joiner receives host's login-gated URLs or token-bearing query strings; whether unlisted login-like pages (OAuth consent/SSO) leak session params; invite-link reuse after host leaves
verify_steps: AUTH_HELPED: two authorized accounts join via copied `getJoinUrl`; host opens login-gated page + token-in-query page; capture file-local joiner-observed tab URLs/scroll/DOM; test link replay post-revocation; zero requests beyond client's own session
impact: cross-session disclosure of host's open-tab URLs incl. token-bearing query strings / session ids; Medium-High
testability: AUTH_HELPED
[HYP] `/whalesync/reset` trigger — auth binding / confirmation weakness
class: BUSLOGIC
asset: `https://api.whale.naver.com/whalesync/reset` (client trigger = server data wipe + all-device logout)
confidence: 45
reasoning: `/whalesync/reset` confirmed in v4.38.386.14 binary; whale auth uses `/oauth2/v1/nid/{login,refresh}` tokens — if reset needs only a bearer/refresh token without reauth confirm, token theft or a CSRFable client request wipes sync state
evidence_needed: exact method, body, headers the client sends to `/whalesync/reset`; whether reauth/confirmation is required
verify_steps: PASSIVE: objdump call-site analysis of `/whalesync/reset` in the WhalesyncRequest/`whale_sync_util` path to recover request shape; server probing out of scope
impact: sync data destruction + all-device log-out on token theft; DoS on account
testability: PASSIVE
[NEXT] PROBE: objdump/strings disassembly of the `/whalesync/reset` + `sync.encryption_bootstrap_token_per_account` call sites in `/tmp/opencode/whale_x/extract/opt/naver/whale/whale` (sha256 `6dd7ebc4...`) to recover the reset request method/body/auth-header shape and the bootstrap-token envelope storage (plaintext vs OSCrypt `v10/v11`), plus enumerate remaining embedded extension API schemas (`multiplayPrivate`, sync-related) for invite/session token entropy — local static only, zero network
[RISK] sync: 65 — custom `/whalesync` + per-account bootstrap tokens + server-tweakable Multiplay exclusion + Whale-only NID OAuth all confirmed in latest binary; key-storage/KDF and reset-auth not yet runtime-verified; server-tweakable client heuristics widen the sync attack surface | browser: 78 — sidebar/dual-tab (6 CVEs in 2025) remain 3 minor versions past last fix with zero 2026 CVEs; binary now available enables repro-first variant hunting | libs: 35 — paks now parseable for a bundled-lib inventory, and Whale-only `socket.io.slim.js` is confirmed bundled (content runtime-fetched); version-drift assessment still requires upstream comparison
[HYP] whale_sync_push socket.io push channel — server-controlled URL + unsanitized push message
class: XSS
asset: whale_sync_push component extension + utilityPrivate bridge (https://chat.whale.mu/) — Whale-only
confidence: 55
reasoning: binary embeds utilityPrivate.getPushServerURL (URL runtime-returned) + onPushUpdated(request_id,{message}); push_server_url_fetcher_base.cc logs "GetPushServerURL succeeded/failed"; service_worker.js+socket.io.slim.js are runtime-fetched, not in resources.pak; CVE-2022-24072/CVE-2024-40618 prove built-in-extension processing is a live Whale injection vector
evidence_needed: runtime push URL returned by getPushServerURL; whether socket.io message payload reaches chrome.tabs/history or extension internals unsanitized; whether URL-fetch endpoint is attacker-influenceable
verify_steps: AUTH_HELPED: authorized login → capture getPushServerURL response + runtime-fetched service_worker.js (file-local); audit socket.io onmessage handlers for unsanitized remote data reaching privileged APIs; zero out-of-scope probing
impact: remote push message mutating synced tabs/history or injection in extension context; Medium-High
testability: AUTH_HELPED
[HYP] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux
class: AUTH
asset: api.whale.naver.com/whalesync client engine + profile prefs sync.encryption_bootstrap_token_per_account{,_migration_done}, sync.whale_need_encryption_key_forced_time
confidence: 60
reasoning: strings confirm Whale-only prefs keys + Whale-forked OSCrypt (os_crypt_whale.cc, wbc_wrapper_apis.cc, `''xv10` magic); /whalesync authed by NEO_SES cookie only; sync.cookies/sync.passwords in type list
evidence_needed: per-account token plaintext vs Whale-OSCrypt-v10 in Preferences; where os_crypt_whale stores master key on Linux; whether whale_need_encryption_key_forced_time downgrades to a stale key
verify_steps: PASSIVE: objdump/strings on os_crypt_whale + whale_sync_util call sites for the bootstrap-token envelope and /whalesync/reset request shape (method/body/auth headers); diff pref set vs upstream Chromium; zero network
impact: local attacker/infostealer with profile access decrypts synced passwords/cookies/autofill → full-account compromise; High
testability: PASSIVE
[HYP] whaleonPrivate messaging channel — room/inviteCode/password authz
class: AUTH
asset: WhaleON messaging (whaleonPrivate.connectMessagingChannel{roomId,password,inviteCode,clientId}, pushMessage, onMessageReceived) — client static only
confidence: 45
reasoning: schema exposes inviteCode+password-bound room join and raw pushMessage{data,targets}+onMessageReceived{data}; real-time channel into browser; scope/entropy undocumented
evidence_needed: whether a replayed/guessed inviteCode or weak password joins a room and reads other participants' messages; whether pushMessage targets are validated
verify_steps: AUTH_HELPED: two authorized accounts join a study room; test inviteCode replay post-leave, empty password, cross-room message targeting; capture payloads file-local; zero requests beyond client's own session
impact: cross-session message disclosure or channel injection; Medium
testability: AUTH_HELPED
[HYP] whale_sync_push push-channel XSS — runtime-fetched handler, unverifiable in-session
class: XSS
asset: whale_sync_push component extension + utilityPrivate.getPushServerURL (push domain runtime-returned) — Whale-only
confidence: 48
reasoning: XSS class at extension API is ACCEPTED (CVE-2022-24072 devtools, CVE-2024-40618 built-in-extension sanitization, both on advisory 2026-08-08); push handler + socket.io.slim.js service_worker were runtime-fetched (not in resources.pak) per prior binary strings; binary artifact now absent so every concrete detail is stale
evidence_needed: re-acquired binary strings OR authorized-login capture of getPushServerURL response + runtime-fetched service_worker.js; whether socket.io message payload reaches chrome.tabs/history unsanitized
verify_steps: AUTH_HELPED: authorized login → capture push URL + service_worker.js file-local; audit onmessage handlers for remote data reaching privileged APIs; zero out-of-scope probing
impact: remote push message mutating synced tabs/history or injection in extension context; Medium-High
testability: AUTH_HELPED
[HYP] Sidebar/dual-tab SOP regression — new variant post-CVE-2025-69235/62584
class: OTHER
asset: sidebarAction.show() URL loading + dual-tab navigation (Whale-only window environments) on v4.38.386.14
confidence: 55
reasoning: 6 CVEs (CWE-346/358) fixed ≤ v4.35.351.12 (Dec 2025); latest v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs re-confirmed 2026-08-08; environment classes remain shipped; no 2026 disclosure reduces regression signal, not likelihood
evidence_needed: a trigger on 4.38.386.14 that reproduces SOP/csp bypass in sidebar or dual-tab; requires installed browser or binary — neither available in-session
verify_steps: HUMAN_ONLY: install v4.38.386.14, craft sidebarAction.show()/dual-tab nav repro per CVE-2025-69235 PoC patterns; no concrete endpoint derivable passively
impact: cross-origin read/write in privileged window env → full compromise; High
testability: HUMAN_ONLY
[HYP] Multiplay tab-URL sync disclosure — joiner receives token-bearing URLs
class: AUTH
asset: browser Multiplay sync feature (multiplayPrivate.getJoinUrl → real-time tab/scroll sync; server `multiplay.whale.naver.com` is OOS, client behavior is the sync bug) — synchronization in scope
confidence: 55
reasoning: prior binary strings showed tab/scroll sync injects DOM (`isWhaleMultiplayScroll`) and URLs sync verbatim incl. query strings while only a server-fetched login-page exclusion list (`whale.tweak.multiplay_login_pages`) is filtered; scope tension: server-side is *.whale.naver.com (OOS) but the disclosure is a client sync feature
evidence_needed: whether joiner observes host's token-bearing query strings; whether unlisted OAuth/SSO consent pages leak session params; invite-link reuse post-revocation
verify_steps: AUTH_HELPED: two authorized accounts join via copied getJoinUrl; host opens login-gated + token-in-query pages; capture joiner-observed tab URLs/scroll/DOM file-local; test link replay; zero requests beyond own session
impact: cross-session disclosure of host open-tab URLs incl. token-bearing query strings / session ids; Medium-High
testability: AUTH_HELPED
## 2026-08-08 02:51:25 UTC [sync] (model bigpickle)
verify_steps: AUTH_HELPED: two authorized accounts join a study room; test inviteCode replay post-leave, empty password, cross-room message targeting; capture payloads file-local; zero requests beyond client's own session
impact: cross-session message disclosure or channel injection; Medium
testability: AUTH_HELPED
[HYP] whale_sync_push push-channel XSS — runtime-fetched handler, unverifiable in-session
class: XSS
asset: whale_sync_push component extension + utilityPrivate.getPushServerURL (push domain runtime-returned) — Whale-only
confidence: 48
reasoning: XSS class at extension API is ACCEPTED (CVE-2022-24072 devtools, CVE-2024-40618 built-in-extension sanitization, both on advisory 2026-08-08); push handler + socket.io.slim.js service_worker were runtime-fetched (not in resources.pak) per prior binary strings; binary artifact now absent so every concrete detail is stale
evidence_needed: re-acquired binary strings OR authorized-login capture of getPushServerURL response + runtime-fetched service_worker.js; whether socket.io message payload reaches chrome.tabs/history unsanitized
verify_steps: AUTH_HELPED: authorized login → capture push URL + service_worker.js file-local; audit onmessage handlers for remote data reaching privileged APIs; zero out-of-scope probing
impact: remote push message mutating synced tabs/history or injection in extension context; Medium-High
testability: AUTH_HELPED
[HYP] Sidebar/dual-tab SOP regression — new variant post-CVE-2025-69235/62584
class: OTHER
asset: sidebarAction.show() URL loading + dual-tab navigation (Whale-only window environments) on v4.38.386.14
confidence: 55
reasoning: 6 CVEs (CWE-346/358) fixed ≤ v4.35.351.12 (Dec 2025); latest v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs re-confirmed 2026-08-08; environment classes remain shipped; no 2026 disclosure reduces regression signal, not likelihood
evidence_needed: a trigger on 4.38.386.14 that reproduces SOP/csp bypass in sidebar or dual-tab; requires installed browser or binary — neither available in-session
verify_steps: HUMAN_ONLY: install v4.38.386.14, craft sidebarAction.show()/dual-tab nav repro per CVE-2025-69235 PoC patterns; no concrete endpoint derivable passively
impact: cross-origin read/write in privileged window env → full compromise; High
testability: HUMAN_ONLY
[HYP] Multiplay tab-URL sync disclosure — joiner receives token-bearing URLs
class: AUTH
asset: browser Multiplay sync feature (multiplayPrivate.getJoinUrl → real-time tab/scroll sync; server `multiplay.whale.naver.com` is OOS, client behavior is the sync bug) — synchronization in scope
confidence: 55
reasoning: prior binary strings showed tab/scroll sync injects DOM (`isWhaleMultiplayScroll`) and URLs sync verbatim incl. query strings while only a server-fetched login-page exclusion list (`whale.tweak.multiplay_login_pages`) is filtered; scope tension: server-side is *.whale.naver.com (OOS) but the disclosure is a client sync feature
evidence_needed: whether joiner observes host's token-bearing query strings; whether unlisted OAuth/SSO consent pages leak session params; invite-link reuse post-revocation
verify_steps: AUTH_HELPED: two authorized accounts join via copied getJoinUrl; host opens login-gated + token-in-query pages; capture joiner-observed tab URLs/scroll/DOM file-local; test link replay; zero requests beyond own session
impact: cross-session disclosure of host open-tab URLs incl. token-bearing query strings / session ids; Medium-High
testability: AUTH_HELPED
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
[HYP] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9)
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase is never sent to/stored on the Naver server and must be re-entered on every new device → client-side key derivation + local key store. Android only added sync encryption in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists (org scan), so binary must be analyzed statically.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: acquire latest desktop installer and Android XAPK 3.9.14.9 (apkpure blocked curl 403; try APKMirror or JS-resolved official link), extract/decompile, grep sync module for passphrase/scrypt/PBKDF2 constants, key-store paths, sync hostnames, bearer/OAuth token handling. AUTH_HELPED: authorized test login to observe token/key lifecycle in filesystem. Zero requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade; High
testability: AUTH_HELPED
[HYP] Security-boundary enforcement in Whale-only sidebar/dual-tab environments on latest desktop (variant hunting)
class: OTHER
asset: Latest desktop Whale (>=4.35.351.12) sidebar and dual-tab web panels
confidence: 45
reasoning: 2025-26 CVE family shows sidebar/dual-tab (Whale-only) repeatedly broke SOP, iframe sandbox, CSP (CVE-2025-69234/5, 62583/4/5, 53600), each fixed a release later; recurrence across 3 releases signals a systemic boundary weak spot; adjacent features (mobile window, quicksearch, scrapbook) may carry variants.
evidence_needed: crafted HTML that escapes iframe sandbox or bypasses SOP/CSP inside sidebar/dual-tab web panel on the LATEST build
verify_steps: AUTH_HELPED: install latest desktop Whale, open crafted HTML in sidebar and dual-tab web panels, test sandbox escape / cross-origin read / CSP bypass; repro-first on latest build; no server interaction.
impact: sandbox escape / SOP bypass from a webpage → arbitrary script execution or cross-origin data theft in browser UI; Critical if escalates to renderer code execution
testability: AUTH_HELPED
[HYP] Whale-only bundled third-party libs: version drift vs upstream with known CVEs
class: MISCONFIG
asset: Whale desktop/mobile bundled third-party libs (inventory not yet built)
confidence: 35
reasoning: historical installers/extension-store bugs (CVE-2018-12449, CVE-2022-2407x); no current inventory; only generic diff-against-upstream verify path
evidence_needed: bundled lib manifest + upstream version comparison
verify_steps: PASSIVE: build inventory from extracted binary, compare versions to upstream known-CVE tables
impact: outdated lib with public exploit → local/remote compromise; Medium-High
testability: PASSIVE
[HYP] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9)
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase is never sent to/stored on the Naver server and must be re-entered on every new device → client-side key derivation + local key store. Android only added sync encryption in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists (org scan), so binary must be analyzed statically.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: acquire latest desktop installer and Android XAPK 3.9.14.9 (apkpure blocked curl 403; try APKMirror or JS-resolved official link), extract/decompile, grep sync module for passphrase/scrypt/PBKDF2 constants, key-store paths, sync hostnames, bearer/OAuth token handling. AUTH_HELPED: authorized test login to observe token/key lifecycle in filesystem. Zero requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade; High
testability: AUTH_HELPED
[HYP] Security-boundary enforcement in Whale-only sidebar/dual-tab environments on latest desktop (variant hunting)
class: OTHER
asset: Latest desktop Whale (>=4.35.351.12) sidebar and dual-tab web panels
confidence: 45
reasoning: 2025-26 CVE family shows sidebar/dual-tab (Whale-only) repeatedly broke SOP, iframe sandbox, CSP (CVE-2025-69234/5, 62583/4/5, 53600), each fixed a release later; recurrence across 3 releases signals a systemic boundary weak spot; adjacent features (mobile window, quicksearch, scrapbook) may carry variants.
evidence_needed: crafted HTML that escapes iframe sandbox or bypasses SOP/CSP inside sidebar/dual-tab web panel on the LATEST build
verify_steps: AUTH_HELPED: install latest desktop Whale, open crafted HTML in sidebar and dual-tab web panels, test sandbox escape / cross-origin read / CSP bypass; repro-first on latest build; no server interaction.
impact: sandbox escape / SOP bypass from a webpage → arbitrary script execution or cross-origin data theft in browser UI; Critical if escalates to renderer code execution
testability: AUTH_HELPED
## 2026-08-08 03:58:17 UTC [sync] (model bigpickle)
## 2026-08-08 05:01:09 UTC [sync] (model bigpickle)
[NEW] Android sync asset pinned for the first time: com.naver.whale 3.9.14.9 (vc 15965), arm64-v8a XAPK 166.29 MB, SHA256 3c7232913cd054651eae6151d82cfd7719da1f35bf69e3cbc3da79bf1e011faf, published 2026-07-23 (APKMirror) / 2026-08-05 (Uptodown); APKMirror listing shows 4 variants (arm-v7a/arm64, APK+BUNDLE)
[NEW] Android patch cadence: 3.9.14.5 → 3.9.14.9 in ~6 weeks (2026-06-27 → 2026-08-05) with TWO re-uploads of 3.9.14.9 (2026-08-02, 08-05) — rapid churn on latest, sync encryption (added 3.8.6.2, 2025-04) still maturing
[PRIO] Desktop sync client (OSCrypt/bootstrap-token envelope, Local State) v4.38.386.14 — score 7.05 — attack 7 business 9 tech 8 gate 3 cloud 6 fresh 8
[PRIO] Android sync client com.naver.whale 3.9.14.9 — score 7.00 — attack 7 business 9 tech 7 gate 3 cloud 6 fresh 9
[PRIO] Sidebar/dual-tab environment, latest desktop >=4.35.351.12 — score 6.70 — attack 8 business 7 tech 7 gate 6 cloud 1 fresh 9
[HYP] Whale sync passphrase KDF + local key storage, desktop OSCrypt envelope (v4.38.386.14)
class: AUTH
asset: Whale sync client, whale://settings/syncSetup; key material in Local State/OS keychain/Preferences
confidence: 60
reasoning: Vendor help center: passphrase never sent to/stored server-side, re-entered per new device → client-side derivation. Prior binary-string recon shows Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account`, `whale_need_encryption_key_forced_time`, `_migration_done`) absent from upstream Chromium, indicating a custom OSCrypt/key-migration deviation.
evidence_needed: KDF algorithm + iteration counts; per-OS envelope of derived key; whether token/key persists in Local State
verify_steps: PASSIVE: acquire installer (non-Naver mirror), extract binaries, grep sync modules for "PBKDF2"/"scrypt"/"passphrase"/"bootstrap_token"/"xv10"; AUTH_HELPED: authorized test login, diff Local State pre/post to observe key lifecycle. Zero requests to naver sync infra.
impact: Weak KDF or weak envelope → local attacker/infostealer decrypts synced bookmarks + saved passwords → PII cascade; High
testability: AUTH_HELPED
[HYP] Android sync encryption key derivation/storage, com.naver.whale 3.9.14.9
class: AUTH
asset: com.naver.whale 3.9.14.9 (vc 15965) sync client; EncryptedSharedPreferences/Keystore
confidence: 58
reasoning: Sync encryption only added 2025-04 (3.8.6.2); TLS-only before. 3.9.14.9 SHA256 now pinned (3c723291…) via non-Naver mirror, confirming latest Android build reachable for static analysis once download channel is solved. Prefs key family (`sync.encryption_*`) already evidenced in prior binary strings.
evidence_needed: KDF constants, EncryptedSharedPreferences use, master-key source, bootstrap-token persistence path
verify_steps: PASSIVE: obtain 3.9.14.9 XAPK, extract APK, decompile dex, grep for scrypt/PBKDF2/Keystore/bootstrap_token strings and manifest-referenced sync hostnames; AUTH_HELPED: authorized login on test device to observe filesystem key/token lifecycle.
impact: Weak/plaintext-adjacent key storage → local attacker decrypts synced passwords; High
testability: AUTH_HELPED
[HYP] Sidebar/dual-tab boundary variant post-CVE-2025-69235, latest desktop
class: OTHER
asset: Latest desktop Whale (>=4.35.351.12) sidebar + dual-tab web panels
confidence: 45
reasoning: 6 confirmed 2025 CVEs across these Whale-only environments (SOP CWE-346, iframe sandbox escape, CSP CWE-358), each fixed a release later; recurrence across 3 releases signals systemic boundary weak spot; DevTools-in-sidebar added v4.38.386.12 expands the surface.
evidence_needed: crafted HTML escaping iframe sandbox / bypassing SOP/CSP in sidebar or dual-tab web panel on the LATEST build
verify_steps: AUTH_HELPED: install latest desktop Whale, open crafted HTML in sidebar and dual-tab web panels, test sandbox escape / cross-origin read / CSP bypass on v4.38.386.14
impact: Sandbox escape / SOP bypass from webpage → arbitrary script or cross-origin data theft in browser UI; Critical if it escalates to renderer
testability: AUTH_HELPED
[PARKED] Whale-only bundled third-party libs version drift: confidence 35 < 40; also binary-inventory verify path is currently unexecutable (Cloudflare egress block)
[PARKED] Android sync hypothesis merged with desktop sync hypothesis — same KDF/envelope design, only asset pin differs; both survive as one lead
[FINAL] 1. Whale sync client KDF + local key/envelope storage (desktop v4.38.386.14 + Android 3.9.14.9 SHA256 3c723291…) — confidence 58-60, class AUTH 2. Sidebar/dual-tab SOP/sandbox variant on v4.38.386.14 — confidence 45, class OTHER
[NEXT] PROBE: resolve the uptodown session-signed direct link to obtain com.naver.whale 3.9.14.9 XAPK: (1) `curl -c /tmp/opencode/utd.jar -s "https://naver-whale-browser.en.uptodown.com/android/download"` and re-extract the fresh `data-url` token (session-bound, re-fetched per request); (2) `curl -b /tmp/opencode/utd.jar -H "Referer: https://naver-whale-browser.en.uptodown.com/android/download" -o /tmp/opencode/whale_3.9.14.9.xapk "https://dw.uptodown.com/<fresh-token>"`; (3) verify `sha256sum` equals 3c7232913cd054651eae6151d82cfd7719da1f35bf69e3cbc3da79bf1e011faf before unzip; (4) unzip APK, grep dex for sync module strings ("passphrase", "PBKDF2", "scrypt", "bootstrap_token", "EncryptedSharedPreferences", "whale_need_encryption_key_forced_time"). Fallback if dw still 404s: try apkpure.net JS-resolved link or APKCombo browser-referenced GET. Zero requests to naver infra.
[LEARN] REJECTED binary acquisition @ APKMirror/APKCombo/apk.support/apkpure: Cloudflare 403 on curl egress in this sandbox — PASSIVE binary-download path is dead here; only session/JS-resolved channels (uptodown dw, apkpure.net) remain
[LEARN] ACCEPTED Android sync asset @ com.naver.whale 3.9.14.9: version + SHA256 pinned via non-Naver mirror metadata (APKMirror/Uptodown) — in-scope sync surface confirmed real, latest, and verifiable once the download channel is solved
[LEARN] CONFIRMED desktop latest @ changelog.whale.naver.com: page is fully JS-rendered (empty text fetch) — no server-side version assertion available passively; v4.38.386.14 claim remains from prior recon
[RISK] sync: 56 — Android 3.9.14.9 now SHA-pinned and latest (3.9.14.5→.9 in 6 weeks, two re-uploads), sync encryption still only ~15 months old with Whale-only prefs keys (`sync.encryption_*`, `_migration_done`) evidencing custom OSCrypt/migration deviations; but in-sandbox binary access is egress-blocked, so the KDF/envelope design stays unverifiable → moderate-high exposure, unchanged in kind
[RISK] browser: 78 — 6 confirmed 2025 CVEs in Whale-only sidebar/dual-tab envs (SOP CWE-346, iframe sandbox, CSP CWE-358); v4.38.386.14 is 3 minor bumps past last CVE-fix with 0 CVEs in the 6-month gap; DevTools-in-sidebar (v4.38.386.12) added attack surface
[RISK] libs: 30 — no Whale-only third-party manifests or version lists public; repo documentation-only; inventory impossible without binary access; low visibility, moderate inherent risk
## 2026-08-08 05:55:19 UTC [sync] (model bigpickle)
[HYP] Whale sync passphrase KDF + bootstrap-token envelope, official v4.38.386.14
class: AUTH
asset: whale binary (os_crypt_whale.cc / whale_sync_util.cc); key in Local State/keyring
confidence: 65
reasoning: Official latest binary contains Whale-only os_crypt_whale.cc + `sync.encryption_bootstrap_token[_per_account]` + migration_done + `whale_need_encryption_key_forced_time` prefs; "peanuts" fallback absent → custom envelope; UMA proves client-side token encryption. KDF/iteration params not yet extracted (stripped binary).
evidence_needed: PBKDF2/scrypt algorithm + iteration count for passphrase→bootstrap-token key; persistence location of derived key on Linux (keyring vs file vs Local State); brute-force resistance of envelope.
verify_steps: PASSIVE: string-guided objdump of os_crypt_whale.cc/whale_sync_util.cc code regions, .rodata scan for iteration constants vs Chromium nigori defaults. AUTH_HELPED: authorized Linux login, diff Local State/keyring pre/post sync to observe token envelope + key persistence. Zero requests to sync backend.
impact: Weak KDF or device-recoverable key → local attacker/infostealer decrypts synced passwords+bookmarks → PII cascade; High
testability: AUTH_HELPED
[HYP] Sidebar/dual-tab boundary variant post-CVE-2025-69235, latest desktop
class: OTHER
asset: Latest desktop Whale (>=4.35.351.12) sidebar + dual-tab web panels
confidence: 45
reasoning: 6 confirmed 2025 CVEs in Whale-only sidebar/dual-tab envs (SOP CWE-346, iframe sandbox, CSP CWE-358), each fixed a release later; v4.38.386.14 is 3 minor bumps past last fix with 0 CVEs in the gap; DevTools-in-sidebar (v4.38.386.12) expanded surface.
evidence_needed: crafted HTML escaping iframe sandbox / bypassing SOP/CSP in sidebar or dual-tab web panel on v4.38.386.14
verify_steps: AUTH_HELPED: install latest desktop Whale, open crafted HTML in sidebar and dual-tab web panels, test sandbox escape / cross-origin read / CSP bypass. No server interaction.
impact: sandbox escape / SOP bypass from webpage → arbitrary script or cross-origin data theft in browser UI; Critical if escalates to renderer
testability: AUTH_HELPED
[HYP] Sync auth refresh-token storage deviation in Whale client
class: AUTH
asset: whale binary — whale_sync_auth_manager.cc, naver_access_token_fetcher.cc, whale_refresh_token_revoker.cc; .whaleon.us tokens
confidence: 45
reasoning: Whale forks stock OAuth components (whale_sync_auth_manager.cc, access_token_fetcher_immediate_refresh_token.cc, whale_refresh_token_revoker.cc) — custom token lifecycle code is the deviation; such forks historically mis-store tokens (plaintext prefs/cookies).
evidence_needed: whether Whale refresh/access tokens persist outside Chromium token_service (plaintext prefs/cookies) and their scope
verify_steps: PASSIVE: strings around whale auth manager + token-service prefs, look for plaintext token/cookie key names. AUTH_HELPED: authorized login, inspect Preferences/Login Data/Secure Preferences for whale token keys. Zero requests to naver infra.
impact: stolen refresh token → sync account takeover (synced data + saved passwords); High
testability: AUTH_HELPED
[NEXT] PROBE: continue PASSIVE static analysis of the official binary — (1) scan `locales/en-US.pak` + `resources.pak` for the sync/passphrase UI strings (may document KDF/iteration); (2) objdump the `.rodata` region xref'ing `sync.encryption_bootstrap_token_per_account` to extract the OSCrypt-v10 key-wrap/KDF constants (PBKDF2 iterations / scrypt N,r,p); (3) verify SHA256 of the acquired deb against CDN `Last-Modified` metadata. Zero requests to Naver sync infra.
## 2026-08-08 06:40:33 UTC [sync] (model bigpickle)
## 2026-08-08 07:48:14 UTC [sync] (model bigpickle)
## 2026-08-08 08:21:09 UTC [sync] (model bigpickle)
[HYP] Whale sync passphrase KDF + bootstrap-token envelope, v4.38.386.14 / Android 3.9.14.9
class: AUTH
asset: whale binary os_crypt_whale.cc / whale_sync_util.cc; Local State + keyring (desktop), Keystore/EncryptedSharedPreferences (Android)
confidence: 65
reasoning: Help Center (2026-08-08, live) confirms passphrase never leaves device and is unrecoverable server-side → server stores only ciphertext + bootstrap token, so local key/token storage is the whole attack surface. Whale-only prefs (`sync.encryption_bootstrap_token_per_account`, `_migration_done`, `whale_need_encryption_key_forced_time`) and xv10-magic OSCrypt fork proven in binary via prior runs. KDF params unextractable — binary acquisition is now 100% dead in-sandbox.
evidence_needed: PBKDF2/scrypt alg + iteration count for passphrase→key; whether derived key persists to keyring/file/Local State; EnvelopeKDF resist local brute force; Android Keystore vs plaintext fallback.
verify_steps: AUTH_HELPED: authorized login on test device; snapshot Preferences/Login Data/Secure Preferences + keyring before/after enabling encrypted sync; enumerate KDF by instrumenting os_crypt path. HUMAN: cross-check against decrypted synced-data layout.
impact: Weak KDF or device-recoverable key → local attacker/infostealer decrypts synced passwords + bookmarks; High
testability: AUTH_HELPED
[HYP] Sync refresh-token storage deviation in forked OAuth components
class: AUTH
asset: whale binary whale_sync_auth_manager.cc / whale_refresh_token_revoker.cc; .whaleon.us token scope
confidence: 45
reasoning: Whale forks stock OAuth (access_token_fetcher_immediate_refresh_token.cc, revoker) — custom lifecycle code is the deviation; such forks historically persist tokens outside Chromium token_service (plaintext prefs/cookies). No static access possible; requires binary or authorized session.
evidence_needed: whether whale refresh/access tokens persist outside token_service and their scope/file perms.
verify_steps: AUTH_HELPED: authorized login, inspect Preferences/Cookies for whale token keys (whale.*, .whaleon.us) and file permissions; compare vs Chromium token_service layout.
impact: stolen refresh token → sync account takeover (synced data + saved passwords); High
testability: AUTH_HELPED
[HYP] Sidebar/dual-tab boundary variant post-CVE-2025-69235, latest desktop
class: OTHER
asset: Latest desktop Whale (>=4.35.351.12) sidebar + dual-tab web panels
confidence: 45
reasoning: 6 confirmed 2025 CVEs in these Whale-only envs (CWE-346 SOP, iframe sandbox, CWE-358 CSP), each fixed a release later; v4.38.386.14 is 3 minor bumps past the last fix with 0 CVEs; DevTools-in-sidebar (v4.38.386.12) added surface. NVD CPE confirms the 69235 fix only claims Windows/MacOS.
evidence_needed: crafted HTML escaping iframe sandbox / bypassing SOP/CSP on v4.38.386.14 in sidebar or dual-tab web panel.
verify_steps: AUTH_HELPED: install latest desktop Whale, load crafted HTML in sidebar + dual-tab panels, test sandbox escape / cross-origin read / CSP bypass. No server interaction.
impact: sandbox escape / SOP bypass from webpage → arbitrary script or cross-origin data theft in browser UI; Critical if it escalates to renderer
testability: AUTH_HELPED
## 2026-08-08 09:07:28 UTC [sync] (model bigpickle)
[HYP] Whale sync passphrase KDF + bootstrap-token envelope
class: AUTH
asset: whale binary os_crypt_whale.cc / whale_sync_util.cc; Local State + keyring (desktop), Keystore (Android)
confidence: 65
reasoning: Whale-only prefs (`sync.encryption_bootstrap_token[_per_account]`, `_migration_done`, `whale_need_encryption_key_forced_time`) + xv10-magic OSCrypt fork proven in v4.38.386.14 via prior runs. Public surface now exhaustively enumerated and contains ZERO sync docs (wiki 5 pages, repo 46 files) — sync analysis is binary-only. KDF params unextractable; all in-sandbox binary channels closed.
evidence_needed: PBKDF2/scrypt alg + iteration count for passphrase→key; derived-key persistence location (keyring vs file vs Local State); brute-force resistance.
verify_steps: BLOCKED in-sandbox (no binary). AUTH_HELPED: authorized Linux login, snapshot Preferences/Login Data/Secure Preferences + keyring pre/post encrypted-sync enable; enumerate KDF by instrumenting os_crypt path. HUMAN: deliver official .deb → PASSIVE objdump/.rodata scan for iteration constants.
impact: weak KDF or device-recoverable key → local attacker/infostealer decrypts synced passwords+bookmarks; High
testability: AUTH_HELPED
[HYP] Sync refresh-token storage deviation in forked OAuth components
class: AUTH
asset: whale binary whale_sync_auth_manager.cc / whale_refresh_token_revoker.cc; .whaleon.us token scope
confidence: 45
reasoning: Whale forks stock OAuth (access_token_fetcher_immediate_refresh_token, revoker) — custom token lifecycle is the deviation; such forks historically persist tokens outside Chromium token_service (plaintext prefs/cookies). No static or doc path exists (public surface has zero sync content).
evidence_needed: whether whale refresh/access tokens persist outside token_service, their scope and file perms.
verify_steps: AUTH_HELPED: authorized login, inspect Preferences/Cookies for whale/*.whaleon.us token keys + file perms; diff vs Chromium token_service layout. Zero requests to Naver infra.
impact: stolen refresh token → sync account takeover (synced data + saved passwords); High
testability: AUTH_HELPED
[HYP] Sidebar/dual-tab boundary variant post-CVE-2025-69235
class: OTHER
asset: Latest desktop Whale (>=4.35.351.12) sidebar + dual-tab web panels
confidence: 45
reasoning: 6 confirmed 2025 CVEs in these Whale-only envs (CWE-346 SOP, iframe sandbox, CWE-358 CSP); v4.38.386.14 is 3 minor bumps past last fix with 0 CVEs. Reconfirmed wiki doc: `sidebarAction.show({url})` loads arbitrary URL; sample manifest (translate branch) = MV2, all-origin content_scripts, no extension CSP, `tabs` permission. NVD CPE fixes only claim Windows/MacOS for 69235.
evidence_needed: crafted HTML escaping iframe sandbox / bypassing SOP/CSP in sidebar or dual-tab web panel on v4.38.386.14.
verify_steps: AUTH_HELPED: install latest desktop Whale, load crafted HTML in sidebar + dual-tab panels, test sandbox escape / cross-origin read / CSP bypass. No server interaction.
impact: sandbox escape / SOP bypass from webpage → arbitrary script or cross-origin data theft in browser UI; Critical if it escalates to renderer
testability: AUTH_HELPED
## 2026-08-08 09:56:36 UTC [sync] (model bigpickle)
## 2026-08-08 10:34:23 UTC [sync] (model bigpickle)
[HYP] Sidebar SOP/CSP boundary — CVE-2025-69235 fix not claimed for Linux
class: OTHER
asset: Latest desktop Whale (>=4.35.351.12, now 4.38.386.14) Linux sidebar + dual-tab web panels
confidence: 50
reasoning: NVD CPE for CVE-2025-69235 lists only Windows/MacOS as affected platforms — Linux never in the fix claim. Wiki confirms `sidebarAction.show({url})` loads arbitrary URL; sample extension = MV2, content_scripts `http://*/*`+`https://*/*`, no extension CSP. v4.38.386.14 is 3 bumps past last fix, 0 CVEs.
evidence_needed: sidebar/dual-tab panel in Linux build rendering cross-origin content with script execution or parent-context readback; iframe-sandbox escape.
verify_steps: AUTH_HELPED: install v4.38.386.14 on Linux, drive `sidebarAction.show({url: crafted.html})` + dual-tab web panel, test SOP readback of opener, iframe `sandbox` escape, CSP bypass. Zero requests to Naver infra.
impact: SOP bypass / script execution in privileged browser-UI context; Critical if it escalates to renderer
testability: AUTH_HELPED
[HYP] Sync passphrase KDF + bootstrap-token envelope
class: AUTH
asset: v4.38.386.14 `os_crypt_whale.cc` / `whale_sync_util.cc`; Local State + keyring (desktop), Keystore (Android 3.9.14.9)
confidence: 65
reasoning: Whale-only prefs (`sync.encryption_bootstrap_token_per_account`, `_migration_done`, `whale_need_encryption_key_forced_time`) + xv10-magic OSCrypt fork confirmed in binary via prior runs; Help Center (live) confirms passphrase never leaves device → server holds only ciphertext, so local KDF/key storage is the whole attack surface. KDF params unextractable — all in-sandbox binary channels closed.
evidence_needed: PBKDF2/scrypt alg + iteration count for passphrase→key; derived-key persistence (keyring vs file vs Local State); brute-force resistance.
verify_steps: BLOCKED in-sandbox. AUTH_HELPED: authorized Linux login, snapshot keyring + Preferences/Login Data pre/post encrypted-sync enable; enumerate KDF by instrumenting os_crypt path. HUMAN: deliver official `.deb` → PASSIVE objdump/`.rodata` scan for iteration constants.
impact: weak KDF or device-recoverable key → local attacker/infostealer decrypts synced passwords + bookmarks; High
testability: AUTH_HELPED
[HYP] Sync refresh-token storage deviation in forked OAuth components
class: AUTH
asset: whale binary `whale_sync_auth_manager.cc` / `whale_refresh_token_revoker.cc`; `.whaleon.us` token scope
confidence: 45
reasoning: Whale forks stock OAuth (access_token_fetcher_immediate_refresh_token, revoker) — custom token lifecycle is the deviation; such forks historically persist tokens outside Chromium token_service (plaintext prefs/cookies). Public surface has zero sync docs → binary-only. No static access possible in-sandbox.
evidence_needed: whether whale refresh/access tokens persist outside token_service, their scope + file perms.
verify_steps: AUTH_HELPED: authorized login, inspect Preferences/Cookies for `whale.*`/`.whaleon.us` token keys + perms; diff vs Chromium token_service layout.
impact: stolen refresh token → sync account takeover (synced data + saved passwords); High
testability: AUTH_HELPED
[NEXT] HUMAN: (a) deliver official Whale `.deb` v4.38.386.14 (cloudfront `d1vdt4q2qgdbji.cloudfront.net` DNS-blocked in-sandbox; APKMirror 403; softpedia Cloudflare) for passive `.rodata`/objdump extraction of PBKDF2/scrypt iteration constants + derived-key persistence; (b) install latest on Linux and drive `sidebarAction.show({url})` + dual-tab panels with crafted HTML to test the SOP/CSP boundary the CVE-2025-69235 fix never claimed for Linux.
## 2026-08-08 11:05:53 UTC [sync] (model bigpickle)
[HYP] Sidebar/dual-tab panel SOP-CSP boundary — CVE-2025-69235 fix never claimed for Linux
class: OTHER
asset: Latest desktop Whale v4.38.386.14 Linux sidebar (`sidebarAction.show({url})`) + dual-tab web panel
confidence: 50
reasoning: NVD CPE for CVE-2025-69235 (CWE-346) lists only Windows/MacOS affected — Linux absent from fix claim. Wiki (re-read this cycle) documents the full injection chain: all-origin content_scripts → `runtime.sendMessage` → unvalidated background listener → `show({url:'http://…'})` loads arbitrary web content into an extension-panel context; `use_navigation_bar:false` + drag also navigates the panel to foreign sites. v4.38.386.14 is 3 minor bumps past the last fix with 0 published CVEs.
evidence_needed: crafted HTML loaded into sidebar/dual-tab panel executing script or reading parent/opener cross-origin on Linux build; iframe `sandbox` escape; CSP bypass.
verify_steps: AUTH_HELPED: install v4.38.386.14 on Linux; drive `sidebarAction.show({url: crafted.html})` + dual-tab web panel; test opener readback, `sandbox` escape, CSP bypass. Zero requests to Naver infra.
impact: SOP bypass / script execution in privileged browser-UI context — cross-origin data theft; Critical if it escalates to renderer
testability: AUTH_HELPED
[HYP] Sync passphrase KDF + bootstrap-token envelope — weak/device-recoverable key
class: AUTH
asset: Whale binary `os_crypt_whale.cc`/`whale_sync_util.cc`; Local State + keyring (desktop), Keystore (Android 3.9.14.9)
confidence: 65
reasoning: Whale-only prefs (`sync.encryption_bootstrap_token_per_account`, `_migration_done`, `whale_need_encryption_key_forced_time`) + `xv10`-magic OSCrypt fork confirmed in v4.38.386.14 binary via prior runs. Help Center confirms passphrase never leaves device → server holds ciphertext only, so local KDF/key persistence is the whole attack surface. Wiki/docs inventory (this cycle) proves zero public sync docs — binary-only analysis.
evidence_needed: PBKDF2/scrypt alg + iteration count for passphrase→key; derived-key persistence (keyring vs file vs Local State); brute-force resistance.
verify_steps: BLOCKED in-sandbox (no binary). AUTH_HELPED: authorized Linux login, snapshot keyring + Preferences/Login Data/Secure Preferences pre/post encrypted-sync enable; instrument os_crypt path for KDF params.
impact: weak KDF or device-recoverable key → local attacker/infostealer decrypts synced passwords + bookmarks; High
testability: AUTH_HELPED
[HYP] Sync refresh-token storage deviation in forked OAuth components
class: AUTH
asset: Whale binary `whale_sync_auth_manager.cc`/`whale_refresh_token_revoker.cc`; `.whaleon.us` token scope
confidence: 45
reasoning: Whale forks stock OAuth (access_token_fetcher_immediate_refresh_token, revoker); such forks historically persist tokens outside Chromium token_service (plaintext prefs/cookies). Zero public sync docs (wiki inventory complete this cycle) → verification is user-profile + binary, not server.
evidence_needed: whether whale refresh/access tokens persist outside token_service; their scope + file perms.
verify_steps: AUTH_HELPED: authorized login; inspect user-profile `Preferences`/`Cookies` for `whale.*`/`.whaleon.us` token keys + perms; diff vs Chromium token_service layout. Zero requests to Naver infra.
impact: stolen refresh token → sync account takeover (synced data + saved passwords); High
testability: AUTH_HELPED
[NEXT] HUMAN: deliver official Whale `.deb` v4.38.386.14 via unrestricted internet (cloudfront `d1vdt4q2qgdbji.cloudfront.net` DNS-blocked in-sandbox; APKMirror/Softpedia 403; Uptodown 410) — this single asset unblocks BOTH the sync KDF `.rodata`/objdump extraction AND the authorized Linux install needed to drive `sidebarAction.show({url})` + dual-tab panel SOP/CSP tests.
## 2026-08-08 11:41:11 UTC [sync] (model bigpickle)
[HYP] Sync passphrase KDF + bootstrap-token envelope — weak/device-recoverable key
class: AUTH
asset: Whale v4.38.386.14 binary `os_crypt_whale.cc`/`whale_sync_util.cc`; Local State + keyring (desktop); now acquirable as Windows EXE via uptodown
confidence: 65
reasoning: Whale-only prefs (`sync.encryption_bootstrap_token_per_account`, `_migration_done`) + `xv10`-magic OSCrypt fork confirmed in prior binary runs. Help Center: passphrase never leaves device → server holds ciphertext only, so local KDF/key persistence is the whole attack surface. KDF params never extracted.
evidence_needed: PBKDF2/scrypt alg + iteration count for passphrase→key; derived-key persistence (keyring vs file vs Local State); brute-force resistance.
verify_steps: acquire EXE via uptodown JS-token flow (verify SHA256 `8bfdd96f81b8...c6c5de`), then passive strings/`.rodata`/objdump scan for iteration constants + `xv10`/`os_crypt_whale` symbols; AUTH_HELPED: authorized Linux login, snapshot keyring + Preferences/Login Data pre/post encrypted-sync enable.
impact: weak KDF or device-recoverable key → local attacker/infostealer decrypts synced passwords + bookmarks; High
testability: AUTH_HELPED
[HYP] Sidebar/dual-tab panel SOP-CSP boundary — CVE-2025-69235 fix never claimed for Linux
class: OTHER
asset: Latest desktop Whale v4.38.386.14 Linux sidebar (`sidebarAction.show({url})`) + dual-tab web panel
confidence: 50
reasoning: CVE-2025-69235/69234 NVD CPEs list only Windows/macOS affected; Linux absent from fix claim. Wiki confirms `show({url})` loads arbitrary URL and `use_navigation_bar:false` allows drag-navigation to foreign sites; sample extension uses all-origin content_scripts.
evidence_needed: crafted HTML loaded into sidebar/dual-tab panel executing script or reading opener cross-origin on Linux; iframe `sandbox` escape; CSP bypass.
verify_steps: AUTH_HELPED: install 4.38.386.14 on Linux, drive `sidebarAction.show({url: crafted.html})` + dual-tab web panel; test opener readback, `sandbox` escape, CSP bypass. Zero requests to Naver infra.
impact: SOP bypass / script execution in privileged browser-UI context; Critical if it escalates to renderer
testability: AUTH_HELPED
[HYP] Whale Installer DLL-execution class regression — is current installer still <3.1.0.0?
class: MISCONFIG
asset: WhaleSetup.exe (Windows) shipping v4.38.386.14; install-dir ACLs / DLL search path
confidence: 45
reasoning: CVE-2024-50583 (CWE-427, CVSS 6.3) fixed only in installer 3.1.0.0; installer is a Whale-only component (not Chromium). Current installer version unverified; regressions in improper-permission/DLL-load class are plausible across the 4.35→4.38 window.
evidence_needed: current WhaleSetup.exe VERSIONINFO ≥3.1.0.0?; install-dir ACLs; whether DLL preload pattern persists.
verify_steps: acquire WhaleSetup.exe via uptodown (SHA256 `8bfdd96f81b8...c6c5de`), read VERSIONINFO resource + scan for DLL load paths; if <3.1.0.0 → active in-scope bug. AUTH_HELPED: authorized Windows install, check dir ACLs + Procmon DLL loads.
impact: local DLL planting → code execution in user/admin installer context; Medium (High if LPE)
testability: AUTH_HELPED
[NEXT] PROBE: Acquire `WhaleSetup.exe` v4.38.386.14 via uptodown JS-token flow (`https://whale-browser.en.uptodown.com/windows/download` → resolve tokenized `dw.uptodown.com/dwn/...` path from page HTML), verify SHA256=`8bfdd96f81b8...c6c5de` (184.86 MB), then run passive static scan: (a) installer VERSIONINFO to settle the <3.1.0.0 open question (closes/sets CVE-2024-50583 hypothesis), (b) strings/`.rodata` scan of inner browser payload for `xv10`, `os_crypt_whale`, PBKDF2/scrypt iteration constants (advances sync KDF hypothesis). One asset, two hypotheses.
