# CDC Synthetic Benchmark — MVP

This is the first public benchmark snapshot for Cognitive Delta Compression (CDC). It is intentionally small and deterministic: it validates the current MVP pipeline, not general LLM answer quality.

## Scope

The benchmark covers:

- encoding session transcripts into CDC deltas;
- indexing deltas in a user-scoped trajectory store;
- retrieving compact context under a token budget;
- flagging expected tensions;
- verifying CDC invariants.

It does **not** cover:

- human validation;
- LLM-as-judge evaluation;
- production RAG comparisons;
- fossilization quality;
- privacy or security audits.

## Dataset

- 12 synthetic multi-session conversations.
- Topics include API preference shifts, benchmark reporting style, privacy red lines, implementation scoping, token budgets, and retrieval priority.
- Each case defines expected retrieval terms and whether tension should be detected.

## Results

| Metric | Result |
|---|---:|
| Cases | 12 |
| Average token savings vs compact snippet baseline | 9.9% |
| Coherence pass rate | 100.0% |
| Expected tension detection | 100.0% |
| Verification issues | 0 |

## Interpretation

This result is a sanity check, not a scientific claim. It shows that the first MVP can preserve expected continuity signals while injecting a smaller context than the compact baseline used in the test.

The token savings are modest because the synthetic conversations are already short. Larger savings should only be claimed after testing longer histories and stronger baselines.

## Known limitations

- Synthetic conversations are small and hand-authored.
- Baseline uses compact snippets, not a production RAG or memory system.
- Token counts are approximate word-based estimates.
- No human evaluation is included yet.
- No claim is made that CDC improves final model answers in real deployments.

## Next benchmark step

The next evaluation should use 20+ longer conversations with:

- 3–6 sessions per user;
- stable preferences;
- changed preferences;
- contradictions and corrections;
- distractor facts;
- direct comparison against summary memory and retrieval baselines.
