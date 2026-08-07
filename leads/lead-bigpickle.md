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
