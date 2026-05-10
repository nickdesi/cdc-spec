# Cognitive Delta Compression (CDC)

> **FR — Ne stocke pas seulement des faits. Stocke les transitions.**  
> **EN — Don’t only store facts. Store transitions.**

---

# Français

## À quoi sert CDC ?

**CDC — Cognitive Delta Compression** est une architecture de mémoire pour applications LLM. Son objectif est de conserver **l’évolution de la compréhension d’un utilisateur dans le temps**, plutôt que de stocker uniquement des faits isolés, des extraits de documents ou des résumés.

CDC sert aux interactions longues où la continuité est importante :

- assistance de recherche ;
- coaching ou accompagnement long terme ;
- éducation ;
- collaboration technique ;
- découverte produit ;
- journaling ou réflexion personnelle avec fortes contraintes éthiques.

L’idée centrale : un utilisateur ne se résume pas à une liste de préférences. Ses préférences, croyances, objectifs et contraintes **changent**. CDC cherche à représenter ces changements.

---

## Ce que fait CDC

Au lieu de mémoriser seulement :

```text
utilisateur préfère X
```

CDC essaie de mémoriser :

```text
l’utilisateur préférait X, puis a évolué vers Y après telle contrainte ou correction
```

Une unité CDC est un **delta cognitif** :

```text
Δ(état mental n → état mental n+1)
+ ancrage éventuel
+ confiance
+ décroissance dans le temps
+ tension/contradiction éventuelle
```

CDC distingue notamment :

- `ANCHORED` : une information vérifiable ou explicitement sourcée ;
- `INFERRED` : une inférence issue de la conversation, incertaine par définition.

Les inférences ne doivent jamais être présentées comme des faits certains.

---

## Pourquoi ne pas utiliser seulement RAG ou une mémoire classique ?

| Approche | Ce qui est stocké | Limite principale |
|---|---|---|
| RAG | morceaux de documents | retrouve du contexte, mais pas forcément l’évolution de l’utilisateur |
| Mémoire factuelle | préférences/faits extraits | écrase souvent les changements et contradictions |
| Résumé de conversation | synthèse compacte | peut perdre les raisons du changement |
| CDC | transitions, confiance, tensions | encore expérimental, doit être validé |

CDC est complémentaire au RAG. Il ne remplace pas la recherche documentaire.

---

## Architecture

```text
SESSION
  │
  ▼
ENCODE ──▶ INDEX ──▶ FOSSILIZE ──▶ RETRIEVE ──▶ VERIFY ──▶ contexte LLM
  ▲                                      │
  └────────────── feedback ◀────────────┘
```

1. **ENCODE** — extrait des transitions depuis une session.
2. **INDEX** — stocke les deltas dans une trajectoire utilisateur.
3. **FOSSILIZE** — compresse les chaînes stables de deltas.
4. **RETRIEVE** — injecte le plus petit contexte utile sous budget de tokens.
5. **VERIFY** — vérifie les invariants, les sources et les tensions.

---

## Comment l’utiliser aujourd’hui ?

Ce dépôt contient la **spécification publique**. L’implémentation MVP est encore privée pendant la phase de validation.

Lecture recommandée :

1. [`README.md`](./README.md) — concept général ;
2. [`BENCHMARK.md`](./BENCHMARK.md) — résultats synthétiques actuels ;
3. [`RESEARCH.md`](./RESEARCH.md) — protocole d’évaluation ;
4. [`ETHICS.md`](./ETHICS.md) — contraintes éthiques et droits utilisateur.

Dans une application, le flux cible est :

```text
1. Envoyer une conversation/session au système CDC.
2. Extraire des deltas cognitifs.
3. Indexer ces deltas par utilisateur.
4. À la prochaine requête, récupérer seulement les deltas utiles.
5. Injecter ces deltas dans le contexte du LLM avec leurs niveaux de confiance.
```

Exemple conceptuel :

```text
Session 1: l’utilisateur veut une API très détaillée.
Session 2: ils corrige : ils préfèrent maintenant une API minimale pour le MVP.
CDC stocke la transition : “préférence passée de détaillée → minimale pour réduire le scope MVP”.
```

---

## État actuel

CDC est un projet **research-stage**. Il ne doit pas encore être présenté comme une technologie validée scientifiquement.

| Chantier | État |
|---|---|
| Spécification publique | v0.1, en évolution |
| Implémentation core | MVP privé fonctionnel |
| Benchmark synthétique | snapshot déterministe publié |
| Validation humaine | protocole conçu, pas encore exécuté |
| Article/paper | pas commencé |

Voir [`BENCHMARK.md`](BENCHMARK.md) pour les résultats actuels.

---

## Ce que CDC n’est pas

- Pas une base vectorielle générale.
- Pas une preuve que la cognition humaine est entièrement modélisable.
- Pas une astuce de compression de prompt.
- Pas un outil clinique ou psychologique validé.
- Pas un remplacement du RAG sourcé.

CDC est une hypothèse d’architecture mémoire à tester, critiquer et améliorer.

---

## Éthique et confidentialité

CDC peut révéler des trajectoires sensibles : changements de croyance, préférences, contradictions, vulnérabilités. Les exigences minimales sont donc strictes :

