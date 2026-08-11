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

- 0 lead(s) triaged at 2026-08-09 04:10 UTC — **0 VALID / 0 HOLD / 0 INVALID**
  - LEADS section empty; no new hypotheses or security probe results
  - Reposcan pipeline still scanning wrong target (gladiaio/ not whale/); 0 hits
  - All prior hypotheses remain INVALID (Q4/Q7 killers)
  - See `triage/run-2026-08-09-04-10.md` for status

- 1 lead(s) marked VALID at 2026-08-09 09:32:58 UTC
  - | **VALID** | 0 | — |

- 2 lead(s) marked VALID at 2026-08-09 11:06:49 UTC
  - | 7 | Multiplay URL Disclosure | **HOLD** | Potentially valid; binary shows URLs sync verbatim; needs active testing |
  - | 11 | Scrapbook Authz Bypass | **HOLD** | Potentially valid; needs active testing; scope unclear |

- 1 lead(s) marked VALID at 2026-08-09 11:52:58 UTC
  - | **VALID** | 0 | — |

- 1 lead(s) marked VALID at 2026-08-09 13:28:34 UTC
  - VALID : 0

- 16 lead(s) triaged at 2026-08-09 14:22 UTC — **0 VALID / 6 INVALID / 10 HOLD**
  - Full 7Q analysis per lead; uniform killer is Q4 (no passive proof) + Q7 (no evidence)
  - 4 active models de-duplicated to 16 distinct lead categories (longcat produced no hypotheses)
  - 6 INVALID: Lead 01/03/11 (dupes documented CVE classes), Lead 02 (dupes CVE-2022-24072/2024-40618), Lead 05 (no specific vuln, best-practice), Lead 08 (out of scope Naver web service, no_doom)
  - 10 HOLD: all require binary artifact absent from sandbox or HUMAN_ONLY/AUTH_HELPED verification
  - Reposcan pipeline still REGRESSED (scanning gladiaio/ not whale/); 0 hits
  - See `triage/run-2026-08-09-14-22.md` for full analysis

- 1 lead(s) marked VALID at 2026-08-09 14:33:16 UTC
  - VALID finding is the Naver Whale security channel referenced in scope.yml —

- 1 lead(s) marked VALID at 2026-08-09 16:22:47 UTC
  - **Verdict: HOLD** — passes Q1–Q3, Q5–Q6; fails Q4 but has a concrete AUTH_HELPED path (two authorized accounts). Could be elevated to VALID if a human tester can run the interactive test. Not reportab

- 12 lead(s) marked VALID at 2026-08-09 17:55:25 UTC
  - | Q6 | YES | SOP bypass is a valid vuln class |
  - | Q6 | YES | Valid vuln class |
  - | Q6 | YES | Valid vuln class |
  - | Q6 | YES | Authz bypass is a valid class |
  - **Verdict: HOLD** — passes Q1-Q3, Q5-Q6. Blocker is evidential: needs a human to run a two-account interactive session on v4.38.386.14. Could elevate to VALID if cross-session data leakage is demonstr
  - | Q6 | YES | Valid class |
  - | Q6 | YES | Valid class |
  - **Verdict: HOLD** — strongest sync-surface lead (Whale-specific OSCrypt fork confirmed). Blocker is purely evidential: binary required. Elevates to VALID upon binary delivery + static extraction of to
  - | Q6 | YES | Valid class |
  - **Verdict: HOLD** — passes Q1-Q3, Q6. Blocker is evidential: APK unreachable in-sandbox (Uptodown download endpoint returns 400 errorCode -51, resolver JS 410). Could elevate to VALID with binary deli
  - | **VALID** | 0 | — |
  - | **VALID** | 0 | — |

