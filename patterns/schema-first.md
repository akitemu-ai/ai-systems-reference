# Schema First

| Metadata | Value |
|---|---|
| Category | Engineering pattern |
| Status | Published |
| Audience | AI system architects and engineers |
| Implementation Basis | Validated input, handoff, output, and persistence contracts across private AI system implementations |

## Overview

Schema First is an engineering principle where structure is treated as the primary contract of an AI system.

Language models, external APIs, and user input are considered implementation details.

The schema defines what the system accepts as truth.

If data does not satisfy the contract, it does not become part of the system state.

---

## Why Schema First?

AI systems often fail because they trust generated content before validating its structure.

A traditional AI workflow is frequently:

```
Input
    ↓
AI Processing
    ↓
Output
```

This assumes that the model output is immediately usable.

Schema First changes the order:

```
Input
    ↓
Normalize
    ↓
Validate
    ↓
Accepted State
    ↓
AI Processing
    ↓
Validate Again
    ↓
Persist
```

Validation is performed both before and after AI processing.

The AI model is never the source of truth.

---

## Structure before meaning

Language models are good at producing plausible information.

Systems need reliable information.

Schema First therefore separates two concerns:

### Structural correctness

Can this object exist inside the system?

### Semantic usefulness

Is this information valuable?

Semantic quality can improve over time.

Structural correctness cannot be optional.

---

## State transitions

Every meaningful state transition is guarded by an explicit contract.

Conceptually:

```
Candidate data
        │
        ▼
Schema validation
        │
    ┌───┴───┐
    │       │
  Invalid  Valid
    │       │
    ▼       ▼
  Reject  Continue
```

The schema controls whether the state transition is allowed.

---

## Normalize before validating

Different external systems produce different representations of similar data.

Normalization converts those representations into a canonical internal structure.

```
External source A
        │
External source B
        │
External source C
        ▼
  Normalization
        ▼
Canonical structure
        ▼
  Schema validation
```

Validation should operate against the canonical model rather than every external representation independently.

---

## AI inside the contract

The language model operates inside architectural boundaries.

```
Valid input
      │
      ▼
   AI Model
      │
      ▼
Candidate output
      │
      ▼
Output validation
      │
  ┌───┴───┐
  │       │
Reject  Accept
```

The model proposes.

The schema decides.

---

## Deterministic boundaries

Probabilistic systems should terminate in deterministic boundaries.

Examples include:

* JSON Schema validation
* explicit field validation
* required-property validation
* enum validation
* identifier validation
* reference validation

The objective is not to make AI deterministic.

The objective is to make system state deterministic.

---

## Schema ownership

Schemas belong to the architecture rather than individual AI prompts.

Changing a schema changes the contract between system components.

This makes schema evolution an architectural decision rather than a prompt engineering exercise.

---

## Multi-repository systems

In multi-repository architectures, schemas become shared contracts.

```
Repository A
      │
      ▼
  Shared schema
      ▲
      │
Repository B
```

Neither repository owns the contract independently.

The architecture owns it.

---

## Failure behavior

Invalid structure is not repaired implicitly.

The default response is:

```
Invalid structure
        │
        ▼
     Reject
```

This fail-closed behavior prevents malformed data from silently propagating through downstream systems.

---

## Benefits

Schema First provides:

* predictable state transitions
* explicit contracts
* safer AI integration
* easier testing
* repository independence
* deterministic orchestration
* simpler debugging
* controlled evolution of system interfaces

---

## Relationship to AI

Schema First does not reduce the importance of AI.

It changes where AI is trusted.

Instead of trusting generated structure, the architecture trusts validated structure.

The AI system contributes intelligence.

The schema defines reality.

---

## Key principle

**The model is an implementation component.**

**The schema is the contract.**

---

## Related documents

* [Multi-Repository AI Pipeline](../architecture/multi-repo-agent-pipeline.md)
* [Human Approval Boundaries](human-approval-boundaries.md)
* [Runtime Truth vs Repository Truth](runtime-truth-vs-repository-truth.md)
