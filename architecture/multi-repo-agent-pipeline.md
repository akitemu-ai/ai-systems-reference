# Multi-Repository AI Pipeline

| Metadata | Value |
|---|---|
| Category | Reference architecture |
| Status | Published |
| Audience | AI system architects, platform engineers, and technical leaders |
| Implementation Basis | Three private, independently implemented runtime components and their audited engineering evidence |

## Overview

This reference architecture demonstrates how multiple independently owned AI system components can operate as one governed pipeline without collapsing orchestration, ingestion, analysis, persistence, and authority into a single agent.

The implementation behind this reference consists of three separate repositories and runtime components:

* a signal harvesting component
* a pipeline orchestration component
* a signal analysis component

The production implementation repositories remain private.

This document describes the architectural pattern, boundaries, and verified implementation characteristics without exposing private infrastructure identifiers, credentials, internal configuration, or proprietary source code.

---

## Architecture

```
Scheduled trigger
        │
        ▼
Pipeline Runner
        │
        ├── invokes Harvest
        │
        ▼
Signal Harvest
        │
        ├── collect / normalize
        ├── deduplicate
        ├── validate
        ├── review gate
        └── persist validated signals
        │
        ▼
Validated persistence layer
        │
        ▼
Runner eligibility + freshness gate
        │
        ├── no valid fresh evidence → STOP
        │
        └── valid fresh evidence
        │
        ▼
Signal Analyzer
        │
        ├── parse
        ├── validate
        ├── deterministic synthesis
        └── validate output
        │
        ▼
Structured synthesis output
```

The central architectural principle is:

**The chain owns orchestration and shared contracts. Individual components own bounded execution.**

No individual agent implicitly controls the entire pipeline.

---

## Component boundaries

### Signal Harvest

The harvesting component owns signal ingestion and normalization.

Its responsibilities include:

* collecting signals through explicitly supported source adapters
* normalizing source data into a canonical internal structure
* deterministic deduplication
* deterministic validation
* evidence handling
* automated review classification
* persistence into raw and validated document layers

It does not own:

* market analysis
* strategy generation
* downstream synthesis
* pipeline orchestration
* autonomous promotion decisions

A deterministic hash based on canonical signal identity is used to support deduplication.

Signals are validated before they can enter the downstream validated layer.

---

### Pipeline Runner

The runner is the orchestration boundary.

It owns:

* scheduled pipeline entry
* invocation of the harvesting component
* interpretation of Harvest execution results
* validation of downstream eligibility
* freshness enforcement
* invocation of the analysis component
* structured pipeline outcomes
* stop taxonomy
* execution evidence and observability

The runner does not perform harvesting or analysis itself.

This separation prevents orchestration logic from becoming hidden inside domain agents.

---

### Signal Analyzer

The analyzer is a downstream-only component.

It consumes only validated signal input.

Its responsibilities include:

* reading the validated signal layer
* deterministic parsing
* strict structural validation
* synthesis of validated signals
* output contract validation
* controlled persistence of structured synthesis

It explicitly does not consume the raw ingestion layer.

This creates a hard information boundary:

```
Raw observations
      │
      ▼
Harvest validation
      │
      ▼
Validated signal layer
      │
      ▼
Analyzer
```

The analyzer cannot bypass upstream validation by reading raw source material directly.

---

## Shared contract ownership

A multi-repository system introduces a critical problem:

Who owns the contracts between repositories?

The architecture resolves this by separating local implementation truth from chain-level shared truth.

```
Chain-level architecture
        │
        ├── execution order
        ├── shared schemas
        ├── handoff contracts
        ├── invocation boundaries
        ├── access boundaries
        ├── failure taxonomy
        └── verification model
        │
        ▼
Repository-local implementation
        │
        └── implements assigned contract
```

An individual repository must not silently redefine a shared handoff contract.

Shared schema changes are architecture changes, not local implementation details.

---

## Schema before intelligence

The pipeline treats structure as an explicit contract.

```
External / fixture input
        │
        ▼
Normalization
        │
        ▼
Schema validation
        │
        ▼
Validated state
        │
        ▼
Analysis
        │
        ▼
Output contract validation
        │
        ▼
Persisted result
```

