# Runtime Truth vs Repository Truth

| Metadata | Value |
|---|---|
| Category | Engineering pattern |
| Status | Published |
| Audience | AI system architects, platform engineers, operators, and technical reviewers |
| Implementation Basis | Deployment-readiness artifacts, structured execution evidence, observability, and controlled live verification reflected in private AI system implementations |

## Overview

Runtime Truth vs Repository Truth is an engineering pattern for distinguishing what source code claims, what infrastructure has been configured to run, what a workload actually did, and what has been verified under the intended live conditions.

These are related but different forms of evidence. Treating one as proof of another creates false confidence, especially in AI systems that cross repository, cloud, identity, document, and model-provider boundaries.

The pattern separates four evidence states:

```
Repository truth
        │
        ▼
Deployment truth
        │
        ▼
Runtime truth
        │
        ▼
Verified runtime truth
```

The sequence represents increasing operational evidence, not automatic promotion. Each boundary requires its own verification.

---

## Repository truth

Repository truth describes what can be established from version-controlled artifacts.

It can show that:

* application logic exists
* schemas and validation rules are defined
* infrastructure and workflow definitions exist
* tests cover stated behavior
* component and authority boundaries are documented
* deployment configuration has been prepared

Repository truth is essential because it makes design and implementation reviewable. It is not proof that the artifacts were deployed, that their external dependencies are accessible, or that a live execution succeeded.

```
Code + tests + configuration + documentation
                    │
                    ▼
            Repository evidence
```

A passing local test demonstrates behavior in the tested environment. It does not by itself establish production runtime behavior.

---

## Deployment truth

Deployment truth describes what has been prepared or applied in a target environment.

Evidence may establish that:

* a workload artifact was deployed
* an infrastructure update completed
* a scheduler or invocation path was configured
* deployment identity was accepted
* required runtime configuration was attached

This state is stronger than repository truth because it concerns the target environment. It still does not prove that the complete workload can execute successfully.

```
Repository artifacts
        │
        ▼
Deployment process
        │
        ▼
Target-environment configuration
```

A successful deployment means the deployment operation succeeded within its contract. It does not prove that runtime identity exchange, external document access, data eligibility, downstream invocation, or persistence will succeed.

---

## Runtime truth

Runtime truth describes what occurred during an actual workload execution.

Useful runtime evidence includes:

* execution and correlation identifiers
* boundary events and timestamps
* invocation outcomes
* schema-validation results
* persistence and freshness evidence
* downstream eligibility decisions
* explicit stop reasons
* final structured run outcomes

In the documented multi-repository pipeline, the orchestration component uses persisted-data eligibility and freshness evidence rather than assuming that an upstream success result is sufficient.

```
Workload invocation
        │
        ├── boundary evidence
        ├── validation evidence
        ├── persistence evidence
        ├── eligibility decision
        └── structured outcome
```

Runtime truth may describe a successful run, a controlled stop, or an explicit failure. All three are useful when represented accurately. A fail-closed stop caused by missing required evidence is not the same as an unknown outcome.

---

## Verified runtime truth

Verified runtime truth is runtime evidence that has been evaluated against explicit expectations for the intended execution mode and boundaries.

Verification asks whether the evidence demonstrates the required behavior, rather than merely whether an invocation occurred. Depending on the boundary under review, this can include confirmation that:

* the intended component version and configuration were exercised
* required identity and authorization boundaries worked
* valid data crossed only the permitted handoffs
* freshness and eligibility gates behaved as designed
* invalid or missing state stopped execution
* expected output was validated before persistence
* evidence is traceable to the run being evaluated

```
Structured runtime evidence
            │
            ▼
Explicit verification criteria
            │
        ┌───┴───┐
        │       │
   Insufficient  Verified
        │       │
        ▼       ▼
      STOP   Verified runtime truth
```

Verified runtime truth is deliberately scoped. Evidence for one execution path, workload, or environment should not be generalized into proof for every component or future run.

---

## Evidence does not collapse across boundaries

Each state answers a different question.

| Evidence state | Question answered | Does not prove |
|---|---|---|
| Repository truth | What has been implemented and reviewed? | That it was deployed or executed successfully |
| Deployment truth | What was prepared or applied in the target environment? | That the workload completed its runtime contract |
| Runtime truth | What happened during a specific execution? | That the outcome satisfied all verification criteria |
| Verified runtime truth | What runtime behavior has been evidenced against explicit criteria? | That all environments, paths, or future executions behave identically |

The distinctions prevent evidence from being promoted by assumption.

---

## Why this matters for AI systems

AI systems combine probabilistic processing with deterministic infrastructure and validation boundaries. They also commonly depend on external services, federated identity, scheduled invocation, structured documents, and independently deployed components.

This creates several common evidence errors:

* treating prompt or model configuration as proof of output quality
* treating tests as proof of live integration behavior
* treating deployment success as proof of runtime success
* treating one component's success as proof of pipeline success
* treating logs without correlation or structured outcomes as complete evidence
* treating an invoked model as proof that its output passed validation and persistence gates

Separating the four truths makes these gaps visible and reviewable.

---

## Chain-level truth in multi-repository systems

An individual repository can establish only its local repository evidence. It cannot independently prove the complete behavior of a chain owned across several repositories and runtime components.

```
Harvest repository truth ──┐
Runner repository truth ───┼──► Chain-level runtime evidence
Analyzer repository truth ─┘              │
                                          ▼
                              Chain-level verification
```

The orchestration boundary is therefore responsible for structured chain outcomes, eligibility decisions, and stop reasons. Local success must not silently become chain success.

---

## Identity evidence

Identity architecture illustrates the same distinction.

Repository configuration can describe GitHub OIDC, an AWS deployment role, or Google Workload Identity Federation. Deployment evidence can show that the relevant configuration was applied. Runtime evidence is still needed to show that the intended workload obtained bounded authorization and accessed the explicitly permitted resource through the expected identity path.

Authentication evidence also does not replace authorization evidence. Proving who the workload is does not prove what it was permitted to access.

---

## Fail-closed evidence handling

When required evidence is absent, the state should remain unknown or unverified.

```
Required evidence missing
          │
          ▼
Do not promote the claim
          │
          ▼
     Stop or verify
```

The system should not infer a successful state from silence, incomplete logs, deployment configuration, or a successful response from only one boundary.

This is the operational equivalent of schema validation: a claim must satisfy its evidence contract before it becomes accepted system truth.

---

## Practical review questions

When evaluating a capability, ask:

1. What does the repository prove?
2. What deployment event or target-environment state is evidenced?
3. What structured evidence came from the actual execution?
4. Which explicit criteria were used to verify that evidence?
5. What remains unknown, untested, stale, or scoped to a different environment?
6. Which authority boundary approves promotion to the next state?

These questions preserve traceability without overstating maturity.

---

## Key principles

* Code is evidence of implementation, not execution.
* Deployment is evidence of environment change, not successful workload behavior.
* Runtime events should be structured, correlated, and attributable.
* Verification requires explicit criteria.
* Local component success is not automatically chain success.
* Missing required evidence must not be interpreted optimistically.
* Operational claims should remain scoped to the evidence that supports them.

---

## Related documents

* [Multi-Repository AI Pipeline](../architecture/multi-repo-agent-pipeline.md)
* [Cross-Cloud Workload Identity](../architecture/cross-cloud-identity.md)
* [Schema First](schema-first.md)
* [Human Approval Boundaries](human-approval-boundaries.md)
