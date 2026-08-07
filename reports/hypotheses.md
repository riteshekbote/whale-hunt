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