AI or probabilistic processing does not replace structural validation.

The architecture assumes:

**The model is an implementation component. The schema is the contract.**

Malformed or structurally unusable input fails before downstream execution.

Invalid synthesis output fails before persistence.

---

## Freshness-gated execution

Successful execution of an upstream component is not sufficient evidence that downstream execution should continue.

The runner evaluates whether the upstream execution actually produced eligible fresh persisted data.

```
Harvest execution success
        │
        ▼
Was validated data persisted?
        │
    ┌───┴───┐
    │       │
   NO      YES
    │       │
    ▼       ▼
  STOP    Is it fresh?
            │
        ┌───┴───┐
        │       │
       NO      YES
        │       │
        ▼       ▼
      STOP    Analyzer
```

This prevents a technically successful but semantically empty upstream run from triggering unnecessary or incorrect downstream analysis.

Duplicate-only or otherwise non-fresh outcomes can therefore terminate safely without invoking the analyzer.

---

## Fail-closed orchestration

The pipeline is designed to stop when required evidence is missing or invalid.

Examples include:

* Harvest execution failure
* missing validated persistence evidence
* unusable validated input
* stale input
* schema validation failure
* downstream invocation failure
* unresolved governance requirements in controlled execution modes

The intended behavior is:

```
uncertainty about required state
            ↓
          STOP
```

not:

```
uncertainty about required state
            ↓
    continue optimistically
```

This makes failure behavior part of the architecture rather than an implementation afterthought.

---

## Fixture-first execution

External integrations are introduced through controlled execution modes.

The architecture supports deterministic fixture-backed execution before broader live network behavior is enabled.

```
Deterministic fixtures
        │
        ▼
Contract validation
        │
        ▼
Pipeline behavior validation
        │
        ▼
Controlled integration boundary
        │
        ▼
Governed live capability
```

Fixture-first does not mean mock-only architecture.

It means external uncertainty is introduced progressively after internal contracts and execution behavior are deterministic.

This supports:

* reproducibility
* safer integration development
* deterministic testing
* controlled activation of network access
* explicit evidence for promotion toward live execution

---

## Cross-cloud document access

Selected runtime components access controlled Google document resources from AWS workloads.

The architectural pattern uses workload identity rather than embedding long-lived Google service-account credentials in application code.

```
AWS runtime workload
        │
        ▼
AWS workload identity
        │
        ▼
Google Workload Identity Federation
        │
        ▼
Authorized Google identity
        │
        ▼
Explicit document access
```

The implementation separates:

* runtime identity
* deployment identity
* document authorization
* application logic

This follows the principle:

**Identity before long-lived credentials.**

Access is limited to explicitly configured document boundaries.

---

## Deployment identity

Deployment uses GitHub Actions with AWS OIDC-based identity.

```
GitHub Actions
        │
        ▼
OIDC identity
        │
        ▼
Scoped AWS deployment role
        │
        ▼
Deployment preparation / update
```

Long-lived AWS deployment credentials are not required in the repository workflow.

Deployment authority and runtime authority remain separate concerns.

---

## Observability as structured evidence

The orchestration layer produces structured execution state rather than relying only on free-form logs.

Execution evidence can include:

* correlation identifiers
* trace identifiers
* boundary events
* timestamps
* eligibility evidence
* freshness state
* stop reasons
* runtime classification
* verification references

Conceptually:

```
Scheduler
   │
   ▼
Runner
   │
   ├── Harvest invocation evidence
   │
   ├── persistence / freshness evidence
   │
   ├── eligibility decision
   │
   ├── Analyzer invocation evidence
   │
   ▼
Final structured run outcome
```

This allows runtime behavior to be evaluated as evidence rather than inferred from deployment configuration alone.

---

## Runtime truth and operational evidence

The architecture distinguishes several evidence states:

```
planned
   ↓
implemented
   ↓
runtime-configured
   ↓
deployable
   ↓
verified-live
```

Repository code alone does not prove that a capability exists correctly in production.

Likewise, deployment configuration alone does not prove successful runtime behavior.

The architecture therefore separates:

* repository truth
* deployment truth
* runtime truth
* verified-live evidence

