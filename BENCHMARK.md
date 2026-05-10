# CDC Synthetic Benchmark — MVP

This is the latest public benchmark snapshot for Cognitive Delta Compression (CDC), rerun on 2026-05-10. It remains deterministic: it validates the current MVP pipeline, not general LLM answer quality.

## Scope

The benchmark covers:

- encoding session transcripts into CDC deltas;
- indexing deltas in a user-scoped trajectory store;
- retrieving relevant trajectory context under a fixed budget;
- comparing expected-term preservation against deterministic summary-memory and compact lexical-retrieval baselines;
- flagging expected tensions;
- verifying CDC invariants.

It does **not** cover:

- token savings as a success metric;
- human validation;
- LLM-as-judge evaluation;
- production RAG comparisons;
- fossilization quality;
- privacy or security audits.

## Dataset

- 30 synthetic multi-session conversations.
- Topics include API preference shifts, benchmark reporting style, privacy red lines, implementation scoping, retrieval priority, architecture decisions, public-claim discipline, user control, communication timing, and everyday-life stress cases with distractors.
- Each case defines expected retrieval terms and whether tension should be detected.

## Results

| Metric | Result |
|---|---:|
| Cases | 30 |
| CDC coherence pass rate | 100.0% |
| Naive summary coherence pass rate | 70.0% |
| Compact retrieval coherence pass rate | 63.3% |
| Expected tension detection | 100.0% |
| Verification issues | 0 |
| Quality score, excluding token savings | 10.0 / 10 |

## Verdict

The benchmark is **conclusive for the tested MVP engineering checks**, but **not conclusive as a scientific or product-level proof**.

CDC preserves all expected continuity signals in this deterministic dataset, detects all expected tensions, and passes all invariant checks. The stronger result comes from a harder 30-case dataset, including longer everyday-life conversations with distractor context — not from counting token savings.

A fair engineering score for the deterministic MVP pipeline is **10.0 / 10** on the tested criteria:

- coherence signal preserved: 100%;
- expected tension detection: 100%;
- invariant violations: 0.

No product/scientific confidence score is assigned yet, because the evaluation still lacks human review, LLM-as-judge answer quality checks, production baselines, and real user transcripts.

## Interpretation

This result is a strong sanity check, not a scientific claim. It shows that the current MVP can preserve expected continuity signals and tension flags across a broader synthetic dataset.

Token economics are intentionally excluded from the score. CDC is evaluated here as a continuity and reasoning-memory mechanism: preserving what changed, what is uncertain, and where tension exists.

## Known limitations

- Synthetic conversations are still hand-authored.
- Baselines are deterministic approximations, not production RAG or memory systems.
- No human evaluation is included yet.
- No claim is made that CDC improves final model answers in real deployments.
- Token savings are not used as a success metric in this benchmark.

## Next benchmark step

The next evaluation should use:

- real or semi-real conversation transcripts;
- LLM-as-judge and/or human review for answer quality;
- adversarial corrections and contradictions;
- production-grade retrieval baselines;
- privacy and deletion behavior checks.
