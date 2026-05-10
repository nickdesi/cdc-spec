# CDC Ethical Framework

CDC models cognitive trajectories. This is more sensitive than ordinary chat history because it can encode how a person's preferences, beliefs, doubts, and decisions evolve over time.

Ethics are therefore part of the architecture, not an optional product layer.

---

## Core principles

### 1. Transparency

Every stored CDC unit must be inspectable by the user.

Inspection should show at minimum:

- delta ID;
- type: `ANCHORED` or `INFERRED`;
- domain;
- confidence;
- decay state;
- source reference or anchor hash when applicable;
- tension flags;
- creation date;
- whether the delta was fossilized or archived.

No hidden memory state.

### 2. Inference labeling

`INFERRED` deltas are hypotheses, not facts.

They must be visibly labeled as uncertain wherever they influence retrieval or output. They must never carry external-source authority and their confidence must remain capped.

### 3. User control

Users must be able to:

- **inspect** stored deltas;
- **annotate** deltas with clarifications;
- **correct** mistaken deltas;
- **delete** individual deltas;
- **export** their memory state;
- **disable** CDC for future sessions.

Correction and deletion events should themselves be auditable.

### 4. Right to erasure

Full deletion must remove:

- CDC units;
- fossil nodes;
- session references;
- derived indexes;
- verification and audit records, except where legally required otherwise.

The target purge window is 72 hours or less.

### 5. Strict data isolation

- Deltas are scoped to one user.
- Cross-user memory sharing is prohibited by default.
- CDC data must not be used for model training without explicit, separate, revocable consent.
- CDC data must not be sold, shared with third parties, or used for advertising.

### 6. Predictive capability disclosure

CDC may support predictions about future preferences or decisions by analyzing trajectory.

Any predictive feature requires:

- explicit product labeling;
- separate opt-in;
- clear explanation of uncertainty;
- user-visible evidence trail;
- easy opt-out.

### 7. Tension visibility

Contradictions must not be silently resolved.

When the system detects tension between deltas, the tension should remain visible until resolved by a clear policy or user correction.

---

## Deployment red lines

CDC should not be deployed for:

- covert profiling;
- behavioral advertising;
- emotion or vulnerability targeting;
- employment, insurance, credit, policing, or eligibility decisions;
- clinical use without domain-specific safety review;
- prediction features without explicit opt-in.

---

## Consent flow

At first activation, users must explicitly acknowledge:

1. CDC stores transitions in their understanding, not just facts.
2. These transitions build a longitudinal trajectory model.
3. Some deltas may be inferred and uncertain.
4. All memory should be inspectable, correctable, exportable, and deletable.
5. Predictive features require separate opt-in.

No dark patterns. No pre-checked boxes.

---

## Public claim discipline

Until independent validation exists, CDC should be described as:

- a research-stage memory architecture;
- a benchmarked implementation hypothesis once benchmarked;
- not a validated cognitive or psychological model.

Public communication must include limitations and failure cases.
