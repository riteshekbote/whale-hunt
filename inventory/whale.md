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
