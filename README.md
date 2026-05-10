# Cognitive Delta Compression (CDC)

> **Don't store facts. Store transitions.**

CDC is a novel memory architecture for LLM-based systems that stores *how understanding evolves* rather than *what was said*.

---

## The core insight

Every existing LLM memory system (RAG, mem0, MemGPT, Zep) stores the same unit of information: **a fact**, extracted from a conversation and indexed for later retrieval.

CDC stores a fundamentally different unit: a **cognitive delta** — the directional vector between two states of understanding.

```
Traditional memory :  (entity, attribute, value, timestamp)
CDC               :  Δ(M_n → M_n+1) + anchor + confidence + decay
```

This distinction matters because:
- A fact can become stale silently
- A delta carries its own temporal direction and confidence decay
- A chain of deltas captures *trajectory*, not just state
- Trajectory enables anticipation — not just recall

---

## Architecture overview

```
SESSION
  │
  ▼
┌─────────┐    ┌─────────┐    ┌───────────┐
│  ENCODE │───▶│  INDEX  │───▶│ FOSSILIZE │  (async, out-of-session)
└─────────┘    └─────────┘    └───────────┘
                                     │
                                     ▼
                              ┌──────────┐    ┌────────┐
                              │ RETRIEVE │───▶│ VERIFY │──▶ LLM context
                              └──────────┘    └────────┘
                                                   │
                                                   └──▶ feedback → ENCODE
```

### Layer 1 — ENCODE
Transforms session transcripts into raw CDC_UNITs. Classifies each delta as `ANCHORED` (externally verifiable source) or `INFERRED` (emergent from conversation). Applies contrastive embeddings over standardized probe questions.

### Layer 2 — INDEX
Inserts CDC_UNITs into a directed acyclic graph (DAG) of cognitive trajectory. Runs tension detection (contradiction flagging), confidence decay scheduling, and domain partitioning.

### Layer 3 — FOSSILIZE *(async)*
Background consolidation process — the computational equivalent of sleep-based memory consolidation. Collapses stable delta chains into Fossil Nodes when variance across probe questions drops below domain-specific threshold ε. Original chains are archived, never deleted.

### Layer 4 — RETRIEVE
Hybrid retrieval combining BM25 (metadata), cosine similarity (contrastive space), and graph traversal (trajectory proximity). Token budget allocator selects optimal delta subset for injection. Priority: `Fossil Nodes > ANCHORED > INFERRED`.

### Layer 5 — VERIFY
Closes the loop. Validates anchor hashes against live sources, runs consistency micro-probes on LLM output, feeds results back to ENCODE as confidence updates.

---

## The CDC_UNIT schema

```python
@dataclass
class CDCUnit:
    id: UUID
    type: Literal["ANCHORED", "INFERRED"]
    delta_vector: list[float]      # 768-dim, contrastive space
    anchor_hash: str | None        # sha256(source) — None if INFERRED
    confidence: float              # ANCHORED: max 1.0 / INFERRED: max 0.4
    decay_rate: float              # ANCHORED: 0.001/day / INFERRED: 0.01/day
    tension_flag: bool             # contradiction with existing delta detected
    inject_cost: int               # estimated tokens if injected
    domain: str                    # auto-assigned semantic cluster
    session_ref: UUID
    archived: bool                 # True if fossilized (never deleted)
    created_at: datetime
```

---

## Key properties

| Property | CDC | RAG | mem0 | MemGPT |
|---|---|---|---|---|
| Unit of storage | Transition | Chunk | Fact | Message |
| Captures trajectory | ✅ | ❌ | ❌ | ❌ |
| Confidence decay | ✅ | ❌ | Partial | ❌ |
| Contradiction detection | ✅ (at write) | ❌ | ✅ (at read) | ❌ |
| Token cost scales with | Δ (delta count) | M (memory size) | M | M |
| Verifiable grounding | Per unit | Per chunk | Per fact | ❌ |
| Async consolidation | ✅ | ❌ | ❌ | ❌ |

---

## What CDC is NOT

- ❌ Not a vector database
- ❌ Not a graph memory (stores transitions, not entities)
- ❌ Not a compression algorithm for prompts
- ❌ Not a replacement for RAG on document retrieval tasks

CDC is specifically designed for **longitudinal user-LLM interactions** where the evolution of understanding over time is as important as the content of any single session.

---

## Open research questions

Before any production deployment, these remain open:

1. **ε calibration** — The fossilization variance threshold must be empirically calibrated per domain
2. **Economic break-even** — Token savings vs consolidation cost break-even point not yet benchmarked
3. **Cross-model stability** — Strategy for delta migration when embedding model changes
4. **Validation protocol** — 100 users / 30 sessions / RAG control group needed

See [`RESEARCH.md`](./RESEARCH.md) for the full validation protocol.

---

## Ethical commitments

CDC models cognitive trajectories — a capability with significant privacy implications. The following are non-negotiable constraints, not optional features:

- **Full transparency** — users can inspect every stored CDC_UNIT
- **Free correction** — any delta can be annotated, corrected, or deleted
- **Right to erasure** — deletion triggers refossilization of dependent chains
- **Strict isolation** — deltas are never shared, sold, or used cross-user
- **Explicit consent** — predictive capabilities are declared at onboarding
- **Audit log** — every operation is logged with timestamp and reason

See [`ETHICS.md`](./ETHICS.md) for the complete framework.

---

## Status

```
[████████░░] Specification        v0.1 — complete
[████░░░░░░] Core implementation  in progress (private repo)
[░░░░░░░░░░] Validation protocol  not started
[░░░░░░░░░░] Paper                not started
```

---

## Contributing

This repository contains the public specification only. Discussion, critique, and formal review are welcome via Issues.

If you are a researcher interested in the validation protocol, open an Issue tagged `[research-collab]`.

---

## License

Specification: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)  
Implementation: proprietary (private repo)
