# Akitemu AI Systems Reference

A curated collection of production-derived AI architectures, engineering patterns, and implementation evidence.

This repository documents engineering approaches that have been implemented, tested, and validated across private AI systems at Akitemu.

The implementation repositories remain private.

This repository exists to document the architecture, engineering decisions, reusable patterns, and selected implementation evidence behind those systems.

---

# Documentation

## Architecture

| Document | Description |
|----------|-------------|
| [Multi-Repository AI Pipeline](architecture/multi-repo-agent-pipeline.md) | Architecture for orchestrating multiple independent AI components with explicit execution boundaries. |
| [Cross-Cloud Workload Identity](architecture/cross-cloud-identity.md) | Identity architecture connecting AWS workloads with Google services using workload identity federation. |
| Internal AI Operations *(coming soon)* | Document-driven operational AI architecture used for internal workflows. |

---

## Engineering Patterns

| Pattern | Description |
|---------|-------------|
| [Schema First](patterns/schema-first.md) | Treat structure as the primary contract of an AI system. |
| Human Approval Boundaries *(coming soon)* | Separate execution capability from organizational authority. |
| Runtime Truth vs Repository Truth *(coming soon)* | Distinguish implementation, deployment, runtime, and verified-live states. |
| Fixture First Development *(coming soon)* | Introduce external integrations progressively through deterministic validation. |

---

## Case Studies

| Case Study | Description |
|------------|-------------|
| Market Signal Pipeline *(coming soon)* | End-to-end AI market intelligence pipeline built on independent runtime components. |
| Client Delivery Pipeline *(coming soon)* | Deterministic dealer discovery, validation, enrichment, and delivery workflow. |
| Weekly Operations *(coming soon)* | AI-assisted operational reporting pipeline driven by structured organizational state. |

---

# Engineering Philosophy

The systems documented here follow a common set of engineering principles.

- Schema before intelligence.
- Explicit execution boundaries.
- Identity before long-lived credentials.
- Human authority at consequential decision boundaries.
- Fail closed when required truth is missing.
- Architecture owns contracts.
- Runtime evidence is more important than repository assumptions.

These principles are applied across multiple independently implemented systems rather than a single demonstration project.

---

# Technology

Representative technologies used across the documented implementations include:

### Cloud

- AWS Lambda
- Amazon EventBridge
- AWS IAM
- AWS Secrets Manager
- CloudFormation

### Identity

- GitHub Actions OIDC
- Google Workload Identity Federation
- Google Drive APIs
- Google Workspace

### Engineering

- TypeScript
- Node.js
- JSON Schema
- Automated testing
- Multi-repository orchestration
- Deterministic validation

---

# Repository Model

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
        ├── Architecture
        ├── Engineering Patterns
        ├── Case Studies
        └── Implementation Evidence
```

Private repositories remain the implementation source of truth.

This repository documents reusable architecture and engineering knowledge derived from those implementations.

---

# About Akitemu

Akitemu designs AI systems around real work, decisions, and operational boundaries.

Website: https://akitemu.com

GitHub Organization: https://github.com/akitemu-ai