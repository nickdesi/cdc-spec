# CDC Research and Evaluation Plan

## Objective

Evaluate whether Cognitive Delta Compression (CDC) can improve longitudinal LLM memory on three practical dimensions:

1. **Token efficiency** — fewer injected tokens than baseline memory.
2. **Cross-session coherence** — better continuity across evolving user positions.
3. **Error visibility** — contradictions and uncertain inferences are surfaced instead of hidden.

CDC should be considered successful only if it improves continuity without concealing uncertainty.

---

## Evaluation sequence

### Phase 0 — Invariant tests

Before model-quality claims, the implementation must prove basic safety invariants:

- `ANCHORED` deltas require an anchor hash.
- `INFERRED` deltas cannot carry anchor hashes.
- `INFERRED` confidence never exceeds `0.4`.
- confidence decay is applied at retrieval time.
- tension flags are preserved in injected context.

### Phase 1 — Synthetic benchmark

Run 10–20 synthetic multi-session conversations before any public communication.

Each synthetic case should include:

- 3–6 sessions;
- at least one stable preference or belief;
- at least one changed preference or belief;
- at least one contradiction or correction;
- expected deltas and expected tensions.

Compare:

| Variant | Description |
|---|---|
| Baseline summary | inject compact session summaries or raw memory snippets |
| CDC | inject selected deltas under a fixed token budget |

Minimum metrics:

| Metric | Definition |
|---|---|
| Token savings | `1 - cdc_tokens / baseline_tokens` |
| Delta recall | expected deltas retrieved / expected deltas |
| Coherence pass rate | synthetic probe answered with correct evolved state |
| Tension detection | expected contradictions flagged / expected contradictions |
| Inference safety | inferred deltas above confidence cap count |

The benchmark report must include failure cases, not only aggregate scores.

### Phase 2 — Expert review

Before human-user studies, ask reviewers to inspect:

- schema design;
- benchmark cases;
- ethics constraints;
- claims made in public documentation.

### Phase 3 — Human validation study

Only after the synthetic benchmark is reproducible.

Target design:

- N = 100 users;
- 30 sessions minimum per user;
- domain mix: technical, knowledge work, creative;
- opt-in participation with explicit consent;
- control group: standard RAG/summary memory;
- test group: CDC delta injection.

Session probes:

1. recall a previously established user position;
2. handle a changed or corrected position;
3. synthesize across sessions without ignoring uncertainty.

Metrics:

| Metric | Measurement |
|---|---|
| Response quality | blind human rating + task-specific checks |
| Token cost | actual injected context tokens |
| Coherence | contradiction rate across probes |
| Trajectory capture | match against human-annotated transitions |
| User trust | survey on inspectability/control |

---

## Economic benchmark

For every evaluated session, log:

- `tokens_injected_cdc`;
- `tokens_injected_baseline`;
- `deltas_retrieved`;
- `tensions_flagged`;
- `verification_issues`;
- optional async consolidation cost.

Break-even condition:

```text
tokens_injected_cdc + consolidation_cost < tokens_injected_baseline
```

For the MVP benchmark, consolidation cost may be reported as `0` if fossilization is not enabled yet.

---

## Fossilization calibration

The fossilization threshold ε remains an open parameter.

Future calibration should test values such as:

```text
ε ∈ {0.01, 0.05, 0.10, 0.20, 0.50}
```

Per domain, measure:

- fossilization rate;
- retrieval accuracy after fossilization;
- information loss on probe questions;
- stale-memory rate.

No production default should be claimed until this is tested.

---

## Reporting standard

Every public benchmark should report:

- dataset size and generation method;
- baseline definition;
- token counting method;
- exact CDC configuration;
- aggregate metrics;
- representative wins;
- representative failures;
- limitations.

Claims must be phrased as benchmark-specific results, not general proof.

---

## Collaboration

Open an Issue tagged `[research-collab]` for protocol critique, dataset design, privacy review, or independent replication.
