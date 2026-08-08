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

- 1 lead(s) marked VALID at 2026-08-08 09:06:43 UTC
  - | **VALID** | 0 | — |

- 1 lead(s) marked VALID at 2026-08-08 10:02:28 UTC
  - | **VALID** | 0 | No lead passes all 7 gates |

- 11 lead(s) triaged at 2026-08-08 13:00 UTC — **0 VALID / 0 HOLD / 11 INVALID**
  - All leads fail Q4 (no passive GET/HEAD proof) and Q7 (no evidence of actual vulnerability)
  - See `triage/run-2026-08-08-13-00.md` for full 7Q analysis per lead

- 2 lead(s) marked VALID at 2026-08-08 13:16:49 UTC
  - valid-bugs.md
  - | **VALID** | 0 | No lead passes all 7 gates |

- 11 lead(s) triaged at 2026-08-08 14:00 UTC — **0 VALID / 0 HOLD / 11 INVALID**
  - All leads fail Q4 (no passive GET/HEAD proof) and Q7 (no evidence of actual vulnerability)
  - See `triage/run-2026-08-08-14-00.md` for full 7Q analysis per lead

- 14 lead(s) triaged at 2026-08-08 14:38 UTC — **0 VALID / 0 HOLD / 14 INVALID**
  - Full 7Q analysis per lead; uniform killer is Q4 (no passive proof) + Q7 (no evidence)
  - All binary-acquisition channels blocked; most leads require HUMAN_ONLY interactive testing
  - See `triage/run-2026-08-08-14-38.md` for full analysis

- 1 lead(s) marked VALID at 2026-08-08 15:52:59 UTC
  - | **VALID** | **0** | — |

- 1 lead(s) marked VALID at 2026-08-08 17:00:02 UTC
  - | **VALID** | **0** |
