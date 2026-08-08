# LEADS laguna (seed)
- SEED: no model output yet; pipeline starts on first run.
## 2026-08-07 18:43:16 UTC [browser] (model laguna)
[NEW] Only 1 whale-named repo in entire `naver` GitHub org: `naver/whale-browser-developers` (seed assumed "more exist")
[NEW] Repo is documentation-only — no browser source code, sync implementation, or bundled library manifests publicly available
[NEW] 4 branches: `master` (Chromium cc docs only), `translate` (extension API docs + sample), `v2` (devcenter pointer), `jdkim/update_documents` (Chromium LUCI/Mojo docs)
[NEW] Extension API surface documented: `whale.runtime`, `whale.storage`, `whale.sidebarAction`, `whale.windows`, `whale.tabs`, `whale.bookmarks`, `whale.browserAction` (from `translate` branch README.ko.md)
[NEW] Sample sidebar extension (`translate/src/sidebar-sample/`) uses `navigator.userAgent.includes('sidebar')` for context detection; content_scripts match ALL origins (`http://*/*`, `https://*/*`)
[NEW] Issue #23 (open, 2025-03-30): "Ignore valid BCP47 Language tags in the Naver Whale Extensions store" → maps to `store.whale.naver.com`
[NEW] NVD query reveals 21 known Whale CVEs; 6 CVEs in 2025 target sidebar + dual-tab environment (SOP bypass, iframe sandbox escape, CSP bypass)
[NEW] Latest known affected version from CVEs: 4.35.351.12 (CVE-2025-69234/69235, Dec 2025); no 2026 CVEs published yet
[NEW] `v2` branch README references `developers.whale.naver.com` and `lab.whale.naver.com` (Naver web services)
[CHANGED] Repo metadata last updated 2025-10-22
[PRIO] Whale browser sidebar environment, 7.15, atk=9 biz=9 tech=7 gate=2 cloud=4 fresh=9 — CWE-346 SOP bypass (CVE-2025-69235) + iframe sandbox escape (CVE-2025-69234) fixed in v4.35.351.12 (Dec 2025); sample code confirms sidebar context detection via userAgent
[PRIO] whale.* extension API surface (runtime/storage/sidebarAction), 6.70, atk=8 biz=8 tech=7 gate=3 cloud=6 fresh=6 — CVE-2022-24072 (devtools JS injection), CVE-2024-40618 (built-in extension XSS CWE-79); content_scripts match all origins; whale.storage may sync via Whale account
[PRIO] Whale browser dual-tab environment, 6.30, atk=8 biz=8 tech=6 gate=2 cloud=3 fresh=8 — 4 CVEs (CVE-2025-53600, 62583, 62584, 62585) for SOP/sandbox/CSP bypass (Jul–Oct 2025); Whale-specific feature not in Chromium
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235
class: OTHER
asset: Whale browser sidebar context (browser-internal, not a Naver web service)
confidence: 45
reasoning: CVE-2025-69235 (CWE-346) was fixed in v4.35.351.12 (Dec 2025). The sample extension code at translate/src/sidebar-sample/js/contentscript.js confirms sidebar context detection via `navigator.userAgent.includes('sidebar')`. 8+ months since fix; Whale-specific sidebar isolation has shown recurring SOP issues.
evidence_needed: Running browser binary ≥4.35.352 demonstrating cross-origin data access from sidebar context
verify_steps: Install latest Whale → open sidebar extension → attempt cross-origin fetch/XMLHttpRequest from sidebar content script to arbitrary origin → confirm if SOP enforced
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration (High)
testability: HUMAN_ONLY
[HYP] Extension content script / API injection
class: XSS
asset: whale.* extension API surface (whale.runtime.onMessage, whale.storage, content_scripts matching http://*/*, https://*/*)
confidence: 40
reasoning: CVE-2022-24072 (CWE-79, devtools API JS injection) and CVE-2024-40618 (improper sanitization in built-in extension) show the extension API surface has recurring XSS/injection issues. Sample manifest.json declares content_scripts matching ALL origins.
evidence_needed: Extension API documentation revealing unsafe message handling or missing origin validation in whale.runtime
verify_steps: Install latest Whale → load test extension with onMessage listener → test message payload with origin-spoofing → check if whale.storage.sync accepts unvalidated input → test content script injection vector
impact: Arbitrary JavaScript execution in extension context → cross-site data access, session theft (High)
testability: HUMAN_ONLY
[HYP] Dual-tab environment SOP/CSP bypass — new variant post-CVE-2025-62585
class: OTHER
asset: Whale browser dual-tab context (browser-internal)
confidence: 40
reasoning: 4 CVEs in Jul–Oct 2025 (CVE-2025-53600 SOP, CVE-2025-62583 iframe sandbox, CVE-2025-62584 SOP, CVE-2025-62585 CSP bypass via scheme). Dual-tab is a Whale-specific feature. Fixed in v4.33.325.17 (Oct 2025); 8+ months since fix.
evidence_needed: Running browser binary ≥4.33.326 demonstrating SOP/CSP bypass in dual-tab mode
verify_steps: Install latest Whale → open dual-tab mode → test cross-origin access between tabs → test scheme-based CSP bypass (javascript:/data: schemes)
impact: Same-origin policy bypass in dual-tab → credential theft, CSRF (High)
testability: HUMAN_ONLY
[FINAL]
[NEXT] [HUMAN]: Install latest Whale browser binary (currently unknown exact version ≥4.35.351.12) locally and test sidebar context SOP isolation — reproduce CVE-2025-69235 test case (cross-origin XHR from sidebar content script) to determine if the Dec 2025 fix is complete or if new variants exist in the current version. Browser binary must be obtained from a non-Naver mirror or local source (naver.com domains excluded per scope).
[LEARN] REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
[LEARN] REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
[LEARN] REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
[RISK] sync: 45 — whale.storage extension API exists and may sync via Whale account (eligible focus area); however, no sync flow code or endpoints are available for static analysis; historical CVEs targeted extension API and built-in extensions, not sync specifically; moderate uncertainty due to source-unavailable constraint
[RISK] browser: 75 — sidebar + dual-tab environments have 6 confirmed CVEs in 2025 (SOP bypass, iframe sandbox escape, CSP bypass); these are Whale-specific features not inherited from Chromium; active and recently vulnerable attack surface with 8+ months since last fixes; high priority for new variant discovery
[RISK] libs: 30 — Whale bundles Chromium (inherits all Chromium CVEs), but no Whale-only third-party library manifests or version lists are publicly available; the repo contains only translated documentation; the extension API surface (whale.*) uses CWE-79-inherited patterns but library-level version drift cannot be assessed without binary access; low visibility, moderate inherent risk
## 2026-08-07 18:58:01 UTC [browser] (model laguna)
[NEW] Latest stable Whale desktop version: v4.38.386.14 (June 25 2026) per Wikipedia infobox — 3 minor version bumps ahead of last CVE-fix version v4.35.351.12 (Dec 30 2025), ~6 months of undisclosed changes
[NEW] Wiki `whale.sidebarAction` docs reveal `show()` accepts a `url` parameter: "url to load in extension panel, if not defined loads the default page"
[NEW] Wiki `whale.sidebarAction` docs warn: `use_navigation_bar` defaults true; when false "your extension page may be navigated to other websites from drag events"
[NEW] Wiki page "How to avoid my extension from changing urls" confirms drag-drop navigation exposure is a documented security concern for sidebar extensions, provides mitigation code
[NEW] NVD 2026 CVE query (pubStartDate=2026-01-01) returns zero results — no public CVEs exist for versions 4.35.352 through 4.38.386
[CHANGED] Repo activity confirmed: last commit 2019-09-23 on both master and jdkim/update_documents; "updated" 2025-10-22 is metadata-only, no new code pushed
[PRIO] Whale browser sidebar environment on v4.38.386.14 (latest), 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9 — CVE-2025-69234/69235 (CWE-346, CWE-358) fixed in v4.35.351.12 (Dec 2025); current v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs; wiki confirms show() loads arbitrary URL in panel + drag-navigation exposure
[PRIO] Whale browser dual-tab environment on v4.38.386.14 (latest), 6.30, atk=8 biz=8 tech=6 gate=2 cloud=3 fresh=8 — 4 CVEs (CVE-2025-53600 SOP, 62583 sandbox, 62584 SOP, 62585 CSP-via-scheme CWE-358) fixed in v4.33.325.17 (Oct 2025); ~8 months since fix, Whale-specific feature
[PRIO] whale.* extension API surface (runtime/storage/sidebarAction/onMessage), 6.28, atk=8 biz=7 tech=7 gate=3 cloud=6 fresh=6 — CVE-2022-24072 (devtools JS injection CWE-79), CVE-2024-40618 (built-in extension XSS CWE-79); content_scripts match all origins; whale.storage may sync via Whale account
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — whale.browserAction.show({url:'<arbitrary>'}) loads cross-origin content into sidebar panel; v4.38.386.14 (latest stable)
confidence: 55
reasoning: CVE-2025-69235 (CWE-346, SOP bypass in sidebar) and CVE-2025-69234 (CWE-358, iframe sandbox escape in sidebar) were both fixed in v4.35.351.12 (Dec 30 2025). Current stable is v4.38.386.14 (June 25 2026) — 3 minor version bumps, 0 published CVEs in between. Wiki docs confirm sidebarAction.show() accepts a `url` parameter to "load in extension panel" and that `use_navigation_bar=false` creates drag-navigation exposure. Sidebar context detection is via `navigator.userAgent.includes('sidebar')` per sample contentscript.js. History of 6 sidebar/dual-tab CVEs in 2025 suggests recurring Whale-specific boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar context — either via show({url:'https://victim.com'}) loading cross-origin page in panel, or via drag-drop navigation bypassing SOP
verify_steps: Install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://example.com'}) and attempt cross-origin fetch/XMLHttpRequest from panel → test drag-drop navigation to cross-origin URL when use_navigation_bar=false → confirm if SOP enforced between sidebar panel and background extension context
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration, potential privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[HYP] Extension content script / API message injection via whale.runtime.onMessage
class: XSS
asset: whale.runtime.onMessage listener + content_scripts matching http://*/* and https://*/* (from sidebar-sample manifest.json)
confidence: 48
reasoning: CVE-2022-24072 (CWE-79, devtools API JS injection) and CVE-2024-40618 (improper sanitization in built-in extension processing) confirm the whale.* extension API has recurring injection issues. Sample manifest.json declares content_scripts matching ALL origins with run_at=document_end. Background script (background.js) processes whale.runtime.sendMessage('sidebarAction.show') without documented origin validation. The wiki sidebarAction API has no documented message-validation requirements.
evidence_needed: Extension API behavior showing unvalidated message processing or missing origin checks in whale.runtime.onMessage leading to arbitrary code execution in extension context
verify_steps: Install Whale v4.38.386.14 → load test extension with onMessage listener → send spoofed origin messages via whale.runtime.sendMessage → test if content script can inject into extension context → check if whale.storage.sync accepts unvalidated cross-origin input → verify content script isolation from web page
impact: Arbitrary JavaScript execution in extension context → cross-site data access, session theft, potential privilege escalation to browser-level APIs (High)
testability: HUMAN_ONLY
[HYP] Dual-tab environment SOP bypass and CSP scheme bypass — new variant post-CVE-2025-62585 on v4.38.386.14
class: OTHER
asset: Whale browser dual-tab context (Whale-specific feature, not in Chromium) — latest v4.38.386.14
confidence: 45
reasoning: 4 CVEs in Jul-Oct 2025: CVE-2025-53600 (CWE-346 SOP bypass), CVE-2025-62583 (CWE-358 iframe sandbox escape), CVE-2025-62584 (CWE-346 SOP bypass), CVE-2025-62585 (CWE-358 CSP bypass via specific scheme) — all fixed in v4.33.325.17 (Oct 16 2025). Current stable v4.38.386.14 is ~8 months ahead with 0 published CVEs. Dual-tab is a Whale-specific feature with no Chromium equivalent, recurring boundary issues.
evidence_needed: Running browser binary v4.38.386.14 demonstrating SOP/CSP bypass in dual-tab mode — cross-origin access between dual-tab panels or CSP bypass via javascript:/data: schemes
verify_steps: Install Whale v4.38.386.14 → open dual-tab mode → load cross-origin iframes in each tab panel → test cross-origin read between panels → test javascript: and data: scheme CSP bypass in dual-tab context → confirm if isolation enforced
impact: Same-origin policy bypass in dual-tab environment → credential theft, CSRF token exfiltration, potential sandbox escape (Critical if escalates to renderer code execution)
testability: HUMAN_ONLY
[FINAL]
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14 (confidence: 55, class: OTHER)
[HYP] Extension content script / API message injection via whale.runtime.onMessage (confidence: 48, class: XSS)
[HYP] Dual-tab environment SOP bypass and CSP scheme bypass — new variant post-CVE-2025-62585 on v4.38.386.14 (confidence: 45, class: OTHER)
[NEXT] [HUMAN]: Obtain Whale browser v4.38.386.14 binary from a non-naver.com source (e.g., official download mirror, third-party archive, or enterprise package) and install locally. Execute the CVE-2025-69235 reproduction test case: load the sidebar-sample extension (translate branch) in latest Whale, call `whale.sidebarAction.show({url:'https://httpbin.org/headers'})` to load a cross-origin page into the sidebar panel, then attempt `fetch('https://victim.example.com/cookie')` from the sidebar panel's content script context. Confirm whether SOP is enforced between the sidebar panel and the background extension context. Also test the drag-drop navigation vector: with `use_navigation_bar:false`, drag a cross-origin link onto the sidebar panel and verify if cross-origin data access persists after navigation.
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346, SOP bypass via sidebarAction.show URL loading) confirmed fixed in v4.35.351.12 (Dec 2025); latest stable v4.38.386.14 has 3 minor version bumps with zero published CVEs — regression or new variant possible
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358, CSP bypass via specific scheme) confirmed fixed in v4.33.325.17 (Oct 2025); latest v4.38.386.14 is ~8 months ahead, no new CVEs published
[LEARN] CONFIRMED @ GitHub: naver/whale-browser-developers repo is documentation-only (last real commit 2019-09-23); "updated" metadata 2025-10-22 is GitHub system metadata refresh, no code changes
[LEARN] CONFIRMED @ NVD: 21 total Whale CVEs, 0 published in 2026 — no public vulnerability disclosures exist for versions 4.35.352 through 4.38.386, creating a knowledge gap for current-version vulnerability discovery
[RISK] sync: 45 — whale.storage extension API exists and may sync via Whale account (eligible focus area); however, no sync flow code or endpoints are available for static analysis; historical CVEs targeted extension API and built-in extensions (CVE-2022-24072, CVE-2024-40618), not sync specifically; bigpickle model already prioritized sync passphrase/key-derivation design at confidence 58; moderate uncertainty due to source-unavailable constraint
[RISK] browser: 78 — sidebar + dual-tab environments have 6 confirmed CVEs in 2025 (SOP bypass CWE-346, iframe sandbox escape CWE-358, CSP bypass CWE-358); these are Whale-specific features not inherited from Chromium; latest stable v4.38.386.14 is 3 minor version bumps ahead of last CVE fix (Dec 2025) with ZERO published CVEs in between, creating a 6-month vulnerability disclosure gap; wiki docs confirm additional attack surface (show() URL loading, drag-navigation exposure); high priority for new variant discovery
[RISK] libs: 30 — Whale bundles Chromium (inherits all Chromium CVEs), but no Whale-only third-party library manifests or version lists are publicly available; the repo contains only translated documentation from 2019; the extension API surface (whale.*) uses CWE-79-inherited patterns but library-level version drift cannot be assessed without binary access; low visibility, moderate inherent risk
## 2026-08-07 20:04:21 UTC [browser] (model laguna)
[NEW] hypotheses-ling3.txt ranks sidebarAction.show SOP bypass at confidence 60 (vs 55 from hypotheses-laguna.txt)
[CHANGED] Browser risk score increased 75→78 (3 minor-version bumps since CVE-fix v4.35.351.12, 0 published CVEs in 6-month gap)
[PRIO] Whale browser sidebar environment on v4.38.386.14 — score 7.4, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9
[PRIO] whale.* extension API surface (runtime/storage/sidebarAction) on v4.38.386.14 — score 6.45, atk=8 biz=7 tech=7 gate=3 cloud=6 fresh=6
[PRIO] Whale browser dual-tab environment on v4.38.386.14 — score 6.3, atk=8 biz=8 tech=6 gate=2 cloud=3 fresh=8
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235
class: OTHER
asset: Whale browser v4.38.386.14, sidebarAction.show({url}) loading cross-origin page into sidebar panel
confidence: 58
reasoning: CVE-2025-69235 (CWE-346) and CVE-2025-69234 (CWE-358) fixed in v4.35.351.12 (Dec 2025); current v4.38.386.14 is 3 minor bumps ahead with 0 CVEs published. Wiki confirms show() accepts url param to "load in extension panel" and use_navigation_bar=false exposes drag-navigation vector. 6 sidebar/dual-tab CVEs in 2025 show recurring boundary weakness.
evidence_needed: Running v4.38.386.14 demonstrating cross-origin data access from sidebar panel content script
verify_steps: HUMAN_ONLY — load sidebar-sample extension in Whale v4.38.386.14 → call whale.sidebarAction.show({url:'https://victim.test'}) → inject fetch('https://other.test/data') from panel content script → observe if SOP enforced
impact: Cross-origin data theft from sidebar context (credentials, CSRF tokens); privilege escalation from extension to web context (Critical)
testability: HUMAN_ONLY
[HYP] Extension content script / API message injection via whale.runtime.onMessage
class: XSS
asset: whale.runtime.onMessage + content_scripts matching http://*/*, https://*/* (sidebar-sample manifest)
confidence: 48
reasoning: CVE-2022-24072 (CWE-79, devtools JS injection) and CVE-2024-40618 (CWE-79, unsanitized built-in extension processing) confirm recurring injection issues in whale.* API. Sample manifest declares content_scripts matching ALL origins with run_at=document_end. Background script processes whale.runtime.sendMessage without documented origin validation.
evidence_needed: Extension API behavior showing unvalidated message processing or missing origin checks
verify_steps: HUMAN_ONLY — load test extension with onMessage listener in v4.38.386.14 → send spoofed-origin messages via whale.runtime.sendMessage → test if content script injects into extension context → check whale.storage.sync origin validation
impact: Arbitrary JS execution in extension context → session theft, privilege escalation to browser-level APIs (High)
testability: HUMAN_ONLY
[HYP] Dual-tab environment SOP/CSP bypass — new variant post-CVE-2025-62585
class: OTHER
asset: Whale browser v4.38.386.14, dual-tab context (Whale-specific feature)
confidence: 45
reasoning: 4 CVEs (CVE-2025-53600 SOP, 62583 sandbox, 62584 SOP, 62585 CSP-via-scheme) fixed in v4.33.325.17 (Oct 2025). Current v4.38.386.14 is ~8 months ahead with 0 CVEs published. Dual-tab is Whale-specific with no Chromium equivalent, recurring boundary issues.
evidence_needed: v4.38.386.14 demonstrating SOP/CSP bypass in dual-tab mode
verify_steps: HUMAN_ONLY — open dual-tab in v4.38.386.14 → load cross-origin iframes in each panel → test cross-origin read between panels → test javascript:/data: scheme CSP bypass
impact: Same-origin policy bypass → credential theft, CSRF exfiltration, potential sandbox escape (Critical)
testability: HUMAN_ONLY
[FINAL]
[HYP] Sidebar context SOP bypass — confidence 58 (rank 1)
[HYP] Extension content script / API message injection — confidence 48 (rank 2)
[HYP] Dual-tab environment SOP/CSP bypass — confidence 45 (rank 3)
[NEXT] [HUMAN]: Obtain Whale browser v4.38.386.14 binary from a non-naver.com mirror or local source. Install and load the sidebar-sample extension from the `translate` branch. Reproduce CVE-2025-69235 test case: call `whale.sidebarAction.show({url:'https://httpbin.org/headers'})` then attempt `fetch('https://victim.example.com/cookie')` from the sidebar panel's content script. Test both the show()-loaded cross-origin page vector and the drag-drop navigation vector with `use_navigation_bar:false`. Document whether SOP is enforced between the sidebar panel and background extension context on v4.38.386.14.
[LEARN] CONFIRMED @ GitHub: naver/whale-browser-developers repo remains documentation-only (last commit 2019-09-23; 2025-10-22 metadata-only refresh) — no source code, sync implementation, or library manifests available for static analysis
[LEARN] CONFIRMED @ NVD: 0 CVEs published for Whale in 2026 — no public disclosures for versions 4.35.352 through 4.38.386, confirming a 6-month vulnerability disclosure gap
[RISK] sync: 45 — whale.storage extension API may sync via Whale account (eligible focus area); but no sync flow code/endpoints available for static analysis; historical CVEs targeted extension API and built-in extensions, not sync specifically; moderate uncertainty due to source-unavailable constraint
[RISK] browser: 78 — sidebar + dual-tab environments have 6 confirmed CVEs in 2025; latest v4.38.386.14 is 3 minor version bumps ahead of last CVE fix (Dec 2025) with ZERO published CVEs in between — 6-month disclosure gap; wiki docs confirm additional attack surface (show() URL loading, drag-navigation exposure); high priority for new variant discovery
[RISK] libs: 30 — Whale bundles Chromium (inherits all Chromium CVEs); but no Whale-only third-party library manifests or version lists publicly available; repo contains only translated documentation from 2019; library-level version drift cannot be assessed without binary access; low visibility, moderate inherent risk
## 2026-08-07 20:58:05 UTC [browser] (model laguna)
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser v4.38.386.14, sidebarAction.show({url}) loading cross-origin page into sidebar panel
confidence: 60
reasoning: CVE-2025-69235 (CWE-346) + CVE-2025-69234 (CWE-358) fixed in v4.35.351.12 (Dec 2025). Current v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki confirms show() accepts url param to "load in extension panel" and use_navigation_bar=false exposes drag-navigation vector. 6 sidebar/dual-tab CVEs in 2025 show recurring boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar panel content script via show({url:'https://victim.com'}) or drag-drop navigation
verify_steps: HUMAN_ONLY — install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://httpbin.org/headers'}) → attempt cross-origin fetch from panel content script → observe if SOP enforced; also test drag-drop navigation with use_navigation_bar:false
impact: Cross-origin data theft from sidebar context (credentials, CSRF tokens); privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend is naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added only in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS key/passphrase storage paths (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device in reset/recovery flows
verify_steps: PROBE: download Whale desktop installer stub (11.6MB, pstatic.net CDN), extract, grep sync module strings for "passphrase", "PBKDF2", "scrypt", "Sync", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero requests to naver sync infra
impact: Weak KDF or plaintext-adjacent key storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[HYP] Dual-tab environment SOP/CSP bypass — new variant post-CVE-2025-62585 on v4.38.386.14
class: OTHER
asset: Whale browser v4.38.386.14, dual-tab context (Whale-specific feature, not in Chromium)
confidence: 45
reasoning: 4 CVEs in Jul–Oct 2025: CVE-2025-53600 (CWE-346 SOP bypass), CVE-2025-62583 (CWE-358 iframe sandbox escape), CVE-2025-62584 (CWE-346 SOP bypass), CVE-2025-62585 (CWE-358 CSP bypass via specific scheme) — all fixed in v4.33.325.17 (Oct 2025). Current v4.38.386.14 is ~8 months ahead with 0 published CVEs.
evidence_needed: Running browser binary v4.38.386.14 demonstrating SOP/CSP bypass in dual-tab mode — cross-origin access between dual-tab panels or CSP bypass via javascript:/data: schemes
verify_steps: HUMAN_ONLY — install v4.38.386.14 → open dual-tab mode → load cross-origin iframes in each panel → test cross-origin read between panels → test javascript:/data: scheme CSP bypass → confirm if isolation enforced
impact: Same-origin policy bypass → credential theft, CSRF token exfiltration, potential sandbox escape (Critical if escalates to renderer code execution)
testability: HUMAN_ONLY
[FINAL] 1. Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14 (confidence 60, class OTHER, testability HUMAN_ONLY)
[FINAL] 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[FINAL] 3. Dual-tab environment SOP/CSP bypass — new variant post-CVE-2025-62585 on v4.38.386.14 (confidence 45, class OTHER, testability HUMAN_ONLY)
[NEXT] [PROBE]: Download Whale desktop installer stub (11.6MB) from pstatic.net CDN (identified by bigpickle recon as the browser package source); extract with 7z/unzip; grep sync module strings: "passphrase", "PBKDF2", "scrypt", "Sync", "sync", "key_derivation", "EncryptedSharedPreferences", "Local State", "Preferences", "salt", "iterations". Zero network requests to naver sync infrastructure — purely static binary extraction. If KDF constants/iteration counts are weak or plaintext key storage paths are found, escalate to [HYP] confidence 70+.
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account (eligible focus area); passphrase KDF/key-storage design entirely client-side with NO public code available; Android sync encryption added only 2025-04 (late); binary static analysis not yet performed; moderate-high exposure due to source-unavailable constraint + PII cascade potential (bookmarks + site passwords)
[RISK] browser: 78 — sidebar + dual-tab environments have 6 confirmed CVEs in 2025 (SOP bypass CWE-346, iframe sandbox escape CWE-358, CSP bypass CWE-358); these are Whale-specific features not inherited from Chromium; latest stable v4.38.386.14 is 3 minor version bumps ahead of last CVE fix (Dec 2025 v4.35.351.12) with ZERO published CVEs in between — 6-month disclosure gap; wiki docs confirm additional attack surface (sidebarAction.show() URL loading, drag-navigation exposure); $4k SOP reward tier; high priority for new variant discovery
[RISK] libs: 30 — Whale bundles Chromium (inherits all Chromium CVEs), but no Whale-only third-party library manifests or version lists are publicly available; repo contains only translated documentation from 2019; library-level version drift cannot be assessed without binary access; low visibility, moderate inherent risk
## 2026-08-07 21:56:51 UTC [browser] (model laguna)
## 2026-08-07 22:22:37 UTC [browser] (model laguna)
## 2026-08-07 23:01:31 UTC [browser] (model laguna)
[PRIO] Whale browser sidebar environment (sidebarAction.show URL loading) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 6.10, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7
[PRIO] Whale browser dual-tab environment on v4.38.386.14, 6.30, atk=8 biz=8 tech=6 gate=2 cloud=3 fresh=8
[PRIO] whale.* extension API surface (runtime/storage/sidebarAction/onMessage), 6.28, atk=8 biz=7 tech=7 gate=3 cloud=6 fresh=6
[PRIO] Whale-only bundled third-party libraries, 3.50, atk=5 biz=6 tech=4 gate=5 cloud=2 fresh=3
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser v4.38.386.14, whale.sidebarAction.show({url}) loading cross-origin page into sidebar panel
confidence: 60
reasoning: CVE-2025-69235 (CWE-346) + CVE-2025-69234 (CWE-358) fixed in v4.35.351.12 (Dec 2025). Current v4.38.386.14 is 3 minor bumps ahead with 0 published CVEs. Wiki confirms show() accepts url param to "load in extension panel" and use_navigation_bar=false exposes drag-navigation vector. 6 sidebar/dual-tab CVEs in 2025 show recurring boundary weakness.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar panel content script via show({url:'https://victim.com'}) or drag-drop navigation
verify_steps: PASSIVE: Inspect sidebarAction.show URL loading in extension manifest on translate branch; check use_navigation_bar defaults; review wiki "How to avoid my extension from changing urls" mitigation code for gaps. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call whale.sidebarAction.show({url:'https://httpbin.org/headers'}) → attempt cross-origin fetch from panel content script → test drag-drop navigation with use_navigation_bar:false
impact: Cross-origin data theft from sidebar context (credentials, CSRF tokens); privilege escalation from extension to web context (Critical if renderer compromise)
testability: HUMAN_ONLY
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added only in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android. Bigpickle binary strings show Whale-only prefs keys (sync.encryption_bootstrap_token_per_account, whale_need_encryption_key_forced_time) absent from upstream Chromium.
evidence_needed: KDF algorithm + iteration counts, per-OS storage paths (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device in reset/recovery flows
verify_steps: PASSIVE: Download Whale desktop installer stub from pstatic.net CDN; extract; grep sync module strings for "passphrase", "PBKDF2", "scrypt", "Sync", "NEO_SES", "nigori", "bootstrap_token". AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem. Zero network requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
[HYP] whale_sync_push socket.io push channel — unsanitized event reaching privileged APIs
class: XSS
asset: whale_sync_push extension (service_worker.js + socket.io.slim.js) bundled in Whale-only resources, v4.38.386.14
confidence: 45
reasoning: CVE-2022-24072 / CVE-2024-40618 prove built-in-extension processing is a prior Whale injection vector. Push channel is socket.io (unusual in browser core, Whale-only), and its events feed tab/typedUrls sync surfaces. Payloads arriving via WebSocket/engine.io transport create a trust boundary worth auditing.
evidence_needed: onmessage/event handlers in the extracted service worker; whether remote events reach chrome.tabs/history APIs unsanitized
verify_steps: PASSIVE: Extract whale_sync_push/*.js from resources.pak (local binary extraction); audit socket.io onmessage handlers for remote-origin event data reaching privileged APIs unsanitized
impact: Remote push message mutating synced tabs/history or executing in extension context; Medium–High
testability: PASSIVE
[PARKED] Dual-tab environment SOP/CSP bypass — new variant post-CVE-2025-62585 on v4.38.386.14: Confidence 45 meets threshold, but verify_steps are HUMAN_ONLY with no passive-first path; requires binary install + dual-tab testing which is not passive. Deferred until binary available.
[PARKED] Extension content script/API message injection via whale.runtime.onMessage: Confidence 48 meets threshold but verify_steps are HUMAN_ONLY; no passive-first path available (requires live browser install + extension load). Deferred until binary available.
[PARKED] /whalesync/reset trigger — auth binding/confirmation weakness: Confidence 40 below 40 threshold (borderline); server probing is explicitly out of scope per program rules; cannot verify client request shape without binary extraction.
[FINAL] 1. Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14 (confidence 60, class OTHER, testability HUMAN_ONLY)
[FINAL] 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[FINAL] 3. whale_sync_push socket.io push channel — unsanitized event reaching privileged APIs (confidence 45, class XSS, testability PASSIVE)
[NEXT] PROBE: Download Whale desktop installer stub from pstatic.net CDN (identified by bigpickle as the browser package source — 11.6MB); extract with 7z; grep sync module strings: "passphrase", "PBKDF2", "scrypt", "NEO_SES", "nigori", "bootstrap_token", "sync.encryption", "whale_need_encryption_key_forced_time". Zero network requests to naver sync infrastructure — purely local static binary extraction. If KDF constants/iteration counts are weak or plaintext key storage paths are found, escalate to [HYP] confidence 70+.
[LEARN] REJECTED @ store.whale.naver.com: Issue #23 maps to Naver web service, excluded per scope.yml out_of_scope (confirmed unchanged)
[LEARN] ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346 SOP bypass) confirmed fixed in v4.35.351.12 (Dec 2025); latest v4.38.386.14 is 3 minor bumps ahead with 0 CVEs — regression window remains open
[LEARN] ACCEPTED OTHER @ dual-tab environment: CVE-2025-62585 (CWE-358 CSP bypass) confirmed fixed in v4.33.325.17 (Oct 2025); ~8 months of undisclosed changes since
[LEARN] ACCEPTED XSS @ extension API: CVE-2022-24072 + CVE-2024-40618 confirmed — built-in extension processing is a recurring Whale-only injection vector
[LEARN] REJECTED @ naver/whale-browser-developers: Repo remains documentation-only (last real commit 2019-09-23; 2025-10-22 metadata-only refresh) — no browser binary source, sync flow code, or library manifests available for static analysis
[LEARN] REJECTED @ naver web services (developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com): All excluded per scope rules
[LEARN] CONFIRMED @ NVD: 0 CVEs published for Whale in 2026 — no public disclosures exist for versions 4.35.352 through 4.38.386, confirming a 6-month vulnerability disclosure gap
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account (eligible focus area); passphrase KDF/key-storage design entirely client-side with NO public code available; Android sync encryption added only 2025-04 (late); binary static analysis not yet performed; bigpickle model already identified Whale-only sync prefs deviations (sync.encryption_bootstrap_token_per_account, whale_need_encryption_key_forced_time) and custom /whalesync endpoint + socket.io push channel; moderate-high exposure due to source-unavailable constraint + PII cascade potential (bookmarks + site passwords)
[RISK] browser: 78 — sidebar + dual-tab environments have 6 confirmed CVEs in 2025 (SOP bypass CWE-346, iframe sandbox escape CWE-358, CSP bypass CWE-358); these are Whale-specific features not inherited from Chromium; latest stable v4.38.386.14 is 3 minor version bumps ahead of last CVE fix (Dec 2025 v4.35.351.12) with ZERO published CVEs in between — 6-month disclosure gap; wiki docs confirm additional attack surface (sidebarAction.show() URL loading, drag-navigation exposure); $4k SOP reward tier; high priority for new variant discovery
[RISK] libs: 30 — Whale bundles Chromium (inherits all Chromium CVEs), but no Whale-only third-party library manifests or version lists are publicly available; repo contains only translated documentation from 2019; library-level version drift cannot be assessed without binary access; low visibility, moderate inherent risk
## 2026-08-07 23:43:21 UTC [browser] (model laguna)
class: OTHER
asset: Whale browser v4.38.386.14 — `whale.sidebarAction.show({url})` loading cross-origin page into sidebar panel
confidence: 60
reasoning: CVE-2025-69235 (CWE-346 SOP bypass) and CVE-2025-69234 (CWE-358 iframe sandbox escape) were fixed in v4.35.351.12 (Dec 2025). Current stable v4.38.386.14 is 3 minor version bumps ahead with 0 published CVEs. Wiki docs confirm `show()` accepts `url` parameter to load arbitrary URL in sidebar panel; `use_navigation_bar=false` creates drag-navigation exposure. Recurring pattern: 6 sidebar/dual-tab CVEs in 2025.
evidence_needed: Running browser binary v4.38.386.14 demonstrating cross-origin data access from sidebar panel content script via `show({url:'https://victim.com'})` or drag-drop navigation with `use_navigation_bar:false`
verify_steps: PASSIVE: Review `translate` branch sidebar-sample extension manifest and wiki sidebarAction docs for gaps in cross-origin isolation when `show()` loads external URL. HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call `whale.sidebarAction.show({url:'https://httpbin.org/headers'})` → attempt cross-origin fetch from panel content script → test drag-drop navigation with `use_navigation_bar:false`
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration; privilege escalation from extension to web context; Critical if renderer compromise
testability: HUMAN_ONLY
class: AUTH
asset: Whale sync client (whale://settings/syncSetup desktop; com.naver.whale Android 3.9.14.9) — backend naver infra, static analysis only
confidence: 58
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added only in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android. Binary strings show Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account`, `whale_need_encryption_key_forced_time`) absent from upstream Chromium.
evidence_needed: KDF algorithm + iteration counts, per-OS storage paths (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device in reset/recovery flows
verify_steps: PASSIVE: Download Whale desktop installer stub from pstatic.net CDN (11.6MB); extract with 7z; grep sync module strings for "passphrase", "PBKDF2", "scrypt", "nigori", "bootstrap_token", "NEO_SES", "sync.encryption". AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem. Zero network requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
testability: AUTH_HELPED
class: AUTH
asset: whalesync client engine (`https://api.whale.naver.com/whalesync`) + profile prefs `sync.encryption_bootstrap_token_per_account`
confidence: 55
reasoning: Binary v4.38.386.14 strings show Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account`, `_per_account_migration_done`, `whale_need_encryption_key_forced_time`) absent from upstream Chromium; encryption uses `nigori-key` + `sync_pb.EncryptionKeys`; `whale_need_encryption_key_forced_time` is a Whale-only rekey gate with undocumented downgrade semantics
evidence_needed: Whether per-account bootstrap token is stored plaintext vs OSCrypt (`v10`/`v11` prefix) in `Preferences`; scrypt N/r/p constants in `whale_sync_util`; whether `forced_time` weakens key freshness
verify_steps: PASSIVE: objdump/strings on `whale_sync_util.cc`/`whale_sync_auth_manager.cc` call sites for `/whalesync/reset` (method+body) and bootstrap-token envelope; compare prefs key set against upstream Chromium `sync.encryption_bootstrap_token` handling — zero network requests
impact: Local attacker/infostealer with profile access decrypts synced passwords, cookies, bookmarks → full-account compromise (High)
testability: PASSIVE
## 2026-08-08 00:43:05 UTC [browser] (model laguna)
## 2026-08-08 02:46:37 UTC [browser] (model laguna)