- 10 lead(s) triaged at 2026-08-09 19:52 UTC — **0 VALID / 5 HOLD / 5 INVALID**
  - Full 7Q analysis per lead; Q4 (passive proof) is the universal blocker for all binary-dependent leads
  - 5 INVALID: Lead 05 (dupes CVE-2022-24072/2024-40618), Lead 06 (no new variant), Lead 07 (no concrete finding), Lead 08 (below confidence threshold), Lead 10 (reposcan REGRESSED)
  - 5 HOLD: Lead 01 (sidebar Linux CPE gap), Lead 02 (sync KDF), Lead 03 (bootstrap-token OSCrypt), Lead 04 (Android keystore), Lead 09 (Scrapbook/Multiplay)
  - Reposcan pipeline still REGRESSED (scanning gladiaio/ not whale/); 0 hits
  - See `triage/run-2026-08-09-19-52.md` for full analysis

- 9 lead(s) marked VALID at 2026-08-09 19:56:28 UTC
  - | Q6 | YES | SOP bypass is a valid vuln class |
  - | Q6 | YES | Valid vuln class |
  - | Q6 | YES | Valid class |
  - | Q6 | YES | Valid class |
  - | Q6 | YES | Valid vuln class |
  - | Q6 | YES | Authz bypass is a valid class |
  - | **VALID** | 0 | — |
  - - **Verdict: HOLD** — passes Q1-Q3, Q6. Blocker is evidential: APK unreachable in-sandbox (Uptodown download endpoint returns 400 errorCode -51, resolver JS 410). Could elevate to VALID with binary de
  - | **VALID** | 0 | — |

- 1 lead(s) marked VALID at 2026-08-09 20:20:07 UTC
  - | **VALID** | 0 | — |

- 2 lead(s) marked VALID at 2026-08-09 20:57:37 UTC
  - | **VALID** | 0 | — |
  - **VALID bugs count: 0** — nothing to report at this time.

- 12 lead(s) marked VALID at 2026-08-09 21:54:53 UTC
  - valid-bugs.md
  - | Q6 Not rejected? | YES | SOP bypass is a valid vuln class |
  - | Q6 | YES | Valid vuln class |
  - | Q6 | YES | Valid class |
  - | Q6 | YES | Valid class |
  - **Verdict: HOLD** — strongest sync-surface lead (Whale-specific OSCrypt fork confirmed). Blocker is purely evidential: binary required. Elevates to VALID upon binary delivery + static extraction.
  - | Q6 | YES | Valid class |
  - | Q6 | YES | Valid class |
  - | Q6 | YES | Valid class |
  - **Verdict: HOLD** — APK acquisition blocked. Elevates to VALID with binary delivery.
  - | Q6 | YES | Valid class |
  - | **VALID** | **0** | — |

- 12 lead(s) marked VALID at 2026-08-09 22:21:01 UTC
  - valid-bugs.md
  - | Q6 Not rejected? | YES | SOP bypass is a valid vuln class |
  - | Q6 | YES | Valid class |
  - | Q6 | YES | Valid vuln class |
  - | Q6 | YES | Valid class |
  - **Verdict: HOLD** — strongest sync-surface lead. Whale-only OSCrypt fork + custom prefs keys confirmed in prior binary runs. Blocker is purely evidential: binary required. **Elevates to VALID upon bin
  - | Q6 | YES | Valid class |
  - | Q6 | YES | Valid class |
  - | Q6 | YES | Valid class |
  - | Q6 | YES | Valid class |
  - **Verdict: HOLD** — APK acquisition blocked. **Elevates to VALID with binary delivery.**
  - | **VALID** | **0** | — |

- 9 lead(s) marked VALID at 2026-08-09 23:54:19 UTC
  - | Q6 Not rejected? | **YES** | SOP bypass is a valid vuln class |
  - | Q6 | **YES** | Valid class |
  - | Q6 | **YES** | Valid vuln class |
  - | Q6 | **YES** | Valid class |
  - | Q6 | **YES** | Valid class |
  - | Q6 | **YES** | Valid class |
  - | Q6 | **YES** | Valid class |
  - | Q6 | **YES** | Valid class |
  - | **VALID** | **0** | — |

