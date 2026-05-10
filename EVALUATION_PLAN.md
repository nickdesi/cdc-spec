# Mini-Evaluation Plan

This document defines the next credibility step after the synthetic MVP benchmark.

## Goal

Evaluate whether CDC preserves useful continuity better than simple memory baselines on semi-real conversations.

This is not a scientific proof. It is a small validation step before public communication.

## Dataset

Use **5–10 anonymized semi-real conversations**.

Each conversation should contain 3–6 sessions and include at least one of:

- a stable preference;
- a corrected preference;
- a constraint introduced after an earlier request;
- a contradiction or tension;
- irrelevant distractor context;
- an uncertainty that must not be treated as fact.

Do not include secrets, personal identifiers, health details, financial identifiers, or sensitive third-party data.

## Systems compared

Compare three memory modes:

1. **Naive summary memory**
   - short summary of prior sessions;
   - no explicit uncertainty or tension model.

2. **Compact lexical retrieval**
   - retrieves the most query-overlapping prior snippets;
   - no explicit trajectory model.

3. **CDC retrieval**
   - retrieves cognitive deltas;
   - includes confidence, source/inference labels, and tension flags.

## Evaluation questions

For each test query, judge whether the system:

- preserves the user's current preference;
- respects corrections over stale memory;
- avoids treating inference as fact;
- surfaces relevant tension when needed;
- avoids injecting irrelevant old context;
- remains concise and useful.

## Scoring rubric

Score each answer from 0 to 2 per criterion.

| Criterion | 0 | 1 | 2 |
|---|---|---|---|
| Coherence | misses key continuity | partially coherent | preserves the trajectory |
| Corrections | uses stale memory | mentions both but unclear | correctly prioritizes correction |
| Uncertainty | overclaims | partially labels uncertainty | clearly labels uncertainty |
| Tension handling | misses contradiction | vague tension | explicit useful tension |
| Relevance | noisy context | some noise | focused context |
| Safety/privacy | exposes too much | minor concern | minimal and appropriate |

Maximum: 12 points per evaluated answer.

## Review method

Use two reviewer modes:

1. **Human review** — manual judgment with the rubric above.
2. **LLM-as-judge** — optional secondary signal, never the only evidence.

Disagreements should be recorded rather than hidden.

## Success threshold

For this mini-evaluation, CDC is promising if it shows:

- higher correction-respect score than both baselines;
- equal or better coherence than both baselines;
- fewer overclaims than both baselines;
- no privacy or invariant violations.

## Publication rule

Only communicate externally after documenting:

- dataset size;
- anonymization method;
- scoring rubric;
- aggregate results;
- failure cases;
- limitations.

Suggested public framing:

> Early CDC spec + MVP benchmark. Promising on continuity and correction handling, still not scientifically proven.
