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
  - | **VALID** | **0** | — |

- 13 lead(s) triaged at 2026-08-08 18:00 UTC — **0 VALID / 0 HOLD / 13 INVALID**
  - Full 7Q analysis per lead; uniform killer is Q4 (no passive proof) + Q7 (no evidence)
  - All binary-acquisition channels blocked; all verify_steps are HUMAN_ONLY or AUTH_HELPED
  - 5 models de-duplicated to 13 distinct lead categories — all INVALID
  - See `triage/run-2026-08-08-18-00.md` for full analysis

- 2 lead(s) marked VALID at 2026-08-08 19:06:55 UTC
  - valid-bugs.md
  - | **VALID** | 0 | No lead demonstrates an actual vulnerability on v4.38.386.14 |

- 1 lead(s) marked VALID at 2026-08-08 20:15:55 UTC
  - | Q6 Not rejected? | YES — SOP bypass is a valid vulnerability class |

- 13 lead(s) triaged at 2026-08-08 21:18 UTC — **0 VALID / 4 INVALID / 9 HOLD**
  - Full 7Q analysis per lead; uniform killer is Q4 (no passive proof) + Q7 (no evidence)
  - 5 models de-duplicated to 13 distinct lead categories
  - 4 INVALID: Lead 03 (dupes CVE-2022-24072/2024-40618), Lead 08 (no_doom + below threshold), Lead 11 (speculative)
  - 9 HOLD: all require binary artifact absent from sandbox or HUMAN_ONLY/AUTH_HELPED verification
  - See `triage/run-2026-08-08-21-18.md` for full analysis

- 1 lead(s) marked VALID at 2026-08-08 21:22:07 UTC
  - VALID:    0

- 1 lead(s) marked VALID at 2026-08-08 21:51:20 UTC
  - **Disclosure policy:** TBD (operator-provided channel not yet confirmed) — I'll note this in any VALID verdict until the channel is finalized.

- 10 lead(s) triaged at 2026-08-08 22:13 UTC — **0 VALID / 4 INVALID / 6 HOLD**
  - Full 7Q analysis per lead; uniform killer is Q4 (no passive proof) + Q7 (no evidence)
  - 5 models de-duplicated to 10 distinct lead categories (longcat produced no hypotheses)
  - 4 INVALID: Lead 02 (dupes CVE-2022-24072/2024-40618), Lead 05 (no specific vuln, best-practice), Lead 08 (out of scope, Naver web service)
  - 6 HOLD: all require binary artifact absent from sandbox or HUMAN_ONLY/AUTH_HELPED verification
  - Reposcan pipeline REGRESSED: scanning gladiaio/ instead of whale/ since ~17:57 UTC, returning 0 hits
  - See `triage/run-2026-08-08-22-13.md` for full analysis

- 1 lead(s) marked VALID at 2026-08-08 22:20:25 UTC
  - - **Disclosure policy:** TBD (operator-provided channel not yet confirmed) — I'll note this in any VALID verdict until the channel is finalized.

- 0 lead(s) triaged at 2026-08-08 22:51 UTC — **0 VALID / 0 INVALID / 0 HOLD**
  - LEADS section empty; no new hypotheses since 22:13 UTC cycle
  - Reposcan still scanning wrong target (gladiaio/ not whale/); 0 hits
  - See `triage/run-2026-08-08-22-51.md` for status

- 2 lead(s) marked VALID at 2026-08-08 22:53:34 UTC
  - VALID bugs total: 0
  - **VALID bugs total: 0** — disclosure channel TBD per scope.yml.

- 4 lead(s) marked VALID at 2026-08-08 23:16:58 UTC
  - | Q6 | YES | SOP bypass is a valid vuln class |
  - | Q6 | YES | Valid class |
  - | Q6 | YES | Valid class |
  - **VALID count: 0** — no reportable vulnerability this cycle. Disclosure channel per scope.yml is TBD (operator-provided; not yet confirmed).

- 14 lead(s) triaged at 2026-08-08 23:48 UTC — **0 VALID / 6 INVALID / 8 HOLD**
  - Full 7Q analysis per lead; uniform killer is Q4 (no passive proof) + Q7 (no evidence)
  - 5 models de-duplicated to 14 distinct lead categories (longcat produced no hypotheses)
  - 6 INVALID: Lead 01/03/11 (dupes documented CVE classes), Lead 02 (dupes CVE-2022-24072/2024-40618), Lead 05 (no specific vuln, best-practice), Lead 08 (out of scope, Naver web service)
  - 8 HOLD: all require binary artifact absent from sandbox or HUMAN_ONLY/AUTH_HELPED verification
  - Reposcan still scanning wrong target (gladiaio/ not whale/); 0 hits
  - See `triage/run-2026-08-08-23-48.md` for full analysis

- 2 lead(s) marked VALID at 2026-08-08 23:56:53 UTC
  - valid-bugs.md
  - | **VALID** | 0 | — |

- 2 lead(s) marked VALID at 2026-08-09 00:39:19 UTC
  - | Q6 Not rejected? | YES — SOP bypass is a valid vuln class |
  - | **VALID** | **0** | — |

- 2 lead(s) marked VALID at 2026-08-09 02:55:26 UTC
  - | Q6 Not rejected? | **YES** — SOP bypass is a valid vuln class |
  - | **VALID** | **0** | — |