- 3 lead(s) marked VALID at 2026-08-10 03:04:14 UTC
  - | Q6 Not rejected? | **YES** | SOP bypass is a valid vuln class |
  - | Q6 Not rejected? | **YES** | Weak crypto is a valid finding |
  - **Verdict: HOLD** — strongest sync-surface lead. Whale-only prefs keys (`sync.encryption_bootstrap_token_per_account`, `whale_need_encryption_key_forced_time`) and custom OSCrypt fork confirmed in pri

- 12 lead(s) triaged at 2026-08-10 08:18 UTC — **0 VALID / 4 HOLD / 8 INVALID**
  - Full 7Q analysis per lead; Q4 (passive proof) is the universal blocker for all 12 leads
  - 5 models de-duplicated to 12 distinct lead categories
  - 8 INVALID: Lead 01/02/03/10 (dupes fixed CVE classes, Q4/Q5/Q7), Lead 06 (socket.io unreachable + below threshold), Lead 07 (version drift, no specific vuln), Lead 08 (/whalesync/reset, server OOS), Lead 12 (Scrapbook, scope unclear)
  - 4 HOLD: Lead 04 (sync KDF, binary required), Lead 05 (bootstrap-token OSCrypt, binary required), Lead 09 (Multiplay URL disclosure, AUTH_HELPED), Lead 11 (Android keystore, APK required)
  - All binary-acquisition channels remain 100% blocked; reposcan pipeline still REGRESSED (scanning gladiaio/ not whale/)
  - See `triage/run-2026-08-10-08-18.md` for full analysis

- 1 lead(s) marked VALID at 2026-08-10 08:23:46 UTC
  - | **VALID** | 0 |

