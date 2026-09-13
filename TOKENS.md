# Token usage tracking — Patrimony

LLM token usage for this project, tallied session by session.

## Cumulative tally (2026-09-13)

| Metric | deepseek-v4-flash | gemini-3.6-flash (vision) | **Total** |
|---|---|---|---|
| Dev sessions (Hermes, interactive) | 8 | (same sessions) | **8** |
| Scripted agent sessions (API / docs) | 9 | 0 | **9** |
| Messages (exchanges) | — | — | **2 265** |
| API calls | 5 510 | 51 | **5 573** |
| Input tokens | 10 282 161 | 60 001 | **10 348 379** |
| Output tokens | 5 068 939 | 62 650 | **5 133 699** |
| **Subtotal (input + output)** | **15 351 100** | **122 651** | **15 482 078** |
| Cache read (reused at reduced price) | 1 136 215 040 | 0 | **1 136 215 040** |
| **Estimated cost** | **≈ 5.90 USD** | **≈ 0.50 USD** | **≈ 6.40 USD** |

(A dozen small calls on `deepseek-flash` (≈ 0.00 USD) and one free
`nvidia/nemotron` call (1 540 tokens) are included in the totals; the 9
scripted sessions (fiscal sheets, error-string translations) billed
< 0.01 USD.)

## How to re-read the counter

The Hermes session database (SQLite) holds the exact counters:

```sql
SELECT SUM(api_call_count), SUM(input_tokens), SUM(output_tokens),
       SUM(cache_read_tokens), SUM(estimated_cost_usd)
FROM session_model_usage
WHERE session_id IN (
  -- interactive dev sessions
  '20260904_172726_2a994788',  -- Patrimony bootstrap, first version (09-04)
  '20260905_103925_6fca4ce6',  -- LostInTheBugs/Patrimony review (09-05)
  '20260906_100900_f2e081d3',  -- dev + demo auto-reset (09-06)
  '20260907_065839_a7915f43',  -- build session (09-07)
  '20260908_101839_0f625177',  -- crowdfunding module integration (09-08)
  '20260909_071315_26cb4392',  -- dedicated pages / charts / simulators (09-09)
  '20260911_064339_75eafe4f',  -- custom tax rules (09-11)
  '20260912_104027_cb3b83ff',  -- Windows Defender false positive + desktop follow-up (09-12)
  -- scripted sessions (API): fiscal sheets FR/LU 2026 + consolidation
  '20260906_181909_1361c2','20260906_181909_49603a','20260906_181909_52e83d',
  '20260906_181909_87a87e','20260906_181909_9d48d5','20260906_182547_cd4e8a',
  -- scripted sessions (API): server error strings → EN/LU/DE (09-06)
  '20260906_222659_2bd8f3','20260906_222659_cf2a3d','20260906_222659_d5cbdf'
);
```

After each dev session, copy the matching row into the table above.

## Notes

- Tally taken from `~/.hermes/state.db` (table `session_model_usage`,
  filtered by session id) — real runtime counters, not an estimate.
- Covers every session attributed to the project since its first version
  (2026-09-04 bootstrap → 2026-09-12). Sessions run over Discord chat
  (no `cwd`) were attributed by content (first user message + dominant
  mentions); a session can carry minor unrelated exchanges, so usage is
  slightly over-attributed. Cross-project sessions (cloudfr.net portal,
  multi-app branding sweeps, token-accounting) are not attributed to any
  single app.
- The 9 scripted sessions wrote the 2026 fiscal sheets (kept under
  `work/patrimony`, never pushed) and translated the server error strings
  of this app.
- The 2026-09-13 session (macOS desktop port) is not yet flushed to the
  database: its tours will land on the next tally.
- `reasoning_tokens` is probably included in `output_tokens`
  (to be confirmed with the provider).
