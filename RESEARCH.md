# CDC Validation Protocol

## Objective

Empirically demonstrate that CDC improves upon RAG-standard memory systems on the following dimensions:
- Response quality at equivalent or lower token cost
- Cross-session coherence
- Contradiction rate
- User-perceived understanding

---

## Protocol design

### Participants
- N = 100 users, minimum 30 sessions each
- Domain mix: 40% technical (dev, research), 40% knowledge work, 20% creative
- Recruited via opt-in, full informed consent required

### Groups
- **Control** — Standard RAG memory (full session summaries injected)
- **Test** — CDC pipeline (delta injection, budget 500 tokens)

### Session structure
Each session includes 3 standardized probe questions at start and end:
1. Domain-specific factual recall
2. Position/opinion on previously discussed topic
3. Novel synthesis question requiring cross-session reasoning

### Metrics

| Metric | Measurement | Tool |
|---|---|---|
| Response quality | Human eval (1-5 Likert) + automated (ROUGE-L, BERTScore) | Blind raters |
| Token cost | Actual tokens injected per session | API logging |
| Coherence | Contradiction rate between sessions | Automated probe comparison |
| Trajectory capture | Delta accuracy vs human-annotated ground truth | Human annotation |
| User satisfaction | Exit survey, NPS | Self-reported |

### Economic benchmark
For each participant session, log:
- `tokens_injected_cdc` — actual tokens used by CDC context
- `tokens_injected_rag` — equivalent RAG context tokens
- `consolidation_cost` — tokens spent by async consolidation worker

Break-even condition: `tokens_injected_cdc + consolidation_cost < tokens_injected_rag`

---

## ε calibration sub-study

Parallel to the main study: 20 participants across 4 domains (tech, philosophy, creative, factual).

Test 5 values of ε ∈ {0.01, 0.05, 0.1, 0.2, 0.5} per domain.

Measure: fossilization rate, information loss (probe variance post-fossil), retrieval accuracy.

Output: domain-specific ε lookup table for production config.

---

## Timeline estimate

| Phase | Duration |
|---|---|
| Instrument setup | 3 weeks |
| Participant recruitment | 2 weeks |
| Data collection | 8 weeks |
| Analysis | 3 weeks |
| Paper draft | 4 weeks |
| **Total** | **~5 months** |

---

## Seeking collaboration

If you are a researcher interested in co-designing or co-running this protocol, open an Issue tagged `[research-collab]`.
