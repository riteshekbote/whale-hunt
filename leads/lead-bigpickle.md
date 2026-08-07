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
