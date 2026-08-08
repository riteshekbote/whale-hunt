# Validated Bugs

- 2026-08-07 ~18:00 UTC - SEED STATE: 0 valid bugs. Pipeline not yet run; hypotheses are recon-based and UNVALIDATED.

- 2026-08-07 23:53 UTC — Triage run (14 hypotheses): 0 VALID / 4 HOLD / 10 INVALID.
- 2026-08-08 04:30 UTC — Follow-up triage (same 14 hypotheses, no new evidence): **0 VALID / 0 HOLD / 14 INVALID**.

| Verdict | Count | Summary |
|---------|-------|---------|
| **VALID** | 0 | No lead passes all 7 gates |
| **HOLD** | 0 | Prior HOLDs resolved to INVALID — no new evidence gathered |
| **INVALID** | 14 | All leads are recon-level hypotheses without proof of a vulnerability on v4.38.386.14 |

**Key findings:**
- **No probe results** were produced (`probe-results.txt` empty) — all leads are model hypotheses without HTTP-level evidence
- **Sidebar SOP bypass** was the #1 hypothesis across all 5 models, but every instance fails Q4 (HUMAN_ONLY), Q5 (no novel variant evidence), and Q7 (speculative)
- **Most promising recon surface**: Whale sync engine — binary static analysis confirmed Whale-only prefs keys, custom OSCrypt fork, and socket.io bundled in core, but token-storage format and KDF constants remain **unverified**
- **0 valid bugs** to report. Disclosure channel per scope.yml is TBD (operator-provided; not yet confirmed).

- 3 lead(s) marked VALID at 2026-08-08 04:39:28 UTC
  - VALID  : 0
  - | **VALID** | 0 | No lead passes all 7 gates |
  - VALID  : 0

- 2 lead(s) marked VALID at 2026-08-08 06:09:27 UTC
  - | **VALID** | 0 | — |
  - | **VALID** | 0 | — |