- les deltas doivent être inspectables ;
- les inférences doivent être clairement marquées ;
- l’utilisateur doit pouvoir corriger, supprimer, exporter ou désactiver la mémoire ;
- aucun profilage caché ;
- aucun usage publicitaire ou décisionnel sensible sans cadre spécifique.

Voir [`ETHICS.md`](./ETHICS.md).

---

## Contribuer

Ouvrez une Issue pour :

- critiques conceptuelles ;
- propositions de benchmark ;
- risques d’usage ou de confidentialité ;
- travaux liés ;
- collaborations de recherche.

Merci de distinguer clairement les hypothèses des résultats validés.

---

# English

## What is CDC for?

**CDC — Cognitive Delta Compression** is a memory architecture for LLM applications. Its goal is to preserve **how a user’s understanding changes over time**, rather than storing only isolated facts, document chunks, or summaries.

CDC is useful for long-running interactions where continuity matters:

- research assistance;
- coaching or long-term support;
- education;
- technical collaboration;
- product discovery;
- journaling or personal reflection with strict ethical constraints.

The core idea: a user is not just a list of preferences. Preferences, beliefs, goals, and constraints **change**. CDC tries to represent those changes.

---

## What CDC does

Instead of storing only:

```text
user prefers X
```

CDC tries to store:

```text
the user used to prefer X, then shifted toward Y after a constraint or correction
```

A CDC unit is a **cognitive delta**:

```text
Δ(mental state n → mental state n+1)
+ optional anchor
+ confidence
+ time decay
+ possible tension/contradiction
```

CDC distinguishes between:

- `ANCHORED`: verifiable or explicitly grounded information;
- `INFERRED`: an uncertain inference from conversation context.

Inferred deltas must never be presented as certain facts.

---

## Why not only RAG or classic memory?

| Approach | Stored unit | Main limitation |
|---|---|---|
| RAG | document chunks | retrieves context, but not necessarily the user’s trajectory |
| Fact memory | extracted facts/preferences | often overwrites changes and contradictions |
| Conversation summary | compact summary | may lose why the change happened |
| CDC | transitions, confidence, tensions | experimental and still needs validation |

CDC complements RAG. It is not meant to replace source-grounded retrieval.

---

## Architecture

```text
SESSION
  │
  ▼
ENCODE ──▶ INDEX ──▶ FOSSILIZE ──▶ RETRIEVE ──▶ VERIFY ──▶ LLM context
  ▲                                      │
  └────────────── feedback ◀────────────┘
```

1. **ENCODE** — extracts transitions from a session.
2. **INDEX** — stores deltas in a user trajectory.
3. **FOSSILIZE** — compresses stable chains of deltas.
4. **RETRIEVE** — injects the smallest useful context under a token budget.
5. **VERIFY** — checks invariants, sources, and tensions.

---

## How to use it today

This repository contains the **public specification**. The MVP implementation is still private while validation is in progress.

Suggested reading order:

1. [`README.md`](./README.md) — general concept;
2. [`BENCHMARK.md`](./BENCHMARK.md) — current synthetic results;
3. [`RESEARCH.md`](./RESEARCH.md) — evaluation protocol;
4. [`ETHICS.md`](./ETHICS.md) — ethical constraints and user rights.

In an application, the target flow is:

```text
1. Send a conversation/session to CDC.
2. Extract cognitive deltas.
3. Index those deltas per user.
4. On the next request, retrieve only useful deltas.
5. Inject those deltas into the LLM context with confidence labels.
```

Conceptual example:

```text
Session 1: the user wants a very detailed API.
Session 2: they correct this and now prefer a minimal API for the MVP.
CDC stores the transition: “preference shifted from detailed → minimal to reduce MVP scope”.
```

---

## Current status

CDC is a **research-stage** project. It should not yet be presented as scientifically validated technology.

| Workstream | Status |
|---|---|
| Public specification | v0.1, active refinement |
| Core implementation | private MVP running |
| Synthetic benchmark | deterministic snapshot published |
| Human validation | protocol designed, not executed |
| Paper | not started |

See [`BENCHMARK.md`](BENCHMARK.md) for current results.

---

## What CDC is not

- Not a general vector database.
- Not proof that human cognition can be fully modeled.
- Not a prompt-compression trick.
- Not a validated clinical or psychological tool.
- Not a replacement for source-grounded RAG.

CDC is a memory architecture hypothesis to test, challenge, and improve.

---

## Ethics and privacy

CDC can reveal sensitive trajectories: belief changes, preferences, contradictions, vulnerabilities. The minimum requirements are strict:

- deltas must be inspectable;
- inferred deltas must be clearly labeled;
- users must be able to correct, delete, export, or disable memory;
- no hidden profiling;
- no advertising or sensitive decision use without a dedicated framework.

See [`ETHICS.md`](./ETHICS.md).

---

## Contribuer

Ouvrez une Issue pour :

- critiques conceptuelles ;
- propositions de benchmark ;
- misuse ou de confidentialité ;
- travaux liés ;
- collaborations de recherche.

Please clearly distinguish hypotheses from validated results.

---

## License

Specification: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)  
Implementation: private/proprietary during MVP stage.
