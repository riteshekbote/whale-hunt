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
