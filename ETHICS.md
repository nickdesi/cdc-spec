# CDC Ethical Framework

CDC models cognitive trajectories. This capability carries significant privacy and ethical responsibilities that must be addressed by design, not as afterthoughts.

---

## Core principles

### 1. Transparency
Every CDC_UNIT stored for a user is inspectable by that user. The system exposes a `/memory/inspect` endpoint returning all deltas, their type (ANCHORED/INFERRED), confidence, source reference, and creation date. No hidden state.

### 2. User control
- **Annotate**: users can add notes to any delta
- **Correct**: users can override the `delta_vector` direction with a manual correction, logged as a human-override event
- **Delete**: users can delete any delta. Deletion triggers automatic refossilization of dependent Fossil Nodes. Original data is purged within 24h.

### 3. Right to erasure
Full account deletion removes all CDC_UNITs, Fossil Nodes, session references, and audit logs within 72h. No archival copies are retained after this window.

### 4. Strict data isolation
- Deltas are scoped to a single user. Cross-user operations are architecturally prohibited.
- Deltas are never used for model training without explicit, separate, revocable consent.
- Deltas are never sold, shared with third parties, or used for advertising.

### 5. Predictive capability disclosure
CDC can, by design, infer future cognitive positions from trajectory patterns. This capability must be disclosed explicitly at onboarding. Users must opt-in separately to any feature that surfaces predictive outputs.

### 6. Audit log
Every system operation (ENCODE, INDEX, FOSSILIZE, RETRIEVE, VERIFY, DELETE) is logged with:
- Timestamp
- Operation type
- Delta IDs affected
- Triggering session reference
- User ID (hashed)

Audit logs are retained for 90 days and are exportable by the user.

---

## What CDC will never do

- ❌ Share cognitive trajectory data across users
- ❌ Use trajectory data for behavioral targeting or advertising
- ❌ Surface predictive outputs without explicit user opt-in
- ❌ Retain data after deletion request beyond the 72h purge window
- ❌ Resolve contradictions silently (always logged, always user-visible)

---

## Consent flow

At first activation, users must explicitly acknowledge:

1. CDC stores transitions in their understanding, not just facts
2. These transitions build a cognitive trajectory model over time
3. The system can surface patterns in how their thinking evolves
4. All data is inspectable, correctable, and deletable at any time
5. Predictive features require separate opt-in

No dark patterns. No pre-checked boxes.