- 17 lead(s) marked VALID at 2026-08-10 12:06:58 UTC
  - | Q6 Not rejected? | YES | SOP bypass is a valid vuln class |
  - | Q6 | YES | Valid vuln class |
  - | Q6 | YES | Weak crypto is a valid finding |
  - **Verdict: HOLD** — Passes Q1-Q3, Q5-Q6. Blocker is purely evidential (Q4): binary required to extract KDF constants. Whale-only OSCrypt fork (`xv10` magic, `os_crypt_whale.cc`, custom prefs keys) con
  - | Q6 | YES | Valid class |
  - **Verdict: HOLD** — Passes Q1-Q3, Q5-Q6. Same blocker as Lead 03: binary required. Elevates to VALID upon binary delivery + objdump showing plaintext (non-OSCrypt-v10) token storage in Preferences.
  - | Q6 | YES | Valid class |
  - | Q6 | YES | Valid class |
  - | Q6 | YES | Valid class |
  - **Verdict: HOLD** — Passes Q1-Q3, Q5-Q6. Blocker is Q4 (AUTH_HELPED). Could elevate to VALID if a human tester runs a two-account session and demonstrates cross-session URL leakage (especially token-b
  - | Q6 | YES | Valid class |
  - **Verdict: HOLD** — Passes Q1-Q3, Q5-Q6. Blocker is Q4. Could elevate to VALID if human tester demonstrates inviteCode replay or empty-password room join on v4.38.386.14.
  - | Q6 | YES | Valid class |
  - **Verdict: HOLD** — Passes Q1-Q3, Q5-Q6. Blocker is Q4 (APK acquisition). SHA256 pinned (`3c723291…`) but download channels 100% blocked. Elevates to VALID with APK delivery + decompilation showing we
  - | Q6 | YES | Valid class |
  - **Verdict: HOLD** — Passes Q1-Q3, Q5-Q6. Blocker is Q4. Elevates to VALID upon binary delivery showing plaintext token storage outside Chromium token_service.
  - | **VALID** | **0** | — |

- 2 lead(s) marked VALID at 2026-08-10 14:53:02 UTC
  - Verdict: **HOLD** — Recurring CVE class (6 sidebar/dual-tab CVEs in 2025) makes this a valid hypothesis, but no variant demonstrated in current binary. Blocked on binary acquisition (cloudfront DNS no
  - **VALID: 0**

- 12 lead(s) marked VALID at 2026-08-10 15:47:06 UTC
  - | Q6 | YES | SOP bypass is a valid vuln class |
  - | Q6 | YES | Valid vuln class |
  - | Q6 | YES | Valid vuln class |
  - | Q6 | YES | Weak crypto is a valid finding |
  - **Verdict: HOLD** — Passes Q1, Q3, Q6. Blocker is purely evidential (Q4). Whale-only OSCrypt fork (`xv10` magic, `os_crypt_whale.cc`, custom prefs keys `sync.encryption_bootstrap_token_per_account`, `
  - | Q6 | YES | Authz bypass is valid class |
  - **Verdict: HOLD** — Passes Q1, Q2, Q3, Q6. Blocker is Q4 (AUTH_HELPED). Could elevate to VALID if human tester runs a two-account session and demonstrates cross-session URL/DOM leakage beyond intended
  - | Q6 | YES | Valid vuln class |
  - | Q6 | YES | Valid class |
  - **Verdict: HOLD** — Same as Lead 04. Whale-only OSCrypt fork confirmed but KDF unextracted. Blocker is Q4. Elevates to VALID upon binary delivery. Not reportable now.
  - | Q6 | YES | Valid class |
  - | **VALID** | **0** | — |

- 13 lead(s) marked VALID at 2026-08-10 16:43:37 UTC
  - | Q6 | YES | SOP bypass is a valid vuln class |
  - | Q6 | YES | Valid class |
  - | Q6 | YES | Valid class (when novel) |
  - | Q6 | YES | Weak crypto is a valid finding |
  - **VERDICT: HOLD** — Passes Q1, Q2, Q3, Q6. Blocker is purely evidential (Q4): binary artifact required. Whale-specific OSCrypt deviation is confirmed real and historically where weak crypto hides, but
  - | Q6 | YES | Valid class |
  - **VERDICT: HOLD** — Passes Q1, Q2, Q3, Q6. Same blocker as Lead 04: binary required. **Elevates to VALID upon:** binary delivery showing `sync.encryption_bootstrap_token_per_account` stored as plainte
  - | Q6 | YES | Valid class |
  - | Q6 | YES | Authz bypass / info disclosure is valid |
  - **VERDICT: HOLD** — Passes Q1, Q2, Q3, Q6. Blocker is Q4 (AUTH_HELPED). **Elevates to VALID upon:** two-account interactive test on v4.38.386.14 demonstrating joiner observes (a) token-bearing query s
  - | Q6 | YES | Authz bypass is valid |
  - | Q6 | YES | Weak crypto is valid |
  - **VERDICT: HOLD** — Passes Q1, Q2, Q3, Q6. Blocker is Q4 (APK acquisition). **Elevates to VALID upon:** APK delivery → decompilation showing `EncryptedSharedPreferences` with hardcoded key, or sync ke

- 14 lead(s) marked VALID at 2026-08-10 17:45:19 UTC
  - | Q6 Not rejected? | YES | SOP bypass is a valid vuln class |
  - | Q6 Not rejected? | YES | Valid vuln class |
  - | Q6 Not rejected? | YES | XSS/injection is a valid class |
  - | Q6 Not rejected? | YES | Weak crypto is a valid finding |
  - **Verdict: HOLD** — Passes Q1, Q2, Q3, Q6. Fails Q4 (no passive proof path; binary blocked). Cannot be elevated to VALID without binary delivery + static extraction showing weak KDF or plaintext token
  - | Q6 Not rejected? | YES | Valid class |
  - | Q6 Not rejected? | YES | Valid class |
  - | Q6 Not rejected? | YES | Valid class when specific |
  - | Q6 Not rejected? | YES | Info disclosure / authz bypass is valid |
  - **Verdict: HOLD** — Passes Q1, Q2, Q3, Q6. Blocker is Q4 (AUTH_HELPED). Could elevate to VALID if a human tester runs a two-account session and demonstrates joiner observes token-bearing query strings
  - | Q6 Not rejected? | YES | Authz bypass is valid |
  - **Verdict: HOLD** — Passes Q2, Q3, Q6. Fails Q4 (AUTH_HELPED). Blocker is Q1 (scope ambiguous — Scrapbook "coming soon" feature) and Q4. Could elevate to VALID with interactive testing but scope needs
  - | Q6 Not rejected? | YES | Valid class |
  - **Verdict: HOLD** — Passes Q1, Q2, Q3, Q6. Blocker is Q4 (APK acquisition). Could elevate to VALID with APK delivery + decompilation showing weak key storage (e.g., EncryptedSharedPreferences with har

- 8 lead(s) marked VALID at 2026-08-10 19:48:46 UTC
  - **Verdict: HOLD** — Passes Q1, Q2, Q3, Q5, Q6. Fails purely on Q4 (evidential). Whale-only OSCrypt fork (`xv10` magic, `os_crypt_whale.cc`, custom prefs keys `sync.encryption_bootstrap_token_per_accou
  - **Verdict: HOLD** — Same blocker as Lead 04. Whale-only prefs deviation confirmed but envelope format unverified. **Elevates to VALID upon:** binary delivery showing `sync.encryption_bootstrap_token_p
  - **Verdict: HOLD** — Passes Q1-Q3, Q5-Q6. **Elevates to VALID upon:** APK delivery → decompilation showing `EncryptedSharedPreferences` with hardcoded key, or sync key stored in plaintext World-Readabl
  - **Verdict: HOLD** — Passes Q1-Q3, Q5-Q6. Blocker is Q4 (AUTH_HELPED). **Elevates to VALID upon:** two-account interactive test on v4.38.386.14 demonstrating joiner observes (a) token-bearing query str
  - **Verdict: HOLD** — Passes Q1-Q3, Q5-Q6. **Elevates to VALID upon:** binary delivery showing Whale refresh/access tokens stored outside Chromium token_service (plaintext in Preferences/Cookies).
  - **Verdict: HOLD** — Passes Q2-Q3, Q5-Q6. Fails Q1 (scope ambiguous — feature may not be GA) and Q4. **Elevates to VALID with:** scope confirmation + interactive test.
- **Verdict: HOLD** — Passes Q1-Q3, Q5-Q6. **Elevates to VALID upon:** interactive test showing inviteCode replay or empty-password room join on v4.38.386.14.
- | **VALID** | **0** | — |

- 15 lead(s) triaged at 2026-08-10 20:26 UTC — **0 VALID / 6 HOLD / 9 INVALID**
  - Full 7Q analysis per lead; Q4 (passive proof) is the universal blocker for all 15 leads
  - 5 models de-duplicated to 15 distinct lead categories (1 new: installer DLL-execution regression)
  - 9 INVALID: Lead 01/02/03 (dupes fixed CVE classes), Lead 07 (inventory gap, best-practice), Lead 08 (unreachable handler + dupe), Lead 09 (out of scope Naver web service), Lead 12 (scope unclear), Lead 13 (low confidence), Lead 14 (adjacent to fixed CVE)
  - 6 HOLD: Lead 04 (sync KDF, binary required), Lead 05 (bootstrap-token OSCrypt, binary required), Lead 06 (Android keystore, APK required), Lead 10 (Multiplay URL, AUTH_HELPED), Lead 11 (refresh-token, binary required), Lead 15 (installer DLL-execution, binary required)
  - All binary-acquisition channels remain 100% blocked; NVD endpoint recovered HTTP 200 (0 CVEs in 2026 re-confirmed)
  - See `triage/run-2026-08-10-20-26.md` for full analysis

- 7 lead(s) marked VALID at 2026-08-10 20:33:17 UTC
  - - **Verdict: HOLD** — Passes Q1-Q3, Q5-Q6. **Elevates to VALID upon:** APK delivery → decompilation showing `EncryptedSharedPreferences` with hardcoded key, or sync key stored in plaintext World-Reada
  - - **Verdict: HOLD** — Passes Q1-Q3, Q5-Q6. Blocker is Q4 (AUTH_HELPED). **Elevates to VALID upon:** two-account interactive test on v4.38.386.14 demonstrating joiner observes (a) token-bearing query s
  - - **Verdict: HOLD** — Passes Q1-Q3, Q5-Q6. **Elevates to VALID upon:** binary delivery showing Whale refresh/access tokens stored outside Chromium token_service (plaintext in Preferences/Cookies).
  - - **Verdict: HOLD** — Passes Q2-Q3, Q5-Q6. Fails Q1 (scope ambiguous — feature may not be GA) and Q4. **Elevates to VALID with:** scope confirmation + interactive test.
  - -  - **Verdict: HOLD** — Passes Q1-Q3, Q5-Q6. **Elevates to VALID upon:** interactive test showing inviteCode replay or empty-password room join on v4.38.386.14.
  - +- **Verdict: HOLD** — Passes Q1-Q3, Q5-Q6. **Elevates to VALID upon:** interactive test showing inviteCode replay or empty-password room join on v4.38.386.14.
  - | **VALID** | **0** |

- 1 lead(s) marked VALID at 2026-08-10 21:31:34 UTC
  - | **VALID** | 0 | — |

- 3 lead(s) triaged at 2026-08-10 22:21 UTC — **0 VALID / 2 HOLD / 1 INVALID**
  - Full 7Q analysis per lead; Q4 (passive proof) is the universal blocker for all 3 leads
  - 3 active models de-duplicated to 3 distinct lead categories (ling3, laguna produced no output)
  - 1 INVALID: Lead 03 (sidebar SOP bypass — dupes CVE-2025-69234/69235; sample extension is a tutorial, not a vuln)
  - 2 HOLD: Lead 01 (desktop sync KDF, binary required), Lead 02 (Android sync KDF, APK required)
  - All binary-acquisition channels remain 100% blocked; reposcan pipeline still REGRESSED (gladiaio/ not whale/)
  - See `triage/run-2026-08-10-22-21.md` for full analysis

- 2 lead(s) marked VALID at 2026-08-10 22:26:33 UTC
  - - +- **Verdict: HOLD** — Passes Q1-Q3, Q5-Q6. **Elevates to VALID upon:** interactive test showing inviteCode replay or empty-password room join on v4.38.386.14.
  - | **VALID** | 0 | — |

- 1 lead(s) marked VALID at 2026-08-10 23:03:56 UTC
  - | **VALID** | 0 | — |

- 12 lead(s) marked VALID at 2026-08-11 04:13:58 UTC
  - | Q6 | Not on rejected list? | YES — SOP bypass is a valid class |
  - | Q6 | Not on rejected list? | YES — weak crypto is a valid finding |
  - | Q7 | Reasonable triager accept? | **NO** — valid hypothesis but zero evidence of actual weakness. Binary acquisition blocked. |
  - **Verdict: HOLD** — Passes Q1–Q3, Q5–Q6. Blocker is purely evidential (Q4). Whale-only OSCrypt fork confirmed real. **Elevates to VALID upon:** binary delivery + static extraction showing weak KDF (e.
  - | Q6 | Not on rejected list? | YES — SOP/CSP bypass is valid |
  - | Q6 | Not on rejected list? | YES — XSS is valid |
  - | Q6 | Not on rejected list? | YES — authz bypass is valid |
  - **Verdict: HOLD** — Passes Q1–Q3, Q6. Blocker is Q4 (AUTH_HELPED). **Elevates to VALID upon:** two-account interactive test on v4.38.386.14 demonstrating joiner observes token-bearing query strings, h
  - | Q6 | Not on rejected list? | YES — XSS is valid |
  - | Q6 | Not on rejected list? | YES — SOP bypass is valid |
  - | **VALID** | 0 | — |
  - **VALID count: 0** — No lead passes all 7 gates.

- 13 lead(s) triaged at 2026-08-11 06:41 UTC — **0 VALID / 5 HOLD / 8 INVALID**
  - Full 7Q analysis per lead; Q4 (passive proof) is the universal blocker for all 13 leads
  - 5 models de-duplicated to 13 distinct lead categories
  - 8 INVALID: Lead 01/02/03/12 (dupes fixed CVE classes, Q4/Q5/Q7), Lead 07 (attacker reach unclear + OOS sync server), Lead 08 (OOS Naver web service), Lead 11 (no specific vuln, best-practice), Lead 13 (reposcan pipeline regression — not a vuln)
  - 5 HOLD: Lead 04 (desktop sync KDF, binary required), Lead 05 (bootstrap-token OSCrypt, binary required), Lead 06 (Android sync KDF, APK required), Lead 09 (Multiplay URL disclosure, AUTH_HELPED), Lead 10 (Scrapbook authz, scope ambiguous + AUTH_HELPED)
  - All binary-acquisition channels remain 100% blocked; reposcan pipeline still REGRESSED (scanning gladiaio/ not whale/)
  - See `triage/run-2026-08-11-06-41.md` for full analysis

- 1 lead(s) marked VALID at 2026-08-11 06:46:21 UTC
  - | VALID | 0 | — |

- 1 lead(s) marked VALID at 2026-08-11 09:25:18 UTC
  - | **VALID** | 0 | — |

- 7 lead(s) marked VALID at 2026-08-11 10:23:22 UTC
  - - **VERDICT: HOLD** — passes Q1, Q2, Q3, Q6; blocked purely on Q4 (evidential). Whale-only OSCrypt fork (`xv10` magic, `os_crypt_whale.cc`, custom prefs keys `sync.encryption_bootstrap_token_per_accou
  - - **VERDICT: HOLD** — passes Q1, Q2, Q3, Q6. Android sync encryption added only 2025-04 (late, possibly custom mobile impl). **Elevates to VALID upon:** APK delivery → decompilation showing `Encrypted
  - - **VERDICT: HOLD** — strongest sync-surface lead. Whale-only OSCrypt deviation is confirmed real and historically where weak crypto hides. **Elevates to VALID upon:** binary delivery showing `sync.en
  - - **VERDICT: HOLD** — passes Q2, Q3, Q6. Blocked on Q1 (scope ambiguous) and Q4 (AUTH_HELPED). **Elevates to VALID with:** scope confirmation + interactive test demonstrating joiner observes host's ot
  - - **VERDICT: HOLD** — passes Q1, Q2, Q3, Q6. Blocker is Q4 (AUTH_HELPED). Binary confirms server-fetched exclusion list `whale.tweak.multiplay_login_pages` (OAuth/SSO consent pages likely unlisted). *
  - - **VERDICT: HOLD** — passes Q1, Q2, Q3, Q6. Blocked purely on Q4 (evidential). Whale-only prefs deviation confirmed real. **Elevates to VALID upon:** binary delivery showing `sync.encryption_bootstra
  - | **VALID** | **0** | — |

- 5 lead(s) triaged at 2026-08-11 13:47 UTC — **0 VALID / 2 HOLD / 3 INVALID**
  - Full 7Q analysis per lead; Q4 (passive proof) is the universal blocker for all 5 leads
  - 5 models de-duplicated to 5 distinct lead categories
  - 2 HOLD: Lead 01 (desktop sync KDF, binary required), Lead 02 (Android sync KDF, APK required)
  - 3 INVALID: Lead 03 (sidebar SOP bypass — duplicate of 6 fixed CVEs, Q5/Q7), Lead 04 (socket.io — below threshold, Q7), Lead 05 (NVD gap — Q6 always-rejected, not a vuln)
  - All binary-acquisition channels remain 100% blocked; reposcan pipeline REGRESSED (0 code/config files scanned)
  - See `triage/run-2026-08-11-13-47.md` for full analysis

- 2 lead(s) marked VALID at 2026-08-11 13:53:01 UTC
  - - - **VERDICT: HOLD** — passes Q2, Q3, Q6. Blocked on Q1 (scope ambiguous) and Q4 (AUTH_HELPED). **Elevates to VALID with:** scope confirmation + interactive test demonstrating joiner observes host's 
  - - - **VERDICT: HOLD** — passes Q1, Q2, Q3, Q6. Blocked purely on Q4 (evidential). Whale-only prefs deviation confirmed real. **Elevates to VALID upon:** binary delivery showing `sync.encryption_bootst
