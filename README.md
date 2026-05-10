# Cognitive Delta Compression (CDC)

> **Don't store facts. Store transitions.**

Cognitive Delta Compression (CDC) is a research-stage memory architecture for LLM applications. It stores **how a user's understanding changes over time** instead of storing only isolated facts, chunks, or summaries.

CDC is intended for longitudinal interactions where continuity matters: coaching, research assistance, education, product discovery, therapy-adjacent journaling, and technical collaboration.

---

## Why CDC exists

Most LLM memory systems store some variant of:

```text
(entity, attribute, value, timestamp)
```

That works for stable facts, but it loses the trajectory of reasoning:

- a preference that became more precise;
- a belief that weakened after new evidence;
- a technical decision that changed because constraints changed;
- a contradiction that should stay visible instead of being overwritten.

CDC stores a different unit:

```text
Δ(M_n → M_n+1) + anchor + confidence + decay + tension state
```

A **cognitive delta** is not “what the user said”. It is the directional transition between two states of understanding.

---

## Current status

CDC is **not production-ready** and should not be presented as validated science yet.

| Workstream | Status |
|---|---|
| Public specification | v0.1, active refinement |
| Core implementation | private MVP running |
| Synthetic benchmark | first deterministic MVP snapshot published |
| Human validation protocol | designed, not executed |
| Paper | not started |

The immediate goal is modest: expand the runnable MVP benchmark before broader communication.

See [`BENCHMARK.md`](BENCHMARK.md) for the first synthetic benchmark snapshot.

---

## Architecture

```text
SESSION
  │
  ▼
ENCODE ──▶ INDEX ──▶ FOSSILIZE ──▶ RETRIEVE ──▶ VERIFY ──▶ LLM context
  ▲                                      │
  └────────────── feedback ◀─────────────┘
```

### 1. ENCODE
Extracts candidate transitions from a session transcript and classifies them as:

- `ANCHORED`: externally verifiable or explicitly grounded;
- `INFERRED`: model-inferred from conversation context, never treated as a fact.

### 2. INDEX
Stores deltas in a trajectory graph and flags tensions when a new delta conflicts with prior trajectory.

### 3. FOSSILIZE
Asynchronously compresses stable delta chains into fossil nodes. Original deltas are archived, not silently deleted.

### 4. RETRIEVE
Selects the smallest useful set of deltas under a token budget. Priority order:

```text
Fossil nodes > ANCHORED deltas > INFERRED deltas
```

### 5. VERIFY
Checks grounding, marks stale sources, and feeds consistency failures back into future encoding.

---

## Minimal CDC unit

```python
@dataclass
class CDCUnit:
    id: UUID
    type: Literal["ANCHORED", "INFERRED"]
    delta_vector: list[float]
    anchor_hash: str | None
    confidence: float
    decay_rate: float
    tension_flag: bool
    inject_cost: int
    domain: str
    session_ref: UUID
    archived: bool
    created_at: datetime
```

Key invariants:

- `ANCHORED` deltas require an `anchor_hash`.
- `INFERRED` deltas must not have an `anchor_hash`.
- `INFERRED` confidence is capped at `0.4`.
- Confidence decays over time.
- Contradictions are flagged, not silently resolved.

---

## Difference from adjacent systems

| System | Primary stored unit | CDC distinction |
|---|---|---|
| RAG | document chunk | CDC stores change in understanding, not source text |
| mem0-style memory | extracted fact/preference | CDC stores direction, confidence, decay, and tension |
| MemGPT/Letta | message or memory page | CDC optimizes for trajectory injection under budget |
| Knowledge graph | entity/relation | CDC stores transitions between cognitive states |

CDC is complementary to RAG. It is not meant to replace document retrieval.

---

## What CDC is not

- Not a general vector database.
- Not a proof that cognition can be fully modeled.
- Not a prompt-compression trick.
- Not a validated clinical or psychological tool.
- Not a replacement for source-grounded retrieval.

CDC is a memory design hypothesis that must be benchmarked and challenged.

---

## Evaluation plan

The first public benchmark should test CDC on 10–20 synthetic multi-session conversations before any broad communication.

Minimum metrics:

- injected token count vs baseline memory;
- cross-session coherence;
- contradiction/tension detection;
- inferred-delta confidence compliance;
- human-readable failure cases.

See [`RESEARCH.md`](./RESEARCH.md) for the validation protocol.

---

## Ethics and privacy

CDC can model sensitive longitudinal patterns. The ethical baseline is therefore stricter than ordinary chat memory:

- every delta must be inspectable;
- inferred deltas must be visibly labeled as uncertain;
- users must be able to correct or delete deltas;
- predictive outputs require separate opt-in;
- cross-user sharing, advertising, and hidden profiling are out of scope.

See [`ETHICS.md`](./ETHICS.md) for the full framework.

---

## Repository structure

This repository contains the public specification only. The MVP implementation is currently private until the benchmark is reproducible and safe to publish.

Suggested reading order:

1. [`README.md`](./README.md) — concept and architecture;
2. [`RESEARCH.md`](./RESEARCH.md) — evaluation strategy;
3. [`ETHICS.md`](./ETHICS.md) — constraints and user rights.

---

## Contributing

Open an Issue for:

- conceptual critiques;
- benchmark suggestions;
- privacy or misuse concerns;
- related work;
- research collaboration.

Please distinguish between validated results and hypotheses.

---

## License

Specification: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)  
Implementation: private/proprietary during MVP stage.
