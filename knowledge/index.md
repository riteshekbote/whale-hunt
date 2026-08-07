# Knowledge Base (seed)

## Program rules (from scope.yml)
- Eligible: latest-version Whale browser bugs (must reproduce on latest at report time); third-party libraries used ONLY by Whale (not Chromium); synchronization bugs
- NOT eligible: all Naver web services (*.naver.com, *.navercorp.com), web servers of built-in extensions (Papago, Belli), URL preview spoofing in status bar, bypassing XSS Auditor, IETab extension, SafeBrowsing FP/FN
- Client-side software: static source analysis + repro-first validation; no server probing
- Secrets in commits: sha256 only, never raw

## Baseline surface (2026-08-07 passive recon)
- Official GitHub repo: naver/whale-browser-developers (no public releases API; repo is developers channel)
- Naver org is HUGE: reposcan clones ONLY whale-name repos
- Known excluded classes to never report: XSS Auditor bypass, status-bar URL preview spoofing, SafeBrowsing FP/FN, anything in IETab

## Rejected / parked
- (none yet)
- 2026-08-07 REJECTED BCP47 @ store.whale.naver.com: Issue #23 maps to Naver web service (store.whale.naver.com/*), explicitly excluded from scope per scope.yml out_of_scope rules
- 2026-08-07 ACCEPTED OTHER @ sidebar environment: CVE-2025-69235 (CWE-346) confirmed — SOP bypass in sidebar context, fixed in v4.35.351.12
- 2026-08-07 ACCEPTED OTHER @ dual-tab environment: CVE-2025-53600, 62584 confirmed — SOP bypass in dual-tab context, fixed in v4.33.325.17
- 2026-08-07 ACCEPTED XSS @ extension API: CVE-2022-24072, CVE-2024-40618 confirmed — injection/XSS via devtools API and built-in extension processing
- 2026-08-07 REJECTED browser source @ naver/whale-browser-developers: Repo is documentation-only; no browser binary source, sync flow code, or bundled library manifests available for static analysis
- 2026-08-07 REJECTED naver web services @ developers.whale.naver.com, lab.whale.naver.com, store.whale.naver.com: All excluded per scope rules (Naver web services)
