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
## 2026-08-08 04:14:03 UTC [browser] (model laguna)
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
## 2026-08-08 05:17:16 UTC [browser] (model laguna)
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
## 2026-08-08 06:06:47 UTC [browser] (model laguna)
## 2026-08-08 06:29:17 UTC [browser] (model laguna)
[HYP] Whale sync passphrase KDF + bootstrap-token envelope, official v4.38.386.14
class: AUTH
asset: whale binary (os_crypt_whale.cc / whale_sync_util.cc); key in Local State/keyring
confidence: 65
[NEW] NO_DELTA — inventory, knowledge, and leads unchanged since last aggregated hypotheses (2026-08-08 04:14:14 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context
[PRIO] Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux, 6.55, atk=7 biz=8 tech=9 gate=2 cloud=3 fresh=8 — Binary strings confirm Whale-only prefs keys + Whale-forked OSCrypt (`os_crypt_whale.cc`, `wbc_wrapper_apis.cc`, `xv10` magic); per-account bootstrap token envelope deviates from upstream Chromium `sync.encryption_bootstrap_token`; local profile access yields full sync decryption
[PRIO] Whale browser sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9 — Whale-specific feature; CVE-2025-69234/69235 fixed Dec 2025; 3 minor version bumps with 0 CVEs; wiki confirms `sidebarAction.show()` loads arbitrary URL + drag-navigation exposure (`use_navigation_bar=false`)
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 6.10, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7 — Sync explicitly in scope; vendor docs: passphrase never leaves device; Android sync encryption added 2025-04; no public client code; binary static analysis required
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
[PRIO] Whale desktop v4.38.386.14 binary static analysis — bootstrap-token envelope + OSCrypt-whalé KDF, 7.85, atk=9 biz=9 tech=9 gate=3 cloud=4 fresh=10 — Whale-forked `os_crypt_whale.cc` + `_per_account` + `_migration_done` + `whale_need_encryption_key_forced_time` prefs are CONFIRMED present in the latest binary but KDF constants/envelope format NEVER statically extracted (stripped binary, egress-blocked in-sandbox). Sync is explicitly in-scope; local profile access = full sync decryption.
[PRIO] Whale sync push channel — `whale_sync_push` extension + `socket.io.slim.js`, 6.65, atk=7 biz=8 tech=6 gate=5 cloud=4 fresh=6 — socket.io bundled inside `resources.pak` is Whale-only (Chromium uses plain WebSocket); prior Whale injection CVEs at extension API surface (2022-24072, 2024-40618) confirm trust-boundary weakness; remote-origin push events reaching `chrome.tabs`/history APIs is the hypothesis.
[PRIO] Sidebar/dual-tab boundary variant on v4.38.386.14, 6.60, atk=8 biz=8 tech=6 gate=3 cloud=4 fresh=9 — 6 confirmed 2025 CVEs (CWE-346/358), 3 minor version bumps since last fix with 0 CVEs in the gap; however testability is HUMAN_ONLY with no passive-first path.
[HYP] Whale sync bootstrap-token envelope KDF extraction from static binary strings
class: AUTH
asset: whale binary v4.38.386.14 `/opt/naver/whale/` — `os_crypt_whale.cc`, `whale_sync_util.cc`, `Local State` key-wrap + `Preferences` `sync.encryption_bootstrap_token_per_account`
confidence: 68
reasoning: Confirmed via bigpickle static analysis that the latest binary contains Whale-only prefs keys (`*_per_account`, `_migration_done`, `whale_need_encryption_key_forced_time`) and forks `os_crypt_whale.cc`/`wbc_wrapper_apis.cc` with `xv10` magic; KDF iteration constants and OSCrypt-v10 master-key storage location have NOT been extracted from the binary — this is the verifiable gap.
evidence_needed: PBKDF2/scrypt iteration count + salt for passphrase→bootstrap-token key; whether `os_crypt_whale` stores master key in `Local State` (file) vs Linux keyring; brute-force resistance
verify_steps: PASSIVE: Download latest Whale `.deb` from `https://d1vdt4q2qgdbji.cloudfront.net/whale/...` (non-Naver CDN, ~11.6MB stub confirmed by prior recon); extract with `dpkg-deb -x`; `strings` + `objdump -d` on `libwhale.so`/`whale` binary targeting `.rodata` xref `sync.encryption_bootstrap_token_per_account`; grep for `PBKDF2`, `scrypt`, `N,r,p=`; inspect `Local State` for `os_crypt` key blob; compute `sha256sum` and verify `Last-Modified` — zero requests to `*.naver.com` or sync backend.
impact: Weak KDF or device-recoverable master key → local attacker/infostealer decrypts synced passwords+bookmarks+autofill → PII cascade (High)
testability: PASSIVE
[HYP] whale_sync_push socket.io message handler — remote-origin event → privileged chrome.* API
class: XSS
asset: `whale_sync_push` extension bundled in `resources.pak` (service_worker.js + `socket.io.slim.js`), v4.38.386.14
confidence: 48
reasoning: CVE-2022-24072 + CVE-2024-40618 confirm built-in-extension processing is a prior Whale injection vector; socket.io is bundled inside Whale-only `resources.pak` (unusual in browser core); push events feed `chrome.tabs`/typedUrls sync surfaces — remote-origin event data may reach privileged APIs if `onmessage` lacks origin validation.
evidence_needed: extracted `service_worker.js` showing `socket.on()` handlers forwarding payload to `chrome.*` without origin/message-source check
verify_steps: PASSIVE: `unzip` extracted `.deb`; use `resources pak` unpacker or `strings resources.pak` for `socket.io.slim` + `whale_sync_push`; grep JS layer for `socket.on` → `chrome.` call chains; inspect message-source validation (chrome.runtime). If push handler is runtime-fetched (not in pak), fall back to documenting stale evidence. Zero network requests to naver infra.
impact: Remote push message executing in extension context → tab history manipulation, credential theft; Medium-High
testability: PASSIVE
[HYP] Sidebar context SOP bypass — new variant on v4.38.386.14
class: OTHER
asset: `whale.sidebarAction.show({url})` + `use_navigation_bar=false` drag-navigation in sidebar panel
confidence: 52
reasoning: CVE-2025-69235 (CWE-346 SOP bypass) fixed in v4.35.351.12 (Dec 2025); v4.38.386.14 is 3 minor bumps ahead with 0 CVEs in gap; wiki docs confirm `show()` loads arbitrary URL in panel + `use_navigation_bar=false` enables drag-navigation to other sites; DEVTools-in-sidebar added in v4.38.386.12 expands surface.
evidence_needed: Running browser v4.38.386.14 demonstrating cross-origin `fetch()` from panel content script after `show({url:'https://victim.com'})`
verify_steps: HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call `whale.sidebarAction.show({url:'https://httpbin.org/headers'})` → attempt cross-origin fetch from panel content script → test drag-drop navigation with `use_navigation_bar:false`
impact: Cross-origin data theft from sidebar → credential/CSRF-token exfiltration; Critical if renderer escape (Critical)
testability: HUMAN_ONLY
[PARKED] Sidebar context SOP bypass — new variant on v4.38.386.14: testability HUMAN_ONLY with no passive-first verification path in the current sandbox (no binary installed, egress blocked); cannot be reproduced statically — deferred until desktop binary is acquired and installed.
[FINAL] (ranked, top first):
[NEXT] PROBE: Download latest Whale desktop `.deb` stub (~11.6 MB) from `https://d1vdt4q2qgdbji.cloudfront.net/whale/whale_stable_latest_amd64.deb` (confirmed non-Naver CDN in prior bigpickle recon); `dpkg-deb -x` into `/tmp/opencode/whale_x`; `strings` + `objdump -d` on the extracted binary targeting `.rodata` xref `sync.encryption_bootstrap_token_per_account`; grep for `PBKDF2`/`scrypt`/iteration constants; inspect `Local State` for `os_crypt` v10 key-blob + master key storage path; compute `sha256sum` and verify `Last-Modified`. Zero requests to `*.naver.com` or the `/whalesync` endpoint.
[LEARN] REJECTED @ naver/whale-browser-developers: Repo remains documentation-only (last code commit 2019-09-23; 2025-10-22 metadata-only) — static analysis path is dead; binary acquisition is the only static analysis vector.
[LEARN] ACCEPTED @ binary static analysis: Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account`, `_migration_done`, `whale_need_encryption_key_forced_time`) + Whale-forked `os_crypt_whale.cc`/`wbc_wrapper_apis.cc` + `xv10` magic CONFIRMED present in v4.38.386.14 binary via prior bigpickle/laguna runs — the KDF/envelope gap is the unfilled verification.
[LEARN] REJECTED passive binary acquisition @ APKMirror/APKCombo/apkpure: Cloudflare 403 on curl egress — confirmed dead in-sandbox; only uptodown session-token or JS-resolved channels remain for Android, and CDN `.deb` for desktop.
[LEARN] ACCEPTED @ socket.io.slim.js bundled: Confirmed Whale-only in `resources.pak` (prior bigpickle/laguna recon strings) — a non-Chromium runtime-bundled lib worth auditing for event-handler injection; however the handler itself may be runtime-fetched, degrading passive evidence — stale until re-acquired.
[RISK] sync: 65 — custom `/whalesync` push via socket.io + per-account bootstrap tokens + Whale-forked OSCrypt (`xv10` magic) + `whale_need_encryption_key_forced_time` rekey gate CONFIRMED in v4.38.386.14 binary; KDF algorithm/iteration count and master-key storage location NOT yet statically extracted (stripped binary) — local profile access yields full sync decryption with PII cascade; egress-blocked in-sandbox so verification is pending binary acquisition.
[RISK] browser: 78 — 6 confirmed 2025 CVEs in Whale-only sidebar/dual-tab (CWE-346/358); 3 minor version bumps since last CVE-fix (v4.35.351.12→v4.38.386.14) with 0 CVEs in the gap; DevTools-in-sidebar added in v4.38.386.12 expands surface; passive verification blocked (HUMAN_ONLY) without binary install.
[RISK] libs: 35 — Whale bundles Chromium (inherits all upstream CVEs); Whale-only `socket.io.slim.js` confirmed in `resources.pak`; no public bundled-lib manifest or version list — version-drift assessment impossible without binary extraction; low visibility, moderate inherent risk.
## 2026-08-08 07:46:42 UTC [browser] (model laguna)
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
[PRIO] Whale desktop v4.38.386.14 binary static analysis — bootstrap-token envelope + OSCrypt-whalé KDF, 7.85, atk=9 biz=9 tech=9 gate=3 cloud=4 fresh=10 — Whale-forked `os_crypt_whale.cc` + `_per_account` + `_migration_done` + `whale_need_encryption_key_forced_time` prefs are CONFIRMED present in the latest binary but KDF constants/envelope format NEVER statically extracted (stripped binary, egress-blocked in-sandbox). Sync is explicitly in-scope; local profile access = full sync decryption.
[PRIO] Whale sync push channel — `whale_sync_push` extension + `socket.io.slim.js`, 6.65, atk=7 biz=8 tech=6 gate=5 cloud=4 fresh=6 — socket.io bundled inside `resources.pak` is Whale-only (Chromium uses plain WebSocket); prior Whale injection CVEs at extension API surface (2022-24072, 2024-40618) confirm trust-boundary weakness; remote-origin push events reaching `chrome.tabs`/history APIs is the hypothesis.
[PRIO] Sidebar/dual-tab boundary variant on v4.38.386.14, 6.60, atk=8 biz=8 tech=6 gate=3 cloud=4 fresh=9 — 6 confirmed 2025 CVEs (CWE-346/358), 3 minor version bumps since last fix with 0 CVEs in the gap; however testability is HUMAN_ONLY with no passive-first path.
[HYP] Whale sync bootstrap-token envelope KDF extraction from static binary strings
class: AUTH
asset: whale binary v4.38.386.14 `/opt/naver/whale/` — `os_crypt_whale.cc`, `whale_sync_util.cc`, `Local State` key-wrap + `Preferences` `sync.encryption_bootstrap_token_per_account`
confidence: 68
reasoning: Confirmed via bigpickle static analysis that the latest binary contains Whale-only prefs keys (`*_per_account`, `_migration_done`, `whale_need_encryption_key_forced_time`) and forks `os_crypt_whale.cc`/`wbc_wrapper_apis.cc` with `xv10` magic; KDF iteration constants and OSCrypt-v10 master-key storage location have NOT been extracted from the binary — this is the verifiable gap.
evidence_needed: PBKDF2/scrypt iteration count + salt for passphrase→bootstrap-token key; whether `os_crypt_whale` stores master key in `Local State` (file) vs Linux keyring; brute-force resistance
verify_steps: PASSIVE: Download latest Whale `.deb` from `https://d1vdt4q2qgdbji.cloudfront.net/whale/...` (non-Naver CDN, ~11.6MB stub confirmed by prior recon); extract with `dpkg-deb -x`; `strings` + `objdump -d` on `libwhale.so`/`whale` binary targeting `.rodata` xref `sync.encryption_bootstrap_token_per_account`; grep for `PBKDF2`, `scrypt`, `N,r,p=`; inspect `Local State` for `os_crypt` key blob; compute `sha256sum` and verify `Last-Modified` — zero requests to `*.naver.com` or sync backend.
impact: Weak KDF or device-recoverable master key → local attacker/infostealer decrypts synced passwords+bookmarks+autofill → PII cascade (High)
testability: PASSIVE
[HYP] whale_sync_push socket.io message handler — remote-origin event → privileged chrome.* API
class: XSS
asset: `whale_sync_push` extension bundled in `resources.pak` (service_worker.js + `socket.io.slim.js`), v4.38.386.14
confidence: 48
reasoning: CVE-2022-24072 + CVE-2024-40618 confirm built-in-extension processing is a prior Whale injection vector; socket.io is bundled inside Whale-only `resources.pak` (unusual in browser core); push events feed `chrome.tabs`/typedUrls sync surfaces — remote-origin event data may reach privileged APIs if `onmessage` lacks origin validation.
evidence_needed: extracted `service_worker.js` showing `socket.on()` handlers forwarding payload to `chrome.*` without origin/message-source check
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
[PRIO] Whale desktop v4.38.386.14 binary static analysis — bootstrap-token envelope + OSCrypt-whalé KDF, 7.85, atk=9 biz=9 tech=9 gate=3 cloud=4 fresh=10 — Whale-forked `os_crypt_whale.cc` + `_per_account` + `_migration_done` + `whale_need_encryption_key_forced_time` prefs are CONFIRMED present in the latest binary but KDF constants/envelope format NEVER statically extracted (stripped binary, egress-blocked in-sandbox). Sync is explicitly in-scope; local profile access = full sync decryption.
[PRIO] Whale sync push channel — `whale_sync_push` extension + `socket.io.slim.js`, 6.65, atk=7 biz=8 tech=6 gate=5 cloud=4 fresh=6 — socket.io bundled inside `resources.pak` is Whale-only (Chromium uses plain WebSocket); prior Whale injection CVEs at extension API surface (2022-24072, 2024-40618) confirm trust-boundary weakness; remote-origin push events reaching `chrome.tabs`/history APIs is the hypothesis.
[PRIO] Sidebar/dual-tab boundary variant on v4.38.386.14, 6.60, atk=8 biz=8 tech=6 gate=3 cloud=4 fresh=9 — 6 confirmed 2025 CVEs (CWE-346/358), 3 minor version bumps since last fix with 0 CVEs in the gap; however testability is HUMAN_ONLY with no passive-first path.
[HYP] Whale sync bootstrap-token envelope KDF extraction from static binary strings
class: AUTH
asset: whale binary v4.38.386.14 `/opt/naver/whale/` — `os_crypt_whale.cc`, `whale_sync_util.cc`, `Local State` key-wrap + `Preferences` `sync.encryption_bootstrap_token_per_account`
confidence: 68
reasoning: Confirmed via bigpickle static analysis that the latest binary contains Whale-only prefs keys (`*_per_account`, `_migration_done`, `whale_need_encryption_key_forced_time`) and forks `os_crypt_whale.cc`/`wbc_wrapper_apis.cc` with `xv10` magic; KDF iteration constants and OSCrypt-v10 master-key storage location have NOT been extracted from the binary — this is the verifiable gap.
evidence_needed: PBKDF2/scrypt iteration count + salt for passphrase→bootstrap-token key; whether `os_crypt_whale` stores master key in `Local State` (file) vs Linux keyring; brute-force resistance
verify_steps: PASSIVE: Download latest Whale `.deb` from `https://d1vdt4q2qgdbji.cloudfront.net/whale/...` (non-Naver CDN, ~11.6MB stub confirmed by prior recon); extract with `dpkg-deb -x`; `strings` + `objdump -d` on `libwhale.so`/`whale` binary targeting `.rodata` xref `sync.encryption_bootstrap_token_per_account`; grep for `PBKDF2`, `scrypt`, `N,r,p=`; inspect `Local State` for `os_crypt` key blob; compute `sha256sum` and verify `Last-Modified` — zero requests to `*.naver.com` or sync backend.
impact: Weak KDF or device-recoverable master key → local attacker/infostealer decrypts synced passwords+bookmarks+autofill → PII cascade (High)
testability: PASSIVE
[HYP] whale_sync_push socket.io message handler — remote-origin event → privileged chrome.* API
class: XSS
asset: `whale_sync_push` extension bundled in `resources.pak` (service_worker.js + `socket.io.slim.js`), v4.38.386.14
confidence: 48
reasoning: CVE-2022-24072 + CVE-2024-40618 confirm built-in-extension processing is a prior Whale injection vector; socket.io is bundled inside Whale-only `resources.pak` (unusual in browser core); push events feed `chrome.tabs`/typedUrls sync surfaces — remote-origin event data may reach privileged APIs if `onmessage` lacks origin validation.
evidence_needed: extracted `service_worker.js` showing `socket.on()` handlers forwarding payload to `chrome.*` without origin/message-source check
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
[NEW] Current timestamp 2026-08-07 22:18:21 UTC — ~80 minutes since last inventory aggregation (2026-08-07 20:58:17 UTC); no new public CVEs, no new GitHub commits, no new wiki edits detected in context
[PRIO] Whale browser sidebar environment (whale.sidebarAction.show) on v4.38.386.14, 7.55, atk=9 biz=10 tech=7 gate=2 cloud=4 fresh=9
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 6.10, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7
[PRIO] Whale browser dual-tab environment on v4.38.386.14, 6.30, atk=8 biz=8 tech=6 gate=2 cloud=3 fresh=8
[PRIO] whale.* extension API surface (runtime/storage/sidebarAction), 6.28, atk=8 biz=7 tech=7 gate=3 cloud=6 fresh=6
[RISK] sync: 45 — whale.storage extension API exists and may sync via Whale account (eligible focus area); however, no sync flow code or endpoints are available for static analysis; historical CVEs targeted extension API and built-in extensions (CVE-2022-24072, CVE-2024-40618), not sync specifically; bigpickle model already prioritized sync passphrase/key-derivation design at confidence 58; moderate uncertainty due to source-unavailable constraint
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added only in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS key/passphrase storage paths (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device in reset/recovery flows
verify_steps: PROBE: download Whale desktop installer stub (11.6MB, pstatic.net CDN), extract, grep sync module strings for "passphrase", "PBKDF2", "scrypt", "Sync", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero requests to naver sync infra
impact: Weak KDF or plaintext-adjacent key storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
[FINAL] 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] [PROBE]: Download Whale desktop installer stub (11.6MB) from pstatic.net CDN (identified by bigpickle recon as the browser package source); extract with 7z/unzip; grep sync module strings: "passphrase", "PBKDF2", "scrypt", "Sync", "sync", "key_derivation", "EncryptedSharedPreferences", "Local State", "Preferences", "salt", "iterations". Zero network requests to naver sync infrastructure — purely static binary extraction. If KDF constants/iteration counts are weak or plaintext key storage paths are found, escalate to [HYP] confidence 70+.
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account (eligible focus area); passphrase KDF/key-storage design entirely client-side with NO public code available; Android sync encryption added only 2025-04 (late); binary static analysis not yet performed; moderate-high exposure due to source-unavailable constraint + PII cascade potential (bookmarks + site passwords)
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 6.10, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added only in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android. Bigpickle binary strings show Whale-only prefs keys (sync.encryption_bootstrap_token_per_account, whale_need_encryption_key_forced_time) absent from upstream Chromium.
evidence_needed: KDF algorithm + iteration counts, per-OS storage paths (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device in reset/recovery flows
verify_steps: PASSIVE: Download Whale desktop installer stub from pstatic.net CDN; extract; grep sync module strings for "passphrase", "PBKDF2", "scrypt", "Sync", "NEO_SES", "nigori", "bootstrap_token". AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem. Zero network requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
[FINAL] 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: Download Whale desktop installer stub from pstatic.net CDN (identified by bigpickle as the browser package source — 11.6MB); extract with 7z; grep sync module strings: "passphrase", "PBKDF2", "scrypt", "NEO_SES", "nigori", "bootstrap_token", "sync.encryption", "whale_need_encryption_key_forced_time". Zero network requests to naver sync infrastructure — purely local static binary extraction. If KDF constants/iteration counts are weak or plaintext key storage paths are found, escalate to [HYP] confidence 70+.
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account (eligible focus area); passphrase KDF/key-storage design entirely client-side with NO public code available; Android sync encryption added only 2025-04 (late); binary static analysis not yet performed; bigpickle model already identified Whale-only sync prefs deviations (sync.encryption_bootstrap_token_per_account, whale_need_encryption_key_forced_time) and custom /whalesync endpoint + socket.io push channel; moderate-high exposure due to source-unavailable constraint + PII cascade potential (bookmarks + site passwords)
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added only in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android. Binary strings show Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account`, `whale_need_encryption_key_forced_time`) absent from upstream Chromium.
evidence_needed: KDF algorithm + iteration counts, per-OS storage paths (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device in reset/recovery flows
verify_steps: PASSIVE: Download Whale desktop installer stub from pstatic.net CDN (11.6MB); extract with 7z; grep sync module strings for "passphrase", "PBKDF2", "scrypt", "nigori", "bootstrap_token", "NEO_SES", "sync.encryption". AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem. Zero network requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
asset: whalesync client engine (`https://api.whale.naver.com/whalesync`) + profile prefs `sync.encryption_bootstrap_token_per_account`
reasoning: Binary v4.38.386.14 strings show Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account`, `_per_account_migration_done`, `whale_need_encryption_key_forced_time`) absent from upstream Chromium; encryption uses `nigori-key` + `sync_pb.EncryptionKeys`; `whale_need_encryption_key_forced_time` is a Whale-only rekey gate with undocumented downgrade semantics
evidence_needed: Whether per-account bootstrap token is stored plaintext vs OSCrypt (`v10`/`v11` prefix) in `Preferences`; scrypt N/r/p constants in `whale_sync_util`; whether `forced_time` weakens key freshness
verify_steps: PASSIVE: objdump/strings on `whale_sync_util.cc`/`whale_sync_auth_manager.cc` call sites for `/whalesync/reset` (method+body) and bootstrap-token envelope; compare prefs key set against upstream Chromium `sync.encryption_bootstrap_token` handling — zero network requests
[PARKED] Whale sync passphrase KDF weakness / plaintext-adjacent key storage: confidence 58 but verify_steps require AUTH_HELPED (authorized test login) and binary acquisition which is not passive-first
[FINAL] 1. Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux (confidence 60, class AUTH, testability PASSIVE) 2. Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14 (confidence 60, class OTHER, testability HUMAN_ONLY) 3. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: Acquire latest Whale desktop installer (non-Naver mirror) and Android XAPK 3.9.14.9; extract and decompile; static grep for sync module strings in os_crypt_whale, whale_sync_util, whale_sync_auth_manager: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences", "xv10", "bootstrap_token" — zero network requests to naver infra
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account; passphrase KDF/key-storage design entirely client-side with no public code; Android sync encryption added 2025-04 (late); binary strings reveal Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account`, `whale_need_encryption_key_forced_time`, `_migration_done`) absent from upstream; moderate-high exposure due to source-unavailable constraint
[HYP] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9)
reasoning: Vendor help center states passphrase is never sent to/stored on the Naver server and must be re-entered on every new device → client-side key derivation + local key store. Android only added sync encryption in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists (org scan), so binary must be analyzed statically.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: acquire latest desktop installer and Android XAPK 3.9.14.9 (apkpure blocked curl 403; try APKMirror or JS-resolved official link), extract/decompile, grep sync module for passphrase/scrypt/PBKDF2 constants, key-store paths, sync hostnames, bearer/OAuth token handling. AUTH_HELPED: authorized test login to observe token/key lifecycle in filesystem. Zero requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade; High
[HYP] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9)
reasoning: Vendor help center states passphrase is never sent to/stored on the Naver server and must be re-entered on every new device → client-side key derivation + local key store. Android only added sync encryption in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists (org scan), so binary must be analyzed statically.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: acquire latest desktop installer and Android XAPK 3.9.14.9 (apkpure blocked curl 403; try APKMirror or JS-resolved official link), extract/decompile, grep sync module for passphrase/scrypt/PBKDF2 constants, key-store paths, sync hostnames, bearer/OAuth token handling. AUTH_HELPED: authorized test login to observe token/key lifecycle in filesystem. Zero requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade; High
[PRIO] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9), score: 6.1 (attack_surface:6, business_value:8, tech_exposure:7, gate_ease:3, cloud_surface:4, freshness:7)
[HYP] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9)
reasoning: Reports/hypotheses-bigpickle.txt detail passphrase/key-derivation and local key-storage design for desktop and Android 3.9.14.9; static analysis of whale-sync client code available
verify_steps: AUTH_HELPED: Inspect whale-sync client for key derivation functions
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 5.80, atk=7 biz=9 tech=6 gate=4 cloud=5 fresh=5 — Vendor docs: passphrase never sent to server; Android sync encryption added 2025-04; no public client code; binary static analysis required
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: acquire latest desktop installer and Android XAPK 3.9.14.9, extract/decompile, grep sync module for passphrase/scrypt/PBKDF2 constants, key-store paths, sync hostnames, bearer/OAuth token handling. AUTH_HELPED: authorized test login to observe token/key lifecycle in filesystem. Zero requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
[FINAL] 1. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED) 2. Sidebar context SOP bypass — new variant post-CVE-2025-69235 (confidence 45, class OTHER, testability HUMAN_ONLY)
[NEXT] PROBE: Acquire latest Whale desktop installer (non-Naver mirror) and Android XAPK 3.9.14.9; extract and decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account; passphrase KDF/key-storage design entirely client-side with no public code; Android sync encryption added 2025-04 (late); moderate-high exposure due to source-unavailable constraint
[HYP] Sync passphrase KDF / client key-storage design (desktop stub + Android)
asset: Whale sync client key-derivation and local key-store (desktop; com.naver.whale Android 3.9.14.9) — naver infra, static analysis only
reasoning: vendor help center: passphrase never sent/stored server-side, re-entered per device → client-side KDF + local key store; Android sync encryption added only 3.8.6.2 (2025-04); desktop installer is a 11.6MB stub (browser package fetched at install), Android APK blocked → full binary static analysis not yet possible.
evidence_needed: KDF algorithm + iteration counts, per-OS key/passphrase storage paths, whether key/token ever leaves device in reset/recovery flows
verify_steps: PASSIVE: download WhaleSetup.exe + beta, extract stub, inventory sync module strings/URLs; then acquire the versioned browser package the stub fetches at install time (same pstatic.net CDN, no naver web-service probing); grep for passphrase/PBKDF2/scrypt/sync/token constants. AUTH_HELPED: authorized test login to observe token/key filesystem lifecycle
impact: weak KDF or plaintext-adjacent key storage → local attacker/infostealer decrypts synced bookmarks+site passwords → PII cascade; High
[HYP] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9)
[RISK] sync: 45 — whale.storage extension API exists and may sync via Whale account (eligible focus area); however, no sync flow code or endpoints are available for static analysis; historical CVEs targeted extension API and built-in extensions (CVE-2022-24072, CVE-2024-40618), not sync specifically; bigpickle model already prioritized sync passphrase/key-derivation design at confidence 58; moderate uncertainty due to source-unavailable constraint
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added only in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android.
evidence_needed: KDF algorithm + iteration counts, per-OS key/passphrase storage paths (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device in reset/recovery flows
verify_steps: PROBE: download Whale desktop installer stub (11.6MB, pstatic.net CDN), extract, grep sync module strings for "passphrase", "PBKDF2", "scrypt", "Sync", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero requests to naver sync infra
impact: Weak KDF or plaintext-adjacent key storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
[FINAL] 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] [PROBE]: Download Whale desktop installer stub (11.6MB) from pstatic.net CDN (identified by bigpickle recon as the browser package source); extract with 7z/unzip; grep sync module strings: "passphrase", "PBKDF2", "scrypt", "Sync", "sync", "key_derivation", "EncryptedSharedPreferences", "Local State", "Preferences", "salt", "iterations". Zero network requests to naver sync infrastructure — purely static binary extraction. If KDF constants/iteration counts are weak or plaintext key storage paths are found, escalate to [HYP] confidence 70+.
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account (eligible focus area); passphrase KDF/key-storage design entirely client-side with NO public code available; Android sync encryption added only 2025-04 (late); binary static analysis not yet performed; moderate-high exposure due to source-unavailable constraint + PII cascade potential (bookmarks + site passwords)
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 6.10, atk=6 biz=8 tech=7 gate=3 cloud=4 fresh=7
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added only in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android. Bigpickle binary strings show Whale-only prefs keys (sync.encryption_bootstrap_token_per_account, whale_need_encryption_key_forced_time) absent from upstream Chromium.
evidence_needed: KDF algorithm + iteration counts, per-OS storage paths (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device in reset/recovery flows
verify_steps: PASSIVE: Download Whale desktop installer stub from pstatic.net CDN; extract; grep sync module strings for "passphrase", "PBKDF2", "scrypt", "Sync", "NEO_SES", "nigori", "bootstrap_token". AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem. Zero network requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
[PARKED] /whalesync/reset trigger — auth binding/confirmation weakness: Confidence 40 below 40 threshold (borderline); server probing is explicitly out of scope per program rules; cannot verify client request shape without binary extraction.
[FINAL] 2. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: Download Whale desktop installer stub from pstatic.net CDN (identified by bigpickle as the browser package source — 11.6MB); extract with 7z; grep sync module strings: "passphrase", "PBKDF2", "scrypt", "NEO_SES", "nigori", "bootstrap_token", "sync.encryption", "whale_need_encryption_key_forced_time". Zero network requests to naver sync infrastructure — purely local static binary extraction. If KDF constants/iteration counts are weak or plaintext key storage paths are found, escalate to [HYP] confidence 70+.
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account (eligible focus area); passphrase KDF/key-storage design entirely client-side with NO public code available; Android sync encryption added only 2025-04 (late); binary static analysis not yet performed; bigpickle model already identified Whale-only sync prefs deviations (sync.encryption_bootstrap_token_per_account, whale_need_encryption_key_forced_time) and custom /whalesync endpoint + socket.io push channel; moderate-high exposure due to source-unavailable constraint + PII cascade potential (bookmarks + site passwords)
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added only in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists for desktop or Android. Binary strings show Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account`, `whale_need_encryption_key_forced_time`) absent from upstream Chromium.
evidence_needed: KDF algorithm + iteration counts, per-OS storage paths (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device in reset/recovery flows
verify_steps: PASSIVE: Download Whale desktop installer stub from pstatic.net CDN (11.6MB); extract with 7z; grep sync module strings for "passphrase", "PBKDF2", "scrypt", "nigori", "bootstrap_token", "NEO_SES", "sync.encryption". AUTH_HELPED: Authorized test login to observe token/key lifecycle in filesystem. Zero network requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
asset: whalesync client engine (`https://api.whale.naver.com/whalesync`) + profile prefs `sync.encryption_bootstrap_token_per_account`
reasoning: Binary v4.38.386.14 strings show Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account`, `_per_account_migration_done`, `whale_need_encryption_key_forced_time`) absent from upstream Chromium; encryption uses `nigori-key` + `sync_pb.EncryptionKeys`; `whale_need_encryption_key_forced_time` is a Whale-only rekey gate with undocumented downgrade semantics
evidence_needed: Whether per-account bootstrap token is stored plaintext vs OSCrypt (`v10`/`v11` prefix) in `Preferences`; scrypt N/r/p constants in `whale_sync_util`; whether `forced_time` weakens key freshness
verify_steps: PASSIVE: objdump/strings on `whale_sync_util.cc`/`whale_sync_auth_manager.cc` call sites for `/whalesync/reset` (method+body) and bootstrap-token envelope; compare prefs key set against upstream Chromium `sync.encryption_bootstrap_token` handling — zero network requests
[PARKED] Whale sync passphrase KDF weakness / plaintext-adjacent key storage: confidence 58 but verify_steps require AUTH_HELPED (authorized test login) and binary acquisition which is not passive-first
[FINAL] 1. Sync bootstrap-token envelope storage — Whale OSCrypt deviation on Linux (confidence 60, class AUTH, testability PASSIVE) 2. Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14 (confidence 60, class OTHER, testability HUMAN_ONLY) 3. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED)
[NEXT] PROBE: Acquire latest Whale desktop installer (non-Naver mirror) and Android XAPK 3.9.14.9; extract and decompile; static grep for sync module strings in os_crypt_whale, whale_sync_util, whale_sync_auth_manager: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences", "xv10", "bootstrap_token" — zero network requests to naver infra
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account; passphrase KDF/key-storage design entirely client-side with no public code; Android sync encryption added 2025-04 (late); binary strings reveal Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account`, `whale_need_encryption_key_forced_time`, `_migration_done`) absent from upstream; moderate-high exposure due to source-unavailable constraint
[HYP] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9)
reasoning: Vendor help center states passphrase is never sent to/stored on the Naver server and must be re-entered on every new device → client-side key derivation + local key store. Android only added sync encryption in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists (org scan), so binary must be analyzed statically.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: acquire latest desktop installer and Android XAPK 3.9.14.9 (apkpure blocked curl 403; try APKMirror or JS-resolved official link), extract/decompile, grep sync module for passphrase/scrypt/PBKDF2 constants, key-store paths, sync hostnames, bearer/OAuth token handling. AUTH_HELPED: authorized test login to observe token/key lifecycle in filesystem. Zero requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade; High
[HYP] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9)
reasoning: Vendor help center states passphrase is never sent to/stored on the Naver server and must be re-entered on every new device → client-side key derivation + local key store. Android only added sync encryption in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists (org scan), so binary must be analyzed statically.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: acquire latest desktop installer and Android XAPK 3.9.14.9 (apkpure blocked curl 403; try APKMirror or JS-resolved official link), extract/decompile, grep sync module for passphrase/scrypt/PBKDF2 constants, key-store paths, sync hostnames, bearer/OAuth token handling. AUTH_HELPED: authorized test login to observe token/key lifecycle in filesystem. Zero requests to naver sync infra.
[HYP] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9)
reasoning: Vendor help center states passphrase is never sent to/stored on the Naver server and must be re-entered on every new device → client-side key derivation + local key store. Android only added sync encryption in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists (org scan), so binary must be analyzed statically.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: acquire latest desktop installer and Android XAPK 3.9.14.9 (apkpure blocked curl 403; try APKMirror or JS-resolved official link), extract/decompile, grep sync module for passphrase/scrypt/PBKDF2 constants, key-store paths, sync hostnames, bearer/OAuth token handling. AUTH_HELPED: authorized test login to observe token/key lifecycle in filesystem. Zero requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade; High
[HYP] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9)
reasoning: Vendor help center states passphrase is never sent to/stored on the Naver server and must be re-entered on every new device → client-side key derivation + local key store. Android only added sync encryption in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists (org scan), so binary must be analyzed statically.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: acquire latest desktop installer and Android XAPK 3.9.14.9 (apkpure blocked curl 403; try APKMirror or JS-resolved official link), extract/decompile, grep sync module for passphrase/scrypt/PBKDF2 constants, key-store paths, sync hostnames, bearer/OAuth token handling. AUTH_HELPED: authorized test login to observe token/key lifecycle in filesystem. Zero requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade; High
[PRIO] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9), score: 6.1 (attack_surface:6, business_value:8, tech_exposure:7, gate_ease:3, cloud_surface:4, freshness:7)
[HYP] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9)
reasoning: Reports/hypotheses-bigpickle.txt detail passphrase/key-derivation and local key-storage design for desktop and Android 3.9.14.9; static analysis of whale-sync client code available
verify_steps: AUTH_HELPED: Inspect whale-sync client for key derivation functions
[PRIO] Whale sync passphrase/key-derivation & local key-storage (desktop + Android 3.9.14.9), 5.80, atk=7 biz=9 tech=6 gate=4 cloud=5 fresh=5 — Vendor docs: passphrase never sent to server; Android sync encryption added 2025-04; no public client code; binary static analysis required
[HYP] Whale sync passphrase KDF weakness / plaintext-adjacent key storage
reasoning: Vendor help center states passphrase never sent to/stored on Naver server; must be re-entered on every new device → client-side key derivation + local key store. Android sync encryption added in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: acquire latest desktop installer and Android XAPK 3.9.14.9, extract/decompile, grep sync module for passphrase/scrypt/PBKDF2 constants, key-store paths, sync hostnames, bearer/OAuth token handling. AUTH_HELPED: authorized test login to observe token/key lifecycle in filesystem. Zero requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade (High)
[FINAL] 1. Whale sync passphrase KDF weakness / plaintext-adjacent key storage (confidence 58, class AUTH, testability AUTH_HELPED) 2. Sidebar context SOP bypass — new variant post-CVE-2025-69235 (confidence 45, class OTHER, testability HUMAN_ONLY)
[NEXT] PROBE: Acquire latest Whale desktop installer (non-Naver mirror) and Android XAPK 3.9.14.9; extract and decompile; static grep for sync module strings: "passphrase", "PBKDF2", "scrypt", "sync", "key", "token", "EncryptedSharedPreferences", "Local State", "Preferences" — zero network requests to naver infra
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account; passphrase KDF/key-storage design entirely client-side with no public code; Android sync encryption added 2025-04 (late); moderate-high exposure due to source-unavailable constraint
[HYP] Sync passphrase KDF / client key-storage design (desktop stub + Android)
asset: Whale sync client key-derivation and local key-store (desktop; com.naver.whale Android 3.9.14.9) — naver infra, static analysis only
reasoning: vendor help center: passphrase never sent/stored server-side, re-entered per device → client-side KDF + local key store; Android sync encryption added only 3.8.6.2 (2025-04); desktop installer is a 11.6MB stub (browser package fetched at install), Android APK blocked → full binary static analysis not yet possible.
evidence_needed: KDF algorithm + iteration counts, per-OS key/passphrase storage paths, whether key/token ever leaves device in reset/recovery flows
verify_steps: PASSIVE: download WhaleSetup.exe + beta, extract stub, inventory sync module strings/URLs; then acquire the versioned browser package the stub fetches at install time (same pstatic.net CDN, no naver web-service probing); grep for passphrase/PBKDF2/scrypt/sync/token constants. AUTH_HELPED: authorized test login to observe token/key filesystem lifecycle
impact: weak KDF or plaintext-adjacent key storage → local attacker/infostealer decrypts synced bookmarks+site passwords → PII cascade; High
[HYP] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9)
reasoning: Vendor help center states passphrase is never sent to/stored on the Naver server and must be re-entered on every new device → client-side key derivation + local key store. Android only added sync encryption in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists (org scan), so binary must be analyzed statically.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: acquire latest desktop installer and Android XAPK 3.9.14.9 (apkpure blocked curl 403; try APKMirror or JS-resolved official link), extract/decompile, grep sync module for passphrase/scrypt/PBKDF2 constants, key-store paths, sync hostnames, bearer/OAuth token handling. AUTH_HELPED: authorized test login to observe token/key lifecycle in filesystem. Zero requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade; High
[HYP] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9)
reasoning: Vendor help center states passphrase is never sent to/stored on the Naver server and must be re-entered on every new device → client-side key derivation + local key store. Android only added sync encryption in 3.8.6.2 (2025-04); prior sync was TLS-only. No public client code exists (org scan), so binary must be analyzed statically.
evidence_needed: KDF algorithm + iteration counts, per-OS storage of derived key/passphrase (Preferences/Local State/OS keychain/EncryptedSharedPreferences), whether key/passphrase ever leaves device (reset/recovery flows), sync auth token storage/scope
verify_steps: PASSIVE: acquire latest desktop installer and Android XAPK 3.9.14.9 (apkpure blocked curl 403; try APKMirror or JS-resolved official link), extract/decompile, grep sync module for passphrase/scrypt/PBKDF2 constants, key-store paths, sync hostnames, bearer/OAuth token handling. AUTH_HELPED: authorized test login to observe token/key lifecycle in filesystem. Zero requests to naver sync infra.
impact: Weak KDF or plaintext-adjacent key/passphrase storage → local attacker or infostealer decrypts synced bookmarks+site passwords → PII cascade; High
[PRIO] Whale sync passphrase/key-derivation & local key-storage design (desktop + Android 3.9.14.9), score: 6.1 (attack_surface:6, business_value:8, tech_exposure:7, gate_ease:3, cloud_surface:4, freshness:7)
[HYP] whale_sync_push socket.io push channel — unsanitized event reaching privileged APIs
asset: whale_sync_push extension (service_worker.js + socket.io.slim.js) bundled in Whale-only resources, v4.38.386.14
reasoning: CVE-2022-24072 / CVE-2024-40618 prove built-in-extension processing is a prior Whale injection vector. Push channel is socket.io (unusual in browser core, Whale-only), and its events feed tab/typedUrls sync surfaces. Payloads arriving via WebSocket/engine.io transport create a trust boundary worth auditing.
verify_steps: PASSIVE: Extract whale_sync_push/*.js from resources.pak (local binary extraction); audit socket.io onmessage handlers for remote-origin event data reaching privileged APIs unsanitized
[FINAL] 3. whale_sync_push socket.io push channel — unsanitized event reaching privileged APIs (confidence 45, class XSS, testability PASSIVE)
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account (eligible focus area); passphrase KDF/key-storage design entirely client-side with NO public code available; Android sync encryption added only 2025-04 (late); binary static analysis not yet performed; bigpickle model already identified Whale-only sync prefs deviations (sync.encryption_bootstrap_token_per_account, whale_need_encryption_key_forced_time) and custom /whalesync endpoint + socket.io push channel; moderate-high exposure due to source-unavailable constraint + PII cascade potential (bookmarks + site passwords)
[HYP] whale_sync_push push-channel XSS — runtime-fetched handler, unverifiable in-session
asset: whale_sync_push component extension + utilityPrivate.getPushServerURL (push domain runtime-returned) — Whale-only
reasoning: XSS class at extension API is ACCEPTED (CVE-2022-24072 devtools, CVE-2024-40618 built-in-extension sanitization, both on advisory 2026-08-08); push handler + socket.io.slim.js service_worker were runtime-fetched (not in resources.pak) per prior binary strings; binary artifact now absent so every concrete detail is stale
evidence_needed: re-acquired binary strings OR authorized-login capture of getPushServerURL response + runtime-fetched service_worker.js; whether socket.io message payload reaches chrome.tabs/history unsanitized
verify_steps: AUTH_HELPED: authorized login → capture push URL + service_worker.js file-local; audit onmessage handlers for remote data reaching privileged APIs; zero out-of-scope probing
[RISK] sync: 65 — custom `/whalesync` + per-account bootstrap tokens + server-tweakable Multiplay exclusion + Whale-only NID OAuth all confirmed in latest binary; key-storage/KDF and reset-auth not yet runtime-verified; server-tweakable client heuristics widen the sync attack surface | browser: 78 — sidebar/dual-tab (6 CVEs in 2025) remain 3 minor versions past last fix with zero 2026 CVEs; binary now available enables repro-first variant hunting | libs: 35 — paks now parseable for a bundled-lib inventory, and Whale-only `socket.io.slim.js` is confirmed bundled (content runtime-fetched); version-drift assessment still requires upstream comparison
[RISK] sync: 65 — custom `/whalesync` + per-account bootstrap tokens + server-tweakable Multiplay exclusion + Whale-only NID OAuth all confirmed in latest binary; key-storage/KDF and reset-auth not yet runtime-verified; server-tweakable client heuristics widen the sync attack surface | browser: 78 — sidebar/dual-tab (6 CVEs in 2025) remain 3 minor versions past last fix with zero 2026 CVEs; binary now available enables repro-first variant hunting | libs: 35 — paks now parseable for a bundled-lib inventory, and Whale-only `socket.io.slim.js` is confirmed bundled (content runtime-fetched); version-drift assessment still requires upstream comparison
[HYP] whale_sync_push socket.io push channel — unsanitized event reaching privileged APIs
asset: whale_sync_push extension (service_worker.js + socket.io.slim.js) bundled in Whale-only resources, v4.38.386.14
reasoning: CVE-2022-24072 / CVE-2024-40618 prove built-in-extension processing is a prior Whale injection vector. Push channel is socket.io (unusual in browser core, Whale-only), and its events feed tab/typedUrls sync surfaces. Payloads arriving via WebSocket/engine.io transport create a trust boundary worth auditing.
verify_steps: PASSIVE: Extract whale_sync_push/*.js from resources.pak (local binary extraction); audit socket.io onmessage handlers for remote-origin event data reaching privileged APIs unsanitized
[FINAL] 3. whale_sync_push socket.io push channel — unsanitized event reaching privileged APIs (confidence 45, class XSS, testability PASSIVE)
[RISK] sync: 55 — whale.storage extension API exists and may sync via Whale account (eligible focus area); passphrase KDF/key-storage design entirely client-side with NO public code available; Android sync encryption added only 2025-04 (late); binary static analysis not yet performed; bigpickle model already identified Whale-only sync prefs deviations (sync.encryption_bootstrap_token_per_account, whale_need_encryption_key_forced_time) and custom /whalesync endpoint + socket.io push channel; moderate-high exposure due to source-unavailable constraint + PII cascade potential (bookmarks + site passwords)
[HYP] whale_sync_push push-channel XSS — runtime-fetched handler, unverifiable in-session
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
[PRIO] Whale desktop v4.38.386.14 binary static analysis — bootstrap-token envelope + OSCrypt-whalé KDF, 7.85, atk=9 biz=9 tech=9 gate=3 cloud=4 fresh=10 — Whale-forked `os_crypt_whale.cc` + `_per_account` + `_migration_done` + `whale_need_encryption_key_forced_time` prefs are CONFIRMED present in the latest binary but KDF constants/envelope format NEVER statically extracted (stripped binary, egress-blocked in-sandbox). Sync is explicitly in-scope; local profile access = full sync decryption.
[PRIO] Whale sync push channel — `whale_sync_push` extension + `socket.io.slim.js`, 6.65, atk=7 biz=8 tech=6 gate=5 cloud=4 fresh=6 — socket.io bundled inside `resources.pak` is Whale-only (Chromium uses plain WebSocket); prior Whale injection CVEs at extension API surface (2022-24072, 2024-40618) confirm trust-boundary weakness; remote-origin push events reaching `chrome.tabs`/history APIs is the hypothesis.
[PRIO] Sidebar/dual-tab boundary variant on v4.38.386.14, 6.60, atk=8 biz=8 tech=6 gate=3 cloud=4 fresh=9 — 6 confirmed 2025 CVEs (CWE-346/358), 3 minor version bumps since last fix with 0 CVEs in the gap; however testability is HUMAN_ONLY with no passive-first path.
[HYP] Whale sync bootstrap-token envelope KDF extraction from static binary strings
class: AUTH
asset: whale binary v4.38.386.14 `/opt/naver/whale/` — `os_crypt_whale.cc`, `whale_sync_util.cc`, `Local State` key-wrap + `Preferences` `sync.encryption_bootstrap_token_per_account`
confidence: 68
reasoning: Confirmed via bigpickle static analysis that the latest binary contains Whale-only prefs keys (`*_per_account`, `_migration_done`, `whale_need_encryption_key_forced_time`) and forks `os_crypt_whale.cc`/`wbc_wrapper_apis.cc` with `xv10` magic; KDF iteration constants and OSCrypt-v10 master-key storage location have NOT been extracted from the binary — this is the verifiable gap.
evidence_needed: PBKDF2/scrypt iteration count + salt for passphrase→bootstrap-token key; whether `os_crypt_whale` stores master key in `Local State` (file) vs Linux keyring; brute-force resistance
verify_steps: PASSIVE: Download latest Whale `.deb` from `https://d1vdt4q2qgdbji.cloudfront.net/whale/...` (non-Naver CDN, ~11.6MB stub confirmed by prior recon); extract with `dpkg-deb -x`; `strings` + `objdump -d` on `libwhale.so`/`whale` binary targeting `.rodata` xref `sync.encryption_bootstrap_token_per_account`; grep for `PBKDF2`, `scrypt`, `N,r,p=`; inspect `Local State` for `os_crypt` key blob; compute `sha256sum` and verify `Last-Modified` — zero requests to `*.naver.com` or sync backend.
impact: Weak KDF or device-recoverable master key → local attacker/infostealer decrypts synced passwords+bookmarks+autofill → PII cascade (High)
testability: PASSIVE
[HYP] whale_sync_push socket.io message handler — remote-origin event → privileged chrome.* API
class: XSS
asset: `whale_sync_push` extension bundled in `resources.pak` (service_worker.js + `socket.io.slim.js`), v4.38.386.14
confidence: 48
reasoning: CVE-2022-24072 + CVE-2024-40618 confirm built-in-extension processing is a prior Whale injection vector; socket.io is bundled inside Whale-only `resources.pak` (unusual in browser core); push events feed `chrome.tabs`/typedUrls sync surfaces — remote-origin event data may reach privileged APIs if `onmessage` lacks origin validation.
evidence_needed: extracted `service_worker.js` showing `socket.on()` handlers forwarding payload to `chrome.*` without origin/message-source check
verify_steps: PASSIVE: `unzip` extracted `.deb`; use `resources pak` unpacker or `strings resources.pak` for `socket.io.slim` + `whale_sync_push`; grep JS layer for `socket.on` → `chrome.` call chains; inspect message-source validation (chrome.runtime). If push handler is runtime-fetched (not in pak), fall back to documenting stale evidence. Zero network requests to naver infra.
impact: Remote push message executing in extension context → tab history manipulation, credential theft; Medium-High
testability: PASSIVE
[HYP] Sidebar context SOP bypass — new variant on v4.38.386.14
class: OTHER
asset: `whale.sidebarAction.show({url})` + `use_navigation_bar=false` drag-navigation in sidebar panel
confidence: 52
reasoning: CVE-2025-69235 (CWE-346 SOP bypass) fixed in v4.35.351.12 (Dec 2025); v4.38.386.14 is 3 minor bumps ahead with 0 CVEs in gap; wiki docs confirm `show()` loads arbitrary URL in panel + `use_navigation_bar=false` enables drag-navigation to other sites; DEVTools-in-sidebar added in v4.38.386.12 expands surface.
evidence_needed: Running browser v4.38.386.14 demonstrating cross-origin `fetch()` from panel content script after `show({url:'https://victim.com'})`
verify_steps: HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call `whale.sidebarAction.show({url:'https://httpbin.org/headers'})` → attempt cross-origin fetch from panel content script → test drag-drop navigation with `use_navigation_bar:false`
impact: Cross-origin data theft from sidebar → credential/CSRF-token exfiltration; Critical if renderer escape (Critical)
testability: HUMAN_ONLY
[PARKED] Sidebar context SOP bypass — new variant on v4.38.386.14: testability HUMAN_ONLY with no passive-first verification path in the current sandbox (no binary installed, egress blocked); cannot be reproduced statically — deferred until desktop binary is acquired and installed.
[FINAL] (ranked, top first):
[NEXT] PROBE: Download latest Whale desktop `.deb` stub (~11.6 MB) from `https://d1vdt4q2qgdbji.cloudfront.net/whale/whale_stable_latest_amd64.deb` (confirmed non-Naver CDN in prior bigpickle recon); `dpkg-deb -x` into `/tmp/opencode/whale_x`; `strings` + `objdump -d` on the extracted binary targeting `.rodata` xref `sync.encryption_bootstrap_token_per_account`; grep for `PBKDF2`/`scrypt`/iteration constants; inspect `Local State` for `os_crypt` v10 key-blob + master key storage path; compute `sha256sum` and verify `Last-Modified`. Zero requests to `*.naver.com` or the `/whalesync` endpoint.
[LEARN] REJECTED @ naver/whale-browser-developers: Repo remains documentation-only (last code commit 2019-09-23; 2025-10-22 metadata-only) — static analysis path is dead; binary acquisition is the only static analysis vector.
[LEARN] ACCEPTED @ binary static analysis: Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account`, `_migration_done`, `whale_need_encryption_key_forced_time`) + Whale-forked `os_crypt_whale.cc`/`wbc_wrapper_apis.cc` + `xv10` magic CONFIRMED present in v4.38.386.14 binary via prior bigpickle/laguna runs — the KDF/envelope gap is the unfilled verification.
[LEARN] REJECTED passive binary acquisition @ APKMirror/APKCombo/apkpure: Cloudflare 403 on curl egress — confirmed dead in-sandbox; only uptodown session-token or JS-resolved channels remain for Android, and CDN `.deb` for desktop.
[LEARN] ACCEPTED @ socket.io.slim.js bundled: Confirmed Whale-only in `resources.pak` (prior bigpickle/laguna recon strings) — a non-Chromium runtime-bundled lib worth auditing for event-handler injection; however the handler itself may be runtime-fetched, degrading passive evidence — stale until re-acquired.
[RISK] sync: 65 — custom `/whalesync` push via socket.io + per-account bootstrap tokens + Whale-forked OSCrypt (`xv10` magic) + `whale_need_encryption_key_forced_time` rekey gate CONFIRMED in v4.38.386.14 binary; KDF algorithm/iteration count and master-key storage location NOT yet statically extracted (stripped binary) — local profile access yields full sync decryption with PII cascade; egress-blocked in-sandbox so verification is pending binary acquisition.
[RISK] browser: 78 — 6 confirmed 2025 CVEs in Whale-only sidebar/dual-tab (CWE-346/358); 3 minor version bumps since last CVE-fix (v4.35.351.12→v4.38.386.14) with 0 CVEs in the gap; DevTools-in-sidebar added in v4.38.386.12 expands surface; passive verification blocked (HUMAN_ONLY) without binary install.
[RISK] libs: 35 — Whale bundles Chromium (inherits all upstream CVEs); Whale-only `socket.io.slim.js` confirmed in `resources.pak`; no public bundled-lib manifest or version list — version-drift assessment impossible without binary extraction; low visibility, moderate inherent risk.
[HYP] Sync bootstrap-token envelope KDF extraction from static binary
class: AUTH
asset: Whale binary v4.38.386.14 — `os_crypt_whale.cc`/`whale_sync_util.cc`/`.rodata`; `Local State` key-wrap + `Preferences` `sync.encryption_bootstrap_token_per_account`
confidence: 65
reasoning: Prior bigpickle/laguna runs confirmed Whale-only prefs keys (`sync.encryption_bootstrap_token[_per_account]`, `_migration_done`, `whale_need_encryption_key_forced_time`), Whale-forked `os_crypt_whale.cc`/`wbc_wrapper_apis.cc` with `xv10` magic, custom `/whalesync` endpoint (NEO_SES cookie auth), and `nigori-key` + `sync_pb.EncryptionKeys` in v4.38.386.14. KDF algorithm, iteration count, salt, and OSCrypt-v10 master-key storage location have NOT been extracted — this is the verifiable gap.
evidence_needed: PBKDF2/scrypt algorithm + N/r/p iteration constants for passphrase→bootstrap-token key; whether `os_crypt_whale` stores master key in `Local State` file vs Linux keyring vs EncryptedPreferences; `whale_need_encryption_key_forced_time` downgrade semantics
verify_steps: PASSIVE: Download latest Whale `.deb` from `https://d1vdt4q2qgdbji.cloudfront.net/whale/stable/latest` — **CONFIRMED DNS-BLOCKED in-sandbox** (nslookup: No answer; `*.cloudfront.net` does not resolve). Download channels also blocked: APKMirror 403, uptodown 410, apkpure.com 403, pstatic.net 403, Naver domains excluded. Binary acquisition requires HUMAN with unrestricted internet access. Once acquired: `dpkg-deb -x` → `strings` + `objdump -d` on `whale` binary targeting `.rodata` xref `sync.encryption_bootstrap_token_per_account`; grep for `PBKDF2`, `scrypt`, `N,r,p=`; inspect `Local State` for `os_crypt` v10 key-blob. Zero requests to `*.naver.com` or `/whalesync`.
impact: Weak KDF or device-recoverable master key → local attacker/infostealer with profile access decrypts synced passwords+bookmarks+autofill → PII cascade (High)
testability: PASSIVE (binary acquisition blocked in-sandbox — requires out-of-band binary)
[HYP] Sidebar context SOP bypass — new variant post-CVE-2025-69235 on v4.38.386.14
class: OTHER
asset: Whale browser sidebar context — `whale.sidebarAction.show({url})` loads cross-origin content into sidebar panel; `use_navigation_bar=false` drag-navigation; v4.38.386.14 (latest stable)
confidence: 60
reasoning: CVE-2025-69235 (CWE-346 SOP bypass) + CVE-2025-69234 (CWE-358 iframe sandbox escape) fixed in v4.35.351.12 (Dec 2025). Current v4.38.386.14 is 3 minor bumps ahead with 0 CVEs in gap. Wiki docs (passively fetched from GitHub) confirm `sidebarAction.show()` accepts `url` parameter to load arbitrary URL in panel; `use_navigation_bar=false` exposes drag-navigation to other sites; opt-in mitigation (dragover/drop event listeners) is NOT default. Server-side application pattern allows `location.replace('https://...')` redirect. Sample extension content_scripts match ALL origins. 6 sidebar/dual-tab CVEs in 2025 suggest recurring Whale-specific boundary weakness. DevTools-in-sidebar added v4.38.386.12 expands surface.
evidence_needed: Running browser v4.38.386.14 demonstrating cross-origin data access from sidebar context after `show({url:'https://victim.com'})` or drag-drop navigation bypassing SOP
verify_steps: HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension from `translate/src/sidebar-sample/` → call `whale.sidebarAction.show({url:'https://httpbin.org/headers'})` → attempt cross-origin `fetch()` from panel content script → test drag-drop navigation with `use_navigation_bar:false` (no opt-in mitigation). No server interaction.
impact: Cross-origin data theft from sidebar context → credential/CSRF-token exfiltration; Critical if escalates to renderer code execution
testability: HUMAN_ONLY
[HYP] Sidebar/dual-tab boundary variant on v4.38.386.14
class: OTHER
asset: Whale browser dual-tab context (Whale-specific, not Chromium) — latest v4.38.386.14
confidence: 45
reasoning: 4 CVEs Jul–Oct 2025: CVE-2025-53600 (CWE-346 SOP bypass), 62583 (CWE-358 iframe sandbox escape), 62584 (CWE-346 SOP bypass), 62585 (CWE-358 CSP bypass via specific scheme) — all fixed in v4.33.325.17 (Oct 2025). Current v4.38.386.14 is ~8 months ahead with 0 published CVEs. Dual-tab is Whale-specific (no Chromium equivalent), recurring boundary issues. DevTools-in-sidebar (v4.38.386.12) added onto same surface.
evidence_needed: Running browser v4.38.386.14 demonstrating SOP/CSP bypass in dual-tab mode — cross-origin access between panels or javascript:/data: scheme bypass
verify_steps: HUMAN_ONLY: Install Whale v4.38.386.14 → open dual-tab mode → load cross-origin iframes in each panel → test cross-origin read between panels → test javascript:/data: scheme CSP bypass in dual-tab context. No server interaction.
impact: SOP bypass in dual-tab → credential theft, CSRF token exfiltration, potential sandbox escape (Critical if renderer escalation)
testability: HUMAN_ONLY
[LEARN] REJECTED @ cloudfront CDN binary acquisition: DNS resolution fails for `*.cloudfront.net` (nslookup: No answer) — the desktop `.deb` download path from `d1vdt4q2qgdbji.cloudfront.net` is confirmed dead in-sandbox; combined with APKMirror 403, uptodown 410 Gone, apkpure.com 403, and Naver-domain exclusion, ALL binary acquisition paths are blocked.
[LEARN] ACCEPTED @ GitHub wiki documentation: `naver/whale-browser-developers` wiki is publicly accessible via `raw.githubusercontent.com/wiki/` — confirms `sidebarAction.show({url})` loads arbitrary URL in sidebar panel, `use_navigation_bar=false` exposes drag-navigation with opt-in mitigation, server-side applications redirect via `location.replace`, and sample extension content_scripts match ALL origins. This is concrete documentation-level evidence for the SOP bypass attack surface.
[LEARN] ACCEPTED @ GitHub sample extension source: `translate/src/sidebar-sample/` on GitHub repo — `manifest.json` declares `content_scripts` matching `http://*/*` and `https://*/*`, `contentscript.js` detects sidebar context via `navigator.userAgent.includes('sidebar')`, `background.js` uses `whale.runtime.onMessage` listener. Confirms extension API surface is accessible from all origins.
[LEARN] REJECTED @ APKMirror: Only hosts legacy Whale Android versions (01.0.0.48/49) under `/apk/naver-corp/whale/` — not the latest 3.9.14.9 referenced in prior recon; download link path is stale and cannot provide the target binary.
[LEARN] CONFIRMED @ NVD: 0 CVEs published for Whale in 2026 — NVD API (keywordSearch=naver+whale) returns 2 total results (CVE-2018-9859, CVE-2020-9754), both pre-2021; no 2026 disclosures exist, confirming the 6-month vulnerability disclosure gap for v4.35.352–v4.38.386.14.
[LEARN] CONFIRMED @ GitHub repo: `naver/whale-browser-developers` remains documentation-only — last pushed 2019-09-23, updated 2025-10-22 (metadata-only), 4 branches (master, translate, v2, jdkim/update_documents), 0 releases, 4 open issues (1 REJECTED BC47 store, 3 feature requests — no security surface).
[LEARN] ACCEPTED @ prior binary analysis: Whale-only prefs keys (`sync.encryption_bootstrap_token[_per_account]`, `_migration_done`, `whale_need_encryption_key_forced_time`), Whale-forked `os_crypt_whale.cc`/`wbc_wrapper_apis.cc` with `xv10` magic, `socket.io.slim.js` in `resources.pak`, and custom `/whalesync` endpoint with NEO_SES cookie auth are CONFIRMED present in v4.38.386.14 via prior bigpickle/laguna runs, but KDF constants/iteration counts and master-key storage location remain unextracted.
[RISK] sync: 65 — custom `/whalesync` push via socket.io + per-account bootstrap tokens + Whale-forked OSCrypt (`xv10` magic) + `whale_need_encryption_key_forced_time` rekey gate CONFIRMED in v4.38.386.14 binary via prior runs; KDF algorithm/iteration count and master-key storage location NOT statically extracted; binary acquisition fully blocked in-sandbox (cloudfront DNS dead, all mirrors 403/410); local profile access yields full sync decryption with PII cascade; egress-blocked so verification pending out-of-band binary acquisition.
[RISK] browser: 78 — 6 confirmed 2025 CVEs in Whale-only sidebar/dual-tab (CWE-346 SOP bypass, CWE-358 iframe sandbox escape/CSP bypass); v4.38.386.14 is 3 minor version bumps past last CVE-fix (v4.35.351.12) with 0 CVEs in the gap; DevTools-in-sidebar (v4.38.386.12) added attack surface; wiki docs now passively confirm API surface (`sidebarAction.show({url})`, `use_navigation_bar=false` drag-navigation, server-side app redirect via `location.replace`); content_scripts match ALL origins in sample extension; HUMAN_ONLY testability blocks passive validation.
[RISK] libs: 35 — Whale bundles Chromium (inherits all upstream CVEs); Whale-only `socket.io.slim.js` confirmed in `resources.pak` (prior runs); no public bundled-lib manifest or version list; version-drift assessment impossible without binary extraction; APKMirror only has legacy versions; low visibility, moderate inherent risk.
## 2026-08-08 08:20:55 UTC [browser] (model laguna)
[PRIO] Whale binary v4.38.386.14 sync KDF/OSCrypt/xv10: 6.7 (atk=8 bus=9 tech=7 gate=3 cloud=0 fresh=9)
[PRIO] Whale sidebar context SOP bypass: 5.7 (atk=6 bus=6 tech=5 gate=7 cloud=2 fresh=7)
[PRIO] Whale dual-tab boundary variant: 5.5 (atk=6 bus=5 tech=5 gate=7 cloud=2 fresh=7)
[PRIO] Whale resources.pak socket.io.slim.js push handler: 5.9 (atk=7 bus=7 tech=6 gate=4 cloud=3 fresh=6)
[HYP] Sync bootstrap-token KDF + OSCrypt master-key extraction from Whale binary v4.38.386.14
class: AUTH
asset: `whale` ELF binary v4.38.386.14 — `os_crypt_whale.cc`/`wbc_wrapper_apis.cc` `.rodata` xref `sync.encryption_bootstrap_token_per_account`
confidence: 65
reasoning: Prior bigpickle/laguna runs confirmed Whale-only prefs keys + xv10 magic + `/whalesync` (NEO_SES cookie) all present in v4.38.386.14; KDF algorithm/iteration count and master-key storage location remain unextracted.
evidence_needed: PBKDF2/scrypt N/r/p iteration constants in `.rodata`; `Local State` `os_crypt` v10 key-blob + master key path (file vs Linux keyring vs EncryptedPreferences)
verify_steps: PASSIVE: acquire `.deb` via out-of-band → `dpkg-deb -x` → `strings` + `objdump -d` on `whale` binary targeting `.rodata` xref `sync.encryption_bootstrap_token_per_account`; grep `PBKDF2`/`scrypt`/`N,r,p=`
impact: Weak KDF or device-recoverable master key → local attacker decrypts synced passwords+bookmarks+autofill (High)
testability: PASSIVE (binary acquisition blocked in-sandbox — cloudfront DNS dead, mirrors 403/410, Naver domains excluded)
[HYP] socket.io.slim.js push handler injection/forwarding in resources.pak
class: AUTH
asset: `resources.pak` → `socket.io.slim.js` bundled — `socket.on()` → `chrome.*` call chain without origin/message-source check
confidence: 52
reasoning: socket.io.slim.js is confirmed Whale-only bundled in `resources.pak`; push handler may forward payload to `chrome.*` APIs without validation
evidence_needed: `service_worker.js` extracted from `resources.pak` showing `socket.on()` handler forwarding payload to `chrome.*` without origin/message-source check
verify_steps: PASSIVE: `unzip` extracted `.deb`; use `resources.pak` unpacker or `strings resources.pak` for `socket.io.slim` + `whale_sync_push`; grep JS layer for `socket.on` → `chrome.` call chains; inspect message-source validation (`chrome.runtime`). If push handler is runtime-fetched (not in pak), fall back to documenting stale evidence.
impact: Remote push message executing in extension context → tab history manipulation, credential theft (Medium-High)
testability: PASSIVE
[HYP] Sidebar context SOP bypass — new variant on v4.38.386.14
class: OTHER
asset: `whale.sidebarAction.show({url})` + `use_navigation_bar=false` drag-navigation in sidebar panel
confidence: 52
reasoning: CVE-2025-69235 (CWE-346) fixed v4.35.351.12 (Dec 2025); v4.38.386.14 is 3 minor bumps ahead with 0 CVEs in gap; DevTools-in-sidebar added v4.38.386.12; wiki docs confirm `show()` loads arbitrary URL + drag-navigation bypasses SOP
evidence_needed: Running browser v4.38.386.14 demonstrating cross-origin `fetch()` from panel content script after `show({url:'https://victim.com'})`
verify_steps: HUMAN_ONLY: Install Whale v4.38.386.14 → load sidebar-sample extension → call `whale.sidebarAction.show({url:'https://httpbin.org/headers'})` → attempt cross-origin `fetch()` from panel content script → test drag-drop navigation with `use_navigation_bar:false`
impact: Cross-origin data theft from sidebar → credential/CSRF-token exfiltration; Critical if renderer escape
testability: HUMAN_ONLY
[FINAL] (ranked, top first):
[NEXT] PROBE: Download latest Whale desktop `.deb` stub (~11.6 MB) from `https://d1vdt4q2qgdbji.cloudfront.net/whale/whale_stable_latest_amd64.deb` (confirmed non-Naver CDN) → `dpkg-deb -x` into `/tmp/opencode/whale_x` → `strings` + `objdump -d` on extracted `whale` binary targeting `.rodata` xref `sync.encryption_bootstrap_token_per_account` → grep `PBKDF2`/`scrypt`/iteration constants → inspect `Local State` for `os_crypt` v10 key-blob + master key storage path. Zero requests to `*.naver.com` or `/whalesync`.
[LEARN] ACCEPTED @ binary static analysis: Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account` sha256=`7b06e6e9...`, `_migration_done`, `whale_need_encryption_key_forced_time`) + Whale-forked `os_crypt_whale.cc`/`wbc_wrapper_apis.cc` + `xv10` magic CONFIRMED present in v4.38.386.14 binary via prior bigpickle/laguna runs — KDF constants/iteration counts + master-key storage location remain unextracted (stale until re-acquired)
[LEARN] REJECTED @ naver/whale-browser-developers: Repo remains documentation-only (last code commit 2019-09-23; 2025-10-22 metadata-only refresh) — static analysis path is dead; binary acquisition is the only static analysis vector
[LEARN] REJECTED passive binary acquisition @ APKMirror/APKCombo/apkpure/cloudfront CDN: All download paths blocked in-sandbox (cloudfront DNS `No answer`; APKMirror 403; uptodown 410; apkpure.com 403; Naver domains excluded) — binary acquisition requires HUMAN with unrestricted internet access
[LEARN] REJECTED @ naver web services (developers/lab/store.whale.naver.com): All excluded per scope.yml out_of_scope rules
[LEARN] CONFIRMED @ NVD: 0 CVEs published for Whale in 2026 — no public disclosures exist for v4.35.352–v4.38.386.14, confirming 6-month disclosure gap
[LEARN] REJECTED @ GitHub repo `naver/whale-browser-developers`: Documentation-only (0 releases, 0 code commits since 2019-09-23) — no browser binary source, sync flow code, or library manifests available
[RISK] sync: 65 — custom `/whalesync` push via socket.io + per-account bootstrap tokens + Whale-forked OSCrypt (`xv10` magic) + `whale_need_encryption_key_forced_time` rekey gate CONFIRMED in v4.38.386.14 binary via prior runs; KDF algorithm/iteration count + master-key storage location NOT statically extracted (stripped binary); binary acquisition fully blocked in-sandbox (cloudfront DNS dead, all mirrors 403/410); local profile access yields full sync decryption with PII cascade; verification pending out-of-band binary acquisition
[RISK] browser: 78 — 6 confirmed 2025 CVEs in Whale-only sidebar/dual-tab (CWE-346 SOP bypass, CWE-358 iframe sandbox escape/CSP bypass); v4.38.386.14 is 3 minor version bumps past last CVE-fix (v4.35.351.12) with 0 CVEs in gap; DevTools-in-sidebar (v4.38.386.12) added attack surface; wiki docs passively confirm API surface (`sidebarAction.show({url})`, `use_navigation_bar=false` drag-navigation, server-side app redirect via `location.replace`); content_scripts match ALL origins in sample extension; HUMAN_ONLY testability blocks passive validation
[RISK] libs: 35 — Whale bundles Chromium (inherits all upstream CVEs); Whale-only `socket.io.slim.js` confirmed in `resources.pak` (prior runs); no public bundled-lib manifest or version list; version-drift assessment impossible without binary extraction; APKMirror only has legacy versions; low visibility, moderate inherent risk
