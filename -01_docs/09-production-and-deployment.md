# 9. Production and Deployment

Deployment puts an app where real users and real failures matter. A successful local run is one step; configuration, data, security, monitoring, and rollback also need a plan.

## Learning outcomes

You should be able to describe a release from commit to running service, identify environment-specific configuration, and explain how to recover from an unsuccessful deployment.

**Prerequisites:** Guides 5, 6, and 8.  
**Key terms:** image (packaged application/runtime template), container (running isolated process), CI (automated checks on changes), CD (automated delivery/release), readiness (safe to receive traffic).

## Configuration and health

Use separate settings for development, test, and production. Provide secrets through a secret manager or hosting platform. Use HTTPS, restrict browser origins, and trust proxy headers only from known infrastructure.

Fail fast when required configuration is invalid. A liveness check indicates whether the process runs; readiness indicates whether it can serve work, including required dependencies. A load balancer or orchestrator can use readiness to stop sending traffic to an unready instance. Do not expose secrets or internal topology in health responses.

## Database and releases

Use a least-privilege database account, protect connection strings, and plan backups and restores. Review migrations before applying them. For risky schema changes, use expand-and-contract: add compatible schema, deploy compatible code, migrate data, then remove obsolete schema later.

- Run under a platform or process manager that restarts failed processes.
- Handle termination signals and close listeners and database connections.
- Build from the lockfile; keep development-only packages out of runtime where appropriate.
- Use CI for type checks and tests before release.
- Review dependency vulnerability reports and update through reviewed changes; do not apply bulk fixes blindly.
- Identify deployed releases in logs and have a rollback plan.

CI should use a clean, lockfile-based install, build the same artifact type that will be deployed, and run the project's checks. Protect deployment credentials with the CI platform's secret and environment controls. Pin third-party workflow actions according to your organization's policy and review updates.

A minimal GitHub Actions check for the capstone could look like this (create these scripts first; review action versions when adopting the file):

~~~yaml
name: checks
on: [push, pull_request]

jobs:
  verify:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: actions/setup-node@v7
        with:
          node-version-file: .nvmrc
          cache: npm
      - run: npm ci
      - run: npm run lint --if-present
      - run: npm run typecheck --if-present
      - run: npm run build --if-present
      - run: npm test
~~~

Keep the supported runtime in `.nvmrc` and the version range in `package.json` aligned. Do not put production secrets in a pull-request workflow.

Containerization is one common deployment option, not a requirement for every role or platform. If using Docker, learn multi-stage builds, small maintained base images, non-root runtime users, health checks, signal handling, and secret injection. Never bake credentials into an image layer.

Collect only operational logs and personal data needed. Restrict access to production credentials and customer data.

## Practice

Write a release checklist for `07_todo_with_neon`: environment variables, migration review, backup/restore plan, health check, log access, and rollback. Never use real production credentials in a learning environment.

**Check your work:** a clean build uses the lockfile; the service starts with production configuration; a readiness failure keeps it out of rotation; a release can be identified and rolled back; a database restore has been rehearsed.

**Common mistake:** assuming that a successful container build proves the service is deployable. Verify runtime permissions, configuration, health, database reachability, shutdown, and logs.

For systems with recovery targets, agree on recovery time objective (RTO: how long service restoration may take) and recovery point objective (RPO: how much recent data loss is tolerable). Test backup restoration against those targets; a backup that has never been restored is unverified.

## Further reading

- [Express: Production security](https://expressjs.com/en/advanced/best-practice-security.html)
- [Express: Performance and reliability](https://expressjs.com/en/advanced/best-practice-performance.html)
- [Twelve-Factor App: Config](https://12factor.net/config)
- [Docker: Node.js guide](https://docs.docker.com/guides/nodejs)
- [GitHub Actions: Build and test Node.js](https://docs.github.com/en/actions/tutorials/build-and-test-code/nodejs)
