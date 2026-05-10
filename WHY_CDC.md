# Why CDC?

CDC — Cognitive Delta Compression — is a memory architecture hypothesis for LLM applications.

Its goal is not to store more facts. Its goal is to preserve how a user's position changes over time.

## The problem

Most LLM memory systems flatten continuity into one of three forms:

1. **Facts** — "the user prefers X".
2. **Summaries** — "the user discussed X and Y".
3. **Retrieved chunks** — pieces of prior text that happen to match the query.

These are useful, but fragile in long-running interactions.

They often miss:

- when a preference changed;
- why a correction happened;
- whether a statement is grounded or inferred;
- whether two remembered items are in tension;
- whether the system should treat an old preference as stale.

A user trajectory is not just a facts table. It is a sequence of revisions, constraints, corrections, and shifts.

## The CDC proposal

CDC stores **cognitive deltas**:

```text
previous understanding → updated understanding
+ confidence
+ optional source anchor
+ time decay
+ possible tension
```

Instead of only storing:

```text
The user wants detailed API docs.
```

CDC tries to preserve:

```text
The user initially wanted detailed API docs, then corrected the scope toward a minimal MVP API to avoid overengineering.
```

That difference matters when an assistant must remain coherent across days, weeks, or many sessions.

## Why it may help

CDC is designed to make memory more explicit about:

- **continuity** — what stayed stable;
- **change** — what shifted;
- **uncertainty** — what is inferred, not known;
- **tension** — what may contradict earlier context;
- **retrieval discipline** — what should be injected now, not everything remembered.

This is especially relevant for:

- long-term assistants;
- technical collaboration;
- coaching and education;
- research workflows;
- product discovery;
- any setting where corrections must override stale memory.

## What CDC is not

CDC is not a replacement for RAG. RAG retrieves source material. CDC models a user or project trajectory.

CDC is also not a claim that cognition is fully modelable. It is a practical representation for memory-sensitive LLM systems.

## Current limits

CDC is still early.

The current public result is an MVP benchmark on synthetic conversations. It validates deterministic checks only:

- expected continuity signals are preserved;
- expected tensions are detected;
- core invariants hold.

It does not yet prove:

- better final LLM answers;
- better production behavior;
- human-perceived usefulness;
- privacy safety in real deployments.

## Next step

The next evaluation should use 5–10 anonymized semi-real conversations and compare:

- naive summary memory;
- compact lexical retrieval;
- CDC retrieval.

Judgment should focus on:

- coherence;
- respect for corrections;
- non-overclaiming;
- useful handling of uncertainty and tension.

CDC should be communicated honestly as an early specification and MVP benchmark, not as a proven memory breakthrough.
