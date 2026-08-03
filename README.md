# Akitemu AI Systems Reference

A curated collection of production-derived AI architectures, engineering patterns, and implementation evidence.

This engineering handbook documents approaches that have been implemented, tested, and validated across private AI systems at Akitemu. The implementation repositories remain private; this repository preserves reusable architecture, engineering decisions, patterns, and selected evidence without exposing private implementation details.

## Documentation overview

The handbook is organized into two complementary parts:

* **Architecture references** describe implemented system boundaries, component responsibilities, identity flows, and operational evidence.
* **Engineering patterns** extract reusable principles from those implementations and explain when the principles matter.

Start with the [Multi-Repository AI Pipeline](architecture/multi-repo-agent-pipeline.md) for the system-level view. Use the pattern documents for focused guidance on contracts, authority, and operational evidence. The [Cross-Cloud Workload Identity](architecture/cross-cloud-identity.md) reference provides the detailed identity architecture.

## Architecture references

| Document | Focus | Related patterns |
|---|---|---|
| [Multi-Repository AI Pipeline](architecture/multi-repo-agent-pipeline.md) | Orchestrating independent AI components with explicit execution, validation, persistence, and authority boundaries. | [Schema First](patterns/schema-first.md), [Human Approval Boundaries](patterns/human-approval-boundaries.md), [Runtime Truth vs Repository Truth](patterns/runtime-truth-vs-repository-truth.md) |
| [Cross-Cloud Workload Identity](architecture/cross-cloud-identity.md) | Connecting AWS workloads to explicitly authorized Google resources through workload identity federation. | [Runtime Truth vs Repository Truth](patterns/runtime-truth-vs-repository-truth.md) |
| Internal AI Operations *(coming soon)* | Document-driven operational AI architecture used for internal workflows. | — |

## Engineering patterns

| Pattern | Focus | Implementation context |
|---|---|---|
| [Schema First](patterns/schema-first.md) | Treat structure as the primary contract of an AI system. | Input, handoff, output, and persistence validation. |
| [Human Approval Boundaries](patterns/human-approval-boundaries.md) | Separate execution capability from organizational authority. | Review gates and controlled state transitions. |
| [Runtime Truth vs Repository Truth](patterns/runtime-truth-vs-repository-truth.md) | Distinguish implementation, deployment, runtime, and verified runtime evidence. | Deployment readiness, structured run outcomes, and live verification. |
| Fixture First Development *(coming soon)* | Introduce external integrations progressively through deterministic validation. | Controlled integration and promotion toward live execution. |

## Case studies

| Case study | Description |
|---|---|
| Market Signal Pipeline *(coming soon)* | End-to-end AI market intelligence pipeline built on independent runtime components. |
| Client Delivery Pipeline *(coming soon)* | Deterministic dealer discovery, validation, enrichment, and delivery workflow. |
| Weekly Operations *(coming soon)* | AI-assisted operational reporting pipeline driven by structured organizational state. |

## Engineering philosophy

The systems documented here follow a common set of engineering principles:

* Schema before intelligence.
* Explicit execution boundaries.
* Identity before long-lived credentials.
* Human authority at consequential decision boundaries.
* Fail closed when required truth is missing.
* Architecture owns contracts.
* Runtime evidence is more important than repository assumptions.

These principles are applied across multiple independently implemented systems rather than a single demonstration project.

## Technology represented

Representative technologies used across the documented implementations include:

| Area | Technologies and practices |
|---|---|
| Cloud | AWS Lambda, Amazon EventBridge, AWS IAM, AWS Secrets Manager, CloudFormation |
| Identity and external access | GitHub Actions OIDC, Google Workload Identity Federation, Google Drive APIs, Google Workspace |
| Engineering | TypeScript, Node.js, JSON Schema, automated testing, multi-repository orchestration, deterministic validation |

## Repository model

```
Private implementation repositories
        │
        ├── production code
        ├── infrastructure
        ├── deployment
        ├── runtime configuration
        └── internal documentation
        │
        ▼
Curated public reference
        │
        ├── architecture references
        ├── engineering patterns
        ├── case studies
        └── implementation evidence
```

Private repositories remain the implementation source of truth. This public handbook documents reusable architecture and engineering knowledge derived from those implementations.

## About Akitemu

Akitemu designs AI systems around real work, decisions, and operational boundaries.

* [Akitemu website](https://akitemu.com)
* [Akitemu GitHub organization](https://github.com/akitemu-ai)
