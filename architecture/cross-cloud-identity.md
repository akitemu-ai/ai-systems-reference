# Cross-Cloud Workload Identity

## Overview

This reference architecture demonstrates how workloads running in AWS can access explicitly authorized Google resources without embedding long-lived Google service-account private keys in application code.

The pattern is used across multiple independently implemented AI system components at Akitemu.

It separates three identity concerns:

* deployment identity
* AWS runtime identity
* Google workload identity

The private implementation repositories remain the source of truth.

This document describes the reusable architectural pattern without exposing account identifiers, role ARNs, provider identifiers, service-account identifiers, document IDs, secret values, or private infrastructure configuration.

---

## Identity architecture

```
Developer / approved repository change
            │
            ▼
     GitHub Actions
            │
            ▼
    GitHub OIDC identity
            │
            ▼
   Scoped AWS deploy role
            │
            ▼
     AWS deployment
            │
            ▼
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
    Explicit Google resources
```

Two separate federation boundaries are involved:

1. GitHub Actions → AWS
2. AWS workload → Google

These boundaries solve different problems and should not be treated as one shared credential mechanism.

---

## Boundary 1: GitHub Actions to AWS

Deployment workflows use GitHub Actions OIDC to obtain scoped AWS deployment credentials.

```
GitHub repository
        │
        ▼
GitHub Actions workflow
        │
        ▼
    OIDC token
        │
        ▼
AWS trusted identity boundary
        │
        ▼
  Scoped deploy role
        │
        ▼
   AWS deployment
```

The repository workflow does not require permanent AWS access keys for normal deployment authentication.

The trust relationship is based on identity assertions from the GitHub Actions workload.

This enables AWS to evaluate who is requesting deployment access rather than relying on a reusable static credential stored in the repository workflow.

---

## Boundary 2: AWS runtime to Google

Runtime workloads access Google APIs through Google Workload Identity Federation.

Conceptually:

```
AWS Lambda
    │
    ▼
AWS workload identity
    │
    ▼
External-account credential configuration
    │
    ▼
Google Security Token Service
    │
    ▼
Google Workload Identity Federation
    │
    ▼
Authorized Google identity
    │
    ▼
Google APIs / Drive resources
```

The application does not require an embedded Google service-account private key.

Instead, Google authentication is derived from the workload identity and federation configuration.

---

## Configuration is not a private key

A critical distinction in this architecture is the difference between:

* identity configuration
* long-lived private credentials

The runtime uses external-account / Workload Identity Federation configuration to describe how Google should trust and exchange the AWS workload identity.

That configuration may be stored and retrieved through a controlled secret-management boundary.

Conceptually:

```
AWS Secrets Manager
        │
        ▼
WIF external-account configuration
        │
        ▼
Google authentication library
        │
        ▼
Federated token exchange
```

The stored configuration enables the federation process.

It is not equivalent to storing a reusable Google service-account private key.

This distinction allows sensitive configuration to remain centrally managed while authentication still depends on workload identity.

---

## Runtime authentication flow

A simplified runtime flow is:

```
Lambda starts
    │
    ▼
Load controlled WIF configuration
    │
    ▼
Initialize Google authentication
    │
    ▼
Present / derive AWS workload identity
    │
    ▼
Federated token exchange
    │
    ▼
Obtain short-lived Google authorization
    │
    ▼
Access explicitly permitted Google resource
```

The application receives usable authorization only through the federation flow.

The architecture avoids treating a manually distributed long-lived credential as the workload's identity.

---

## Explicit resource boundaries

Successful authentication does not imply unrestricted access.

Identity answers:

**Who is the workload?**

Authorization answers:

**What may that workload access?**

The architecture keeps these concerns separate.

```
Workload identity
        │
        ▼
Federated Google identity
        │
        ▼
Explicit authorization boundary
        │
        ▼
Permitted documents / APIs
```

Applications are configured around explicit resource targets rather than assuming broad access to an entire document environment.

This reduces accidental authority expansion.

---

## Multiple workloads, shared pattern

The same architectural pattern is reused across multiple system components with different business responsibilities.

Examples include workloads that:

* read structured source documents
* generate operational state
* generate weekly operational outputs
* persist validated signal data
* consume validated signal data
* write structured synthesis documents

The application logic differs.

The identity architecture remains consistent.

```
Workload A ─┐
Workload B ─┼──► common identity pattern ──► bounded Google access
Workload C ─┘
```

This demonstrates an important infrastructure principle:

**Reuse the proven identity pattern without collapsing application responsibilities.**

---

## Separation of deployment and runtime authority

Deployment identity and runtime identity are intentionally different.

### Deployment identity

Used to:

* update infrastructure
* deploy application artifacts
* configure approved runtime resources

### Runtime identity

Used to:

* execute application behavior
* access explicitly permitted runtime dependencies
* interact with authorized external services

Conceptually:

```
GitHub Actions
      │
      ▼
Deployment authority
      │
      X
      │
Runtime authority
      ▲
      │
  AWS workload
```

A runtime workload does not automatically inherit deployment authority.

A deployment workflow does not automatically become the application's runtime identity.

This separation limits the blast radius of each identity.

---

## Identity before long-lived credentials

The architectural principle is:

**Prefer identity-based trust over manually managed long-lived credentials.**

Traditional static-key model:

```
Application
    │
    ▼
Stored long-lived key
    │
    ▼
External service
```

Workload-identity model:

```
Application workload
    │
    ▼
Verifiable workload identity
    │
    ▼
Federation / trust policy
    │
    ▼
Short-lived authorization
    │
    ▼
External service
```

The second model makes trust relationships explicit and reduces dependence on reusable credential material.

---

## Least privilege as architecture

Least privilege is not treated only as a configuration optimization.

It is part of the system design.

The architecture separates:

* repository identity
* deployment identity
* runtime identity
* external-service identity
* resource authorization

Each boundary should grant only the authority required for its responsibility.

```
Repository workflow
      │
      ▼
scoped deployment authority

Runtime workload
      │
      ▼
scoped runtime authority

Federated Google identity
      │
      ▼
scoped external-resource access
```

This prevents convenience from becoming implicit cross-system authority.

---

## Secret minimization

Secret management remains necessary even in identity-first systems.

The goal is not:

**zero configuration requiring protection**

The goal is:

**minimize reusable credential material and make identity the primary trust mechanism.**

Controlled secret storage may still contain:

* external-account configuration
* API credentials for systems that do not support federation
* other bounded runtime configuration

But wherever federation is available, long-lived cloud identity keys should not be the default authentication mechanism.

---

## Failure behavior

Authentication failures should fail closed.

Examples include:

* missing federation configuration
* malformed external-account configuration
* failed identity exchange
* insufficient Google authorization
* inaccessible configured resource
* missing runtime environment configuration

The intended behavior is:

```
identity or authorization failure
            │
            ▼
          STOP
```

not:

```
identity or authorization failure
            │
            ▼
silently downgrade security model
```

The workload should not fall back automatically to embedded credentials or broader alternate identities.

---

## Proven-pattern reuse

Cross-cloud identity is security-sensitive infrastructure.

Once a pattern has been implemented and validated, new workloads should reuse that pattern rather than independently redesigning authentication.

```
Proven workload
      │
      ▼
Validated identity pattern
      │
      ├── authentication structure
      ├── scopes
      ├── secret retrieval pattern
      └── runtime boundaries
      │
      ▼
New bounded workload
```

Changes should be deliberate and isolated.

Unnecessary authentication refactoring creates security and runtime risk without improving the business capability.

---

## Why this matters for AI systems

AI systems frequently cross infrastructure boundaries.

A single workflow may involve:

* GitHub for engineering
* AWS for runtime
* Google Workspace for organizational documents
* external AI model providers
* additional APIs

Without explicit identity architecture, this often leads to:

* copied API keys
* broad service accounts
* shared credentials
* unclear authority
* difficult credential rotation
* hidden trust relationships

Workload identity makes these boundaries visible.

```
Engineering environment
        │
        ▼
Deployment identity
        │
        ▼
Runtime environment
        │
        ▼
Runtime identity
        │
        ▼
External-service identity
        │
        ▼
Explicit resource authority
```

The architecture can then reason separately about each trust transition.

---

## Engineering evidence

The pattern documented here is derived from multiple private implementations rather than a standalone conceptual design.

The audited repositories demonstrate:

* GitHub Actions OIDC-based AWS deployment authentication
* AWS Lambda runtime workloads
* Google authentication through Workload Identity Federation / external-account credentials
* controlled retrieval of federation configuration
* Google Drive API access
* explicit configured document targets
* separation of deployment and runtime identity
* reuse of the same identity architecture across independently scoped agents

The public reference intentionally abstracts infrastructure-specific identifiers.

---

## Key architectural lessons

### 1. Authentication and authorization are different

Proving workload identity does not imply unrestricted resource access.

### 2. Deployment identity and runtime identity should be separate

The authority required to deploy a workload is different from the authority required to run it.

### 3. Federation configuration is not the same as a private identity key

External-account configuration can describe a trust flow without embedding a long-lived Google private key.

### 4. Prefer short-lived authorization derived from identity

Reusable static credentials should not be the default when workload federation is available.

### 5. Reuse proven security patterns

Authentication architecture should not be redesigned independently for every agent.

### 6. Fail closed

A failed identity boundary must stop execution rather than silently weaken authentication.

### 7. Cross-cloud architecture needs explicit trust boundaries

Using multiple clouds is not inherently unsafe.

Implicit identity relationships are.

---

## Technologies represented in the implementation

* AWS Lambda
* AWS IAM
* AWS Secrets Manager
* GitHub Actions
* GitHub OIDC
* Google Workload Identity Federation
* Google Security Token Service
* Google authentication libraries
* Google Drive APIs
* Node.js
* TypeScript

---

## Public reference boundary

This document intentionally does not expose:

* AWS account identifiers
* IAM role ARNs
* OIDC trust-policy identifiers
* Google Cloud project identifiers
* Workload Identity Pool identifiers
* provider identifiers
* service-account identifiers
* Google Drive file IDs
* secret names or values
* external-account configuration values
* private repository source code

The private repositories remain the implementation source of truth.

This public document exists to demonstrate the reusable identity architecture and engineering principles behind the implementations.
