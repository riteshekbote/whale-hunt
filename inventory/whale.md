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
