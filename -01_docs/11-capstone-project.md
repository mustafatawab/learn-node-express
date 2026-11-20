# 11. Capstone: Task API

Build one small service throughout the learning path. The goal is a reviewable portfolio project that shows how you make backend decisions, not a large feature list.

**Prerequisites:** Guides 1–10.  
**Estimated effort:** plan for multiple short sessions; pause at each milestone to review the contract, code, and evidence.  
**Success means:** another developer can understand, run, verify, and operate your project from the repository instructions.

## Product brief

Build a task API for multiple users. A user can register and sign in, create tasks, list only their own tasks, update a task, and delete a task. A task has an ID, title, optional description, completion state, owner, and creation/update timestamps.

Use the stack represented in `07_todo_with_neon`: Node.js, TypeScript, Express, Zod, Prisma, and PostgreSQL. You may use a different stack if you document tradeoffs and provide equivalent evidence. Do not copy authentication code without reviewing it against Guide 7.

## Contract before implementation

Start with an OpenAPI document. At minimum describe:

- `POST /api/v1/auth/register` and `POST /api/v1/auth/login`.
- `GET /api/v1/tasks` with bounded pagination and stable ordering.
- `POST /api/v1/tasks`, `GET /api/v1/tasks/{taskId}`, `PATCH /api/v1/tasks/{taskId}`, and `DELETE /api/v1/tasks/{taskId}`.
- Request and response schemas, authentication, status codes, and a consistent error format.

Document validation limits, pagination behavior, whether deletion returns 204, and what clients see when an item is missing or belongs to someone else. Prefer not to reveal another user's resource existence.

Use one consistent error envelope and document examples. Define pagination deterministically (for example, cursor plus stable ordering) and set a maximum page size. Decide how dates are serialized and keep timestamps timezone-unambiguous. Link each endpoint to its authorization rule and test case.

## Milestones

1. **Foundation:** create the app, scripts, environment validation, and health/readiness endpoints.
2. **Contract:** write and review OpenAPI before implementing routes. Keep the contract and implementation in sync.
3. **Data:** model users and tasks, enforce unique email and ownership constraints, add migrations, and seed disposable development data.
4. **API:** implement route, validation, service, and persistence boundaries. Use transactions where a multi-step operation must be atomic.
5. **Identity and safety:** implement password hashing and a session strategy; authorize every task operation by owner; add rate and request-size limits.
6. **Verification:** add unit and integration tests for success and failure paths, including cross-user access and database constraint conflicts.
7. **Release:** add CI, a production build, container support, deployment instructions, health checks, structured logs, and a rollback/restore runbook.

## Acceptance criteria

- A new contributor can install, configure, migrate, seed, run, and verify the project using only its README.
- No real credentials are committed. Startup fails clearly when required configuration is missing.
- Every endpoint is described in OpenAPI and has validation, documented responses, and tests.
- A user cannot read, update, or delete another user's task, even by changing an ID.
- Passwords are never stored in plaintext. Secrets, auth cookies/tokens, and sensitive personal data are not logged.
- Schema changes are versioned; tests use isolated data and can be repeated.
- CI runs formatting or linting, type checks, build, and tests on a supported Node version.
- The supported Node version is recorded (for example in `.nvmrc`) and kept aligned with CI and the package's engine policy.
- The production image runs as a non-root user where supported, uses a supported base image strategy, and receives secrets at runtime.
- Timestamps use an unambiguous format (typically UTC ISO 8601). Do not assume users share a timezone, language, address format, or currency.
- The runbook covers health checks, logs, migration review, backup restoration, and rollback.

## Suggested repository layout

Keep the layout small enough to understand:

~~~text
src/
  app.ts             # middleware and route mounting; no listen call
  server.ts          # startup, listen, and shutdown
  modules/task/      # router, schema, controller, service, repository
  modules/auth/
  shared/config/
  shared/errors/
  shared/database/
prisma/
  schema.prisma
  migrations/
tests/
docs/
  openapi.yaml
  threat-model.md
  runbook.md
~~~

Adapt names to the chosen stack. Separate app construction from process startup so integration tests can create the app without binding a port.

## Portfolio deliverables

Include a project README, OpenAPI file, architecture sketch, data model and migrations, test command and results, CI workflow, container configuration, sample environment file with placeholders, threat model, short runbook, and at least one short architecture decision record. Explain three design choices and one limitation to address next.

## Review questions

Can a reviewer run it from a clean checkout? Can they discover the API without reading implementation code? Is there evidence that authorization and failure behavior were tested? Can they understand how to operate and recover the service?

## Review rubric

Score each acceptance item as **0 (absent), 1 (partial/manual), or 2 (complete and repeatably verified)**. A strong submission has no zeroes in security, API behavior, data integrity, or reproducible setup. Include a short evidence link or command for every score of 2.

## References

- [OpenAPI Specification](https://spec.openapis.org/oas/)
- [Docker: Node.js guide](https://docs.docker.com/guides/nodejs/)
- [GitHub Actions: Build and test Node.js](https://docs.github.com/en/actions/tutorials/build-and-test-code/nodejs)
