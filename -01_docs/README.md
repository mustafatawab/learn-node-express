# Backend Engineering Learning Path

This curriculum takes you from JavaScript and HTTP basics to planning, building, testing, and operating a small backend service. Work through the guides in order and build the capstone as you go.

The goal is to develop evidence of skill, not to promise expertise from reading alone. “International market readiness” here means skills commonly expected in backend teams: reliable APIs, secure data handling, automated verification, collaboration, and operating software. Employers and regulations differ by role and country; verify requirements for the job and users you serve.

## Before you begin

You need basic JavaScript: variables, functions, objects, arrays, and conditionals. You will learn promises and async/await in Guide 1. Be able to use a terminal and edit files. You do not need prior Express or database experience.

Install a supported Node.js LTS release and npm. Check [Node.js releases](https://nodejs.org/en/about/previous-releases) before starting. Most folders are independent projects; read their package.json and local instructions before running commands.

## Sequence

| # | Guide | You will be able to… |
|---|---|---|
| 1 | [Node.js foundations](01-nodejs-foundations.md) | Run a Node program, use modules, await I/O, and diagnose basic failures. |
| 2 | [HTTP and web fundamentals](02-http-and-web-fundamentals.md) | Explain requests, responses, methods, status codes, cookies, and CORS. |
| 3 | [Express fundamentals](03-express-fundamentals.md) | Build an Express server with routes, middleware, and safe error responses. |
| 4 | [API design and validation](04-api-design-and-validation.md) | Define a versionable API contract and validate untrusted input. |
| 5 | [Errors, logging, and configuration](05-errors-logging-and-configuration.md) | Handle failures and secrets consistently across environments. |
| 6 | [Databases and data modeling](06-databases-and-data-modeling.md) | Model relationships, query safely, and evolve schemas with migrations. |
| 7 | [Authentication and security](07-authentication-and-security.md) | Explain identity, permissions, common web threats, and defense layers. |
| 8 | [Testing and code structure](08-testing-and-code-structure.md) | Test behavior at useful boundaries and keep responsibilities clear. |
| 9 | [Production and deployment](09-production-and-deployment.md) | Package, configure, release, monitor, and recover a service. |
| 10 | [Scalability and operations](10-scalability-and-operations.md) | Measure bottlenecks and reason about resilience and scale. |
| 11 | [Capstone: Task API](11-capstone-project.md) | Deliver a reviewable backend with an API contract, tests, and runbook. |
| 12 | [Professional readiness checklist](12-professional-readiness-checklist.md) | Assess portfolio evidence and identify gaps. |

## Repository projects

- `01_starting` and `02_filehandler`: Node.js, files, paths, and promises.
- `03_express`, `04_routes`, and `05_middleware`: Express servers, routes, and middleware.
- `06_simple-crud`: a small CRUD API.
- `07_todo_with_neon`: TypeScript, Express, Prisma, Postgres, authentication, and todos.
- `08_mongodb`: TypeScript, Express, Mongoose, and a modular API.

Some folders use Express 4 and others Express 5. Async error behavior differs by major version; each guide labels version-sensitive advice. Treat examples as learning material to review, not audited production software.

## How to use each guide

1. Read the learning outcomes and key terms before the explanation.
2. Follow the example, then explain it in your own words.
3. Complete the exercise and check every acceptance item.
4. Compare your result with the references and note version differences.
5. Apply the concept to the capstone; keep work in version control.

If a term is new, use the linked glossary in Guide 12. If an official source differs from a sample, prefer the official documentation for the version you use and record the discrepancy.