These states and their evidence requirements are developed in [Runtime Truth vs Repository Truth](../patterns/runtime-truth-vs-repository-truth.md). Keeping them separate prevents code or configuration artifacts from being treated as proof of operational behavior.

---

## Authority boundaries

Execution capability and decision authority are intentionally separated.

The system can automate:

* scheduled invocation
* component orchestration
* validation
* persistence checks
* downstream eligibility
* structured evidence generation

But consequential state transitions can still require explicit governance or human authority.

```
Automated preparation / execution
            │
            ▼
   Explicit authority boundary
            │
            ▼
  Approved state transition
```

The implementation includes governance patterns for controlled promotion and accountable decisions without giving individual agents unrestricted authority.

---

## Why separate repositories?

The three components are intentionally separated because they have different responsibilities.

### Harvest

Owns:

* ingestion
* normalization
* validation
* review classification
* validated persistence

### Runner

Owns:

* orchestration
* eligibility
* freshness
* execution sequencing
* chain-level runtime evidence

### Analyzer

Owns:

* validated input consumption
* deterministic analysis
* synthesis contract
* structured output

This creates explicit boundaries between:

**collection → orchestration → analysis**

rather than building one large autonomous agent with implicit internal authority.

---

## Failure isolation

Repository and runtime separation also improves failure isolation.

```
Harvest failure
    ↓
Analyzer not invoked

No fresh validated data
    ↓
Analyzer not invoked

Invalid analyzer input
    ↓
Synthesis not persisted

Analyzer failure
    ↓
Explicit pipeline failure outcome
```

Each component can fail inside its own responsibility boundary while the runner maintains deterministic chain-level behavior.

---

## Engineering evidence

The private implementation behind this reference includes automated testing across all three components.

At the audited implementation state:

* Signal Analyzer: **130 passing tests**
* Pipeline Runner: **268 passing tests**
* Signal Harvest: extensive automated coverage across ingestion, validation, review, persistence, runtime guards, and source adapters

The implementation also includes explicit verification artifacts for:

* deployment readiness
* cross-repository maturity
* runtime evidence
* observability
* controlled live execution
* governance boundaries

Test counts represent locally verified repository state at the time of audit and are not presented as permanent live metrics.

---

## Key architectural lessons

### 1. Chain authority before repository autonomy

Cross-repository contracts must be owned above individual repositories.

### 2. Orchestration should be explicit

Domain agents should not silently become system orchestrators.

### 3. Successful execution is not sufficient evidence

Downstream execution should depend on validated state transitions, not merely successful function returns.

### 4. Freshness is part of the contract

Persisted data must be eligible and current enough for downstream consumption.

### 5. Fail closed when required truth is missing

Unknown required state should stop execution rather than trigger optimistic continuation.

### 6. Runtime evidence matters more than deployment assumptions

Code and infrastructure definitions do not prove verified runtime behavior.

### 7. Identity is an architectural boundary

Deployment identity, runtime identity, and external-service identity should remain explicit and separable.

### 8. AI capability does not imply authority

Automation can increase while consequential decision authority remains governed.

---

## Technology represented in the implementation

* AWS Lambda
* Amazon EventBridge / EventBridge Scheduler
* AWS IAM
* AWS Secrets Manager
* GitHub Actions
* GitHub OIDC
* Google Workload Identity Federation
* Google Drive APIs
* Node.js
* TypeScript
* structured schema validation
* deterministic processing pipelines
* multi-repository orchestration
* automated testing

---

## Public reference boundary

This document is a sanitized architectural reference.

It intentionally does not expose:

* private source code
* AWS account identifiers
* IAM role ARNs
* Google resource identifiers
* secret names or values
* internal environment configuration
* private repository history
* client data
* operational credentials

The private repositories remain the implementation source of truth.

This public repository exists to demonstrate the architecture, engineering decisions, implementation patterns, and evidence behind the system.

---

## Related documents

* [Cross-Cloud Workload Identity](cross-cloud-identity.md)
* [Schema First](../patterns/schema-first.md)
* [Human Approval Boundaries](../patterns/human-approval-boundaries.md)
* [Runtime Truth vs Repository Truth](../patterns/runtime-truth-vs-repository-truth.md)
