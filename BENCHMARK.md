# CDC Synthetic Benchmark — MVP

This is the latest public benchmark snapshot for Cognitive Delta Compression (CDC), rerun on 2026-05-10. It remains deterministic: it validates the current MVP pipeline, not general LLM answer quality.

## Scope

The benchmark covers:

- encoding session transcripts into CDC deltas;
- indexing deltas in a user-scoped trajectory store;
- retrieving compact context under a token budget;
- comparing against deterministic raw-history, summary-memory, and compact lexical-retrieval baselines;
- flagging expected tensions;
- verifying CDC invariants.

It does **not** cover:

- human validation;
- LLM-as-judge evaluation;
- production RAG comparisons;
- fossilization quality;
- privacy or security audits.

## Dataset

- 20 synthetic multi-session conversations.
- Topics include API preference shifts, benchmark reporting style, privacy red lines, implementation scoping, token budgets, retrieval priority, architecture decisions, public-claim discipline, user control, and communication timing.
- Each case defines expected retrieval terms and whether tension should be detected.

## Results

| Metric | Result |
|---|---:|
| Cases | 20 |
| Average CDC token savings vs raw history | 9.6% |
| Average CDC token savings vs naive summary memory | 1.8% |
| Average CDC token savings vs compact lexical retrieval | -8.1% |
| CDC coherence pass rate | 100.0% |
| Naive summary coherence pass rate | 100.0% |
| Compact retrieval coherence pass rate | 80.0% |
| Expected tension detection | 100.0% |
| Verification issues | 0 |
| Overall MVP benchmark score | 6.5 / 10 |

## Verdict

The benchmark is **conclusive as an MVP engineering validation**, but **not yet very conclusive as a scientific or product-level proof**.

CDC currently preserves expected continuity signals, labels uncertainty, detects expected tensions, and passes all deterministic verification checks. However, the token-saving advantage is still modest: it beats raw history and naive summary memory, but it uses more tokens than the compact lexical retrieval baseline.

A fair score is **6.5 / 10**:

- strong for correctness, safety invariants, and continuity preservation;
- medium for compression efficiency;
- weak-to-unknown for real-world answer quality because there is no human or LLM-as-judge evaluation yet.

## Interpretation

This result is a sanity check, not a scientific claim. It shows that the current MVP can preserve expected continuity signals and tension flags across a broader synthetic dataset.

The compact lexical retrieval baseline uses fewer tokens than CDC on average, but loses expected terms in 20% of cases. This is the useful trade-off to study next: CDC is currently optimizing explicit continuity and uncertainty labeling, not pure token minimization.

## Known limitations

- Synthetic conversations are still hand-authored.
- Baselines are deterministic approximations, not production RAG or memory systems.
- Token counts are approximate word-based estimates.
- No human evaluation is included yet.
- No claim is made that CDC improves final model answers in real deployments.

## Next benchmark step

The next evaluation should use longer and less curated conversations with:

- 3–6 sessions per user;
- stable preferences;
- changed preferences;
- contradictions and corrections;
- distractor facts;
- LLM-as-judge and/or human review for answer quality, beyond deterministic expected-term checks.
