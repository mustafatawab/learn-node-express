# 12. Professional Readiness Checklist

Use this checklist to judge evidence, not confidence. Reading about a skill is not the same as demonstrating it. A strong portfolio shows a working result, repeatable checks, and clear reasoning.

## Glossary

- **API contract:** agreed inputs, outputs, errors, and behavior of an interface.
- **Backpressure:** slowing or rejecting new work when a downstream system cannot keep up.
- **Authentication:** verifying who or what is making a request.
- **Authorization:** deciding what that identity may do.
- **CI/CD:** automation that checks changes and helps deliver releases.
- **Migration:** a reviewed, versioned change to database structure or data.
- **OpenAPI:** a machine-readable format for describing HTTP APIs.
- **Observability:** using logs, metrics, and traces to understand system behavior.
- **RTO/RPO:** target restoration time and tolerable data-loss window after an incident.
- **SLO:** a measurable reliability target over a defined period.
- **Threat model:** a structured view of assets, boundaries, threats, and mitigations.
- **UTC:** the global time standard commonly used to store instants without a user's local timezone.

## Evidence levels

- **0 — Not started:** cannot yet explain or demonstrate it.
- **1 — Understand:** can explain the idea and identify it in code.
- **2 — Apply:** can implement it in a guided project and verify normal behavior.
- **3 — Own:** can handle failure cases, explain tradeoffs, and maintain it without step-by-step instructions.

For a junior backend role, aim for level 2 across core topics and level 3 in areas you claim as strengths. Senior roles require deeper design, operational, and mentoring evidence.

## Competency checklist

| Area | Evidence to show |
|---|---|
| Node.js | Modules, async flow, error propagation, scripts, supported runtime selection. |
| HTTP/API | Correct method semantics, status codes, validation, pagination, compatibility, OpenAPI contract. |
| Express | Middleware order, routing, version-aware async errors, not-found and error handling. |
| Data | Schema and constraints, safe queries, transactions, indexes, migrations, backup/restore awareness. |
| Security | Authentication vs authorization, ownership checks, password hashing, session/token tradeoffs, CSRF/CORS, rate limits, secrets. |
| Quality | Readable boundaries, automated unit/integration tests, type checking, linting, repeatable setup. |
| Delivery | Version control, review-ready changes, CI, container basics, environment config, rollback plan. |
| Operations | Health/readiness, useful logs, metrics/traces concepts, timeout/retry behavior, incident learning. |
| Global users | UTC-aware timestamps, locale-neutral storage, privacy minimization, accessible errors, awareness that legal obligations differ by jurisdiction. |
| Collaboration | Small reviewable changes, useful commit/PR descriptions, written design decisions, constructive review, and clear handoff/runbook notes. |
| Engineering foundations | Git, command-line/Linux basics, debugging, core data structures/algorithms, and understanding concurrency and resource limits. |

## Security verification

Use the current [OWASP ASVS](https://asvs.owasp.org/) as a source of verifiable security requirements. Do not claim formal compliance based only on this checklist. Select relevant controls, record how each is tested, and state what remains out of scope.

## API review

Use the [OpenAPI Specification](https://spec.openapis.org/oas/) to describe the API. Review authentication, input limits, response schemas, errors, pagination, and compatibility. Verify that the description matches observed behavior.

## Final self-review

For each area, write what you built, how you verified it, a failure you handled, a tradeoff you made, and what you would improve. Ask a peer to follow setup instructions from a clean checkout and review the API contract. Fix the gaps they find before presenting the project as finished.

This checklist is a learning aid, not a hiring guarantee, certification, or legal compliance assessment.

This path focuses on Node.js backend services. It does not replace role-specific study of algorithms, a particular cloud platform, domain knowledge, or deeper database and distributed-systems work.

## What a 9.5/10 curriculum score means

These are review criteria for the documentation, not grades for a learner:

- **Beginner friendliness:** each guide states prerequisites and outcomes, defines unfamiliar terms, builds examples in small steps, provides an exercise with observable acceptance criteria, and links to next steps.
- **Accuracy:** technical claims match the cited primary references; version-dependent behavior is labeled; code samples are internally consistent; security advice states its limits.
- **Professional relevance:** learners produce evidence across API contracts, data integrity, authorization, tests, CI, release, operations, privacy, and recovery. The capstone is reproducible by another developer.

Award 9.5 only when a reviewer can verify those criteria across the complete guide set. A score is an informed assessment, not a universal certification.
