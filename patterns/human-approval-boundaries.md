# Human Approval Boundaries

| Metadata | Value |
|---|---|
| Category | Engineering pattern |
| Status | Published |
| Audience | AI system architects, governance owners, and technical leaders |
| Implementation Basis | Review gates, governed promotion, and explicit authority boundaries reflected in private AI system implementations |

## Overview

Human Approval Boundaries is an engineering principle that separates **execution capability** from **decision authority**.

AI systems can prepare information, validate inputs, orchestrate workflows, and recommend actions.

They should not automatically own every consequential decision simply because they are technically capable of executing it.

The goal is not to maximize human involvement.

The goal is to place humans only where judgment, accountability, or organizational authority is genuinely required.

---

## The problem

Many AI systems evolve toward one of two extremes.

### Everything is automated

```
AI
  │
  ▼
Execute
```

This creates hidden authority.

The system becomes capable of making decisions that may have legal, financial, operational, or strategic consequences.

---

### Everything requires manual approval

```
AI
  │
  ▼
Human
  │
  ▼
Human
  │
  ▼
Human
  │
  ▼
Execute
```

This creates human middleware.

People spend their time forwarding information instead of making decisions.

Neither extreme scales well.

---

## The architectural principle

Execution and authority are separate concerns.

```
AI preparation
      │
      ▼
Validation
      │
      ▼
Recommendation
      │
      ▼
Authority boundary
      │
  ┌───┴───┐
  │       │
Approved Rejected
  │       │
  ▼       ▼
Execute   Stop
```

The system may prepare everything required for a decision.

Crossing the authority boundary is a separate responsibility.

---

## What AI should automate

Examples include:

* data collection
* normalization
* validation
* enrichment
* orchestration
* scheduling
* report generation
* evidence gathering
* recommendation generation
* deterministic classification

These activities increase efficiency without transferring organizational authority.

---

## What may require approval

Examples include:

* publishing externally
* modifying production state
* financial commitments
* customer-facing actions
* policy changes
* irreversible operations
* strategic decisions
* legal acceptance
* operational overrides

Whether approval is required depends on the organization's governance model.

The important point is that the boundary is explicit.

---

## Explicit authority boundaries

Authority boundaries should be visible in the architecture.

```
Automated pipeline
        │
        ▼
Explicit approval point
        │
    ┌───┴───┐
    │       │
  Approve Reject
    │       │
    ▼       ▼
Continue Stop
```

The system should not hide approval inside prompts or undocumented operational procedures.

---

## Human attention is limited

Human review is expensive.

Approval boundaries should therefore be concentrated where human judgment adds value.

Humans should not review:

* deterministic schema validation
* duplicate detection
* orchestration sequencing
* successful infrastructure operations
* routine state transitions

Human attention should be reserved for uncertainty, risk, and accountability.

---

## Approval is not validation

Validation answers:

**Is this structurally correct?**

Approval answers:

**Should this happen?**

These questions are fundamentally different.

A perfectly valid proposal may still be rejected.

An invalid proposal should never reach the approval boundary.

---

## Relationship to Schema First

Schema First ensures that only structurally valid information enters the decision process.

Human Approval Boundaries determine who owns the final authority once the information is valid.

```
Input
  │
  ▼
Normalize
  │
  ▼
Validate
  │
  ▼
Recommendation
  │
  ▼
Human Approval Boundary
  │
  ▼
Authorized Action
```

The two patterns complement each other.

---

## Fail closed

If authority cannot be determined, the system should stop.

```
Missing authority
        │
        ▼
      Stop
```

The architecture should never assume approval because approval information is unavailable.

---

## Multi-agent systems

In multi-agent architectures, authority should not emerge accidentally.

One agent collecting data does not gain authority over another agent's responsibilities.

One agent orchestrating execution does not automatically become the system owner.

Authority is defined by architecture, not by message flow.

---

## Review gates

Review gates are explicit implementation examples of approval boundaries.

A review gate allows the system to:

* prepare evidence
* classify findings
* identify uncertainty
* present recommendations

without automatically promoting those recommendations into accepted organizational decisions.

---

## Separation of responsibilities

Different components may own different responsibilities.

For example:

```
Harvest
    │
    ▼
Validation
    │
    ▼
Analysis
    │
    ▼
Human Approval
    │
    ▼
Publication
```

Each component remains inside its own authority boundary.

No single component implicitly owns the complete lifecycle.

---

## Why this matters

As AI systems become more capable, they will increasingly automate technical work.

Without explicit approval boundaries, technical capability can unintentionally become organizational authority.

Separating execution from authority allows systems to become more autonomous while keeping accountability transparent.

---

## Key principles

* Execution capability is not decision authority.
* Validation is not approval.
* Human attention belongs at consequential decisions.
* Authority boundaries should be explicit.
* Systems should fail closed when authority is missing.
* AI prepares decisions; organizations own decisions.

---

## Related documents

* [Multi-Repository AI Pipeline](../architecture/multi-repo-agent-pipeline.md)
* [Schema First](schema-first.md)
* [Runtime Truth vs Repository Truth](runtime-truth-vs-repository-truth.md)
