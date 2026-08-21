## 2026-08-21 19:33:58 UTC [api] (model mimo)
[PRIO] whale desktop binary epoch-key response parser | score=6.1 | attack_surface=8 | business_value=7 | tech_exposure=6 | gate_ease=7 | cloud_surface=4 | freshness=5
[HYP] Whale epoch-key response verifierless signing material extraction
class: OTHER
asset: whale binary epoch-key response parser (0xc0d5c91–0xc0d5eb6)
confidence: 70
reasoning: The response parser at 0xc0d5c91–0xc0d5eb6 consumes plain JSON (expires_in/access_token/id_token/error) with ZERO crypto-helper calls — no HMAC verification, no signature check, no nonce/timestamp validation before the epoch-key material is passed downstream to sync encryption setup. The signing pipeline (combine_fn c0d70f0 → domain-separated HMAC whale:hmac:+v1) only covers requests OUTBOUND; the response is trusted as-is. An attacker who can inject or modify the epoch-key HTTP response (via MITM, extension, or compromised proxy) could supply arbitrary access_token/id_token values that feed directly into the sync encryption key derivation chain.
evidence_needed: Full control-flow trace from response parser 0xc0d5c91 through OnEpochKeyConfirmed bridge to sync encryption setup; confirmation that no intermediate verification layer exists between response parse and key usage.
verify_steps: HUMAN_ONLY: Re-acquire binary from repo.whale.naver.com, extract, objdump/ghidra trace the response parser 0xc0d5c91–0xc0d5eb6 forward through all callers to confirm zero verification before key material is consumed.
impact: Attacker-controlled epoch-key response could inject arbitrary sync encryption keys, enabling full data exfiltration or sync session hijack. Severity: HIGH (CVSS 7.5+ if MITM achievable; MEDIUM if extension-gated).
testability: HUMAN_ONLY
[FINAL] Whale epoch-key response verifierless signing material extraction | conf=70 | class=OTHER | asset=binary epoch-key response parser | testability=HUMAN_ONLY
[LEARN] ACCEPTED class @ epoch-key response parser verification absence: binary code at 0xc0d5c91–0xc0d5eb6 consumes plain JSON with zero crypto-helper calls — asymmetric response-trust design confirmed via xref-exhaustive consumer confinement
[LEARN] ACCEPTED class @ request-response asymmetry: client signs outbound requests (EVP_DigestSign, domain-separated whale:hmac:+v1, X-CSRF-Token/X-Timestamp/X-Nonce binding) but performs zero response verification — code-proven, not hypothesis
[LEARN] REJECTED class @ KDF debug string sync evidence: %s: kdf key len: %d proven to be libsrtp/WebRTC debug output, not sync-crypto evidence — no longer a signal
[LEARN] REJECTED class @ epoch-key absence-of-crypto claim: pinned P-256 SPKI @0x2968510 + whale:hmac:/v1 labels falsify "zero client-side verification material" — the verification gap is specifically in the RESPONSE path, not the request path
[RISK] sync: 70 reason | epoch-key response lacks cryptographic verification; combined with Whale's custom signing layer (WBC + authkey_fetcher fork), a MITM or extension-gated attacker could inject arbitrary sync encryption keys. Binary acquisition channel (repo.whale.naver.com) is live and hash-pinned, making this a reproducible finding for a HUMAN analyst.
