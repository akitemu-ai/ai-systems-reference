# Akitemu AI Systems Reference

A curated technical reference of AI systems, architectures, and engineering patterns implemented at Akitemu.

The production, internal, and client repositories behind this work remain private. This repository documents selected architecture, implementation evidence, and reusable engineering patterns without exposing credentials, client data, private infrastructure details, or proprietary source code.

## What this repository demonstrates

The systems documented here cover practical engineering across:

* AI agent architectures
* deterministic and LLM-assisted workflows
* multi-repository orchestration
* serverless AWS infrastructure
* cross-cloud identity
* schema-first validation
* human approval and authority boundaries
* client-delivery pipelines
* AI-first software development

The emphasis is not on autonomous agents for their own sake.

The focus is on building systems where execution boundaries, validation, identity, authority, and failure behavior are explicit.

---

## Reference architectures

### Multi-repository AI pipeline

A three-component agent pipeline demonstrating:

```
Scheduled execution
        ↓
Pipeline Orchestrator
        ↓
Signal Harvest
        ↓
Validated persistence
        ↓
Freshness / eligibility boundary
        ↓
Signal Analysis
        ↓
Structured synthesis
```

Implemented capabilities include:

* AWS Lambda-to-Lambda orchestration
* deterministic execution boundaries
* schema validation
* freshness-gated downstream execution
* Google Drive persistence
* AWS-to-Google workload identity
* structured runtime outcomes
* fixture-first execution
* bounded live integration paths
* automated testing

→ [Read the Multi-Repository AI Pipeline architecture](architecture/multi-repo-agent-pipeline.md)

### Cross-cloud workload identity

Reference architecture for accessing Google services from AWS workloads without embedding long-lived Google service-account keys in application code.

```
AWS Lambda
    ↓
AWS workload identity
    ↓
Google Workload Identity Federation
    ↓
Google service identity
    ↓
Google APIs / Drive
```

Engineering principles demonstrated:

* identity before credentials
* workload-based federation
* least-privilege boundaries
* separation of deployment and runtime identity
* secret minimization

→ `architecture/cross-cloud-identity.md` *(coming next)*

### Internal AI operations

An internal document-driven AI workflow where agents consume explicit state, generate structured operational outputs, and write into controlled document boundaries.

The implementation explores:

* explicit source documents
* structured state generation
* scheduled serverless execution
* deterministic output guards
* controlled document writes
* human authority over production changes

→ `architecture/internal-ai-operations.md` *(coming next)*

---

## Selected implementation evidence

### Client delivery — dealer discovery and qualification

A bounded client-delivery pipeline was implemented for dealer discovery, validation, enrichment, scoring, and delivery preparation.

```
Seed data
    ↓
Deterministic validation
    ↓
Website-assisted enrichment
    ↓
Scoring and classification
    ↓
Candidate pool
    ↓
Contact enrichment
    ↓
Delivery exports
```

Implementation characteristics:

* deterministic validation and scoring
* explainable classification
* website-assisted enrichment
* human-in-the-loop review
* structured XLSX delivery
* automated test coverage

**Locally verified test suite: 113 passing tests.**

The implementation was used in a paid client pilot. Customer business impact is intentionally not claimed as validated.

→ `case-studies/bounded-client-delivery.md` *(coming next)*

### Deterministic signal analysis

A structured analysis component was implemented around explicit input/output contracts rather than unrestricted LLM behavior.

Capabilities include:

* validated structured input
* deterministic synthesis
* output validation
* controlled persistence
* provider abstraction boundary
* runtime evidence mechanisms

**Locally verified test suite: 130 passing tests.**

→ `case-studies/deterministic-signal-analysis.md` *(coming next)*

### Cross-agent orchestration

A dedicated orchestration component coordinates independent agent runtimes and prevents downstream execution when required persistence or freshness conditions are not satisfied.

Capabilities include:

* Lambda invocation boundaries
* downstream eligibility checks
* fail-closed execution
* freshness validation
* structured execution outcomes
* independent component ownership

**Locally verified test suite: 268 passing tests.**

→ `case-studies/cross-agent-orchestration.md` *(coming next)*

---

## Engineering patterns

### Schema before intelligence

AI-generated or AI-processed data is treated as untrusted until it satisfies an explicit contract.

```
Input
  ↓
Schema
  ↓
Processing / AI
  ↓
Schema validation
  ↓
Allowed state transition
```

The model is an implementation component.

The schema is the contract.

→ `patterns/schema-first.md` *(coming next)*

### Execution autonomy ≠ decision authority

The ability of an AI system to execute an action does not automatically give it authority to decide that the action should occur.

```
AI preparation / execution
          ↓
Policy + authority boundary
          ↓
Approved consequential action
```

This distinction is used to separate technical capability from organizational authority.

→ `patterns/authority-boundaries.md` *(coming next)*

### Human attention at authority boundaries

Human involvement should be concentrated where judgment, accountability, risk acceptance, or consequential approval is required.

Humans should not become manual middleware between otherwise automatable system components.

```
Automated execution
        ↓
Explicit authority boundary
        ↓
Human decision when required
        ↓
Controlled continuation
```

→ `patterns/human-approval-boundaries.md` *(coming next)*

### Fixture-first development

External integrations can be introduced progressively instead of making live network dependencies a prerequisite for architecture validation.

```
Deterministic fixtures
        ↓
Contract validation
        ↓
Controlled integration boundary
        ↓
Bounded live execution
        ↓
Runtime evidence
```

This supports reproducibility, testing, and safer progression toward live integrations.

→ `patterns/fixture-first.md` *(coming next)*

---

## Technology

Technologies used across the referenced implementations include:

### Cloud and runtime

* AWS Lambda
* Amazon EventBridge / EventBridge Scheduler
* AWS IAM
* AWS Secrets Manager
* CloudFormation

### Identity and integration

* GitHub Actions OIDC
* Google Workload Identity Federation
* Google Drive APIs
* Google Workspace integrations

### AI and application engineering

* OpenAI APIs and provider abstractions
* TypeScript / JavaScript
* JSON Schema and structured validation
* deterministic processing pipelines
* multi-agent orchestration patterns

### Engineering workflow

* Git
* GitHub
* GitHub Actions
* pull-request-based development
* automated testing
* AI-assisted software development

---

## Repository model

This repository is intentionally separated from the actual implementation repositories.

```
Private implementation repositories
        │
        ├── source code
        ├── infrastructure configuration
        ├── runtime configuration
        └── internal continuity documentation
        │
        ↓ curated and sanitized
        │
Public AI Systems Reference
        ├── architecture
        ├── case studies
        └── reusable engineering patterns
```

Private repositories remain the implementation source of truth.

This repository is a public evidence and reference layer.

---

## About Akitemu

Akitemu designs and builds AI systems around real work, decisions, and operational boundaries.

Website: [akitemu.com](https://akitemu.com)

GitHub: [github.com/akitemu-ai](https://github.com/akitemu-ai)
