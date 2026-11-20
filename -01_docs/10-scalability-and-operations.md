# 10. Scalability and Operations

Scalability means handling changing load while meeting reliability and latency needs. Measure first; extra infrastructure can add cost and complexity without fixing the bottleneck.

## Learning outcomes

You should be able to interpret basic service metrics, find a bottleneck using evidence, and explain the risks of retries, caches, and horizontal scaling.

**Prerequisites:** Guides 6, 8, and 9.  
**Key terms:** latency (time to complete work), throughput (work completed per time), SLI (measured service behavior), SLO (target for that behavior), saturation (resource nearing capacity).

## Measure the service

Track request rate, latency percentiles, error rate, resource use, database connections, and queue depth where relevant. Logs explain events; metrics show trends; traces connect work across services. Avoid secrets and unnecessary personal data in telemetry.

OpenTelemetry provides APIs and SDKs for traces and metrics in Node.js. Decide which operational questions you need answered before adding instrumentation.

Define a small service-level objective (SLO), for example a target proportion of successful requests under a stated latency for a defined period. Choose the measurement and window deliberately; an SLO without an actionable response is just a number. Never claim a production SLO from an unrepresentative local benchmark.

## Common tools

- **Caching:** define freshness, invalidation, and behavior when the cache is unavailable.
- **Indexes and query tuning:** inspect slow queries before changing schemas.
- **Queues and background jobs:** make jobs idempotent and define retry, timeout, and dead-letter behavior.
- **Connection pools:** size them for the database and deployment; too many app instances can exhaust connections.
- **Horizontal scaling:** stateless instances can share traffic, but sessions, scheduled jobs, and files need shared storage or coordination.

Set timeouts for network calls. Limit retries and use backoff where appropriate; repeated retries can worsen an outage. Design for partial failure: a database, email provider, or cache may fail while the process remains alive.

A dead-letter queue holds jobs that exceeded the normal retry policy for later inspection. Backoff increases the wait between retries; jitter adds randomness so many clients do not retry together. Apply admission control or return an overload response when the service cannot safely accept more work.

Load-test a representative workload before changing architecture. Record test data size, concurrency, environment, p95/p99 latency, error rate, and resource use. Do not compare results from different environments as if they were equivalent. Start with a modular service; split it into services only when ownership, scaling, or reliability needs justify the operational cost.

## Learn from incidents

Record impact, timeline, detection, contributing causes, and follow-up actions. Improve monitoring, capacity, and recovery. Practice restoring backups and rolling back releases before an emergency.

## Practice

Measure a slow endpoint, inspect its data access, and compare before and after one targeted change. Record evidence, tradeoffs, and how you would detect a regression.

**Check your work:** capture a baseline; change one variable; compare latency and error rate under the same workload; document whether the result supports the change.

**Common mistake:** optimizing a single fast request or scaling app instances when the database is the actual bottleneck.

## Further reading

- [OpenTelemetry JavaScript and Node.js](https://opentelemetry.io/docs/languages/js/)
- [Express: Performance and reliability](https://expressjs.com/en/advanced/best-practice-performance.html)
- [Google SRE: Monitoring distributed systems](https://sre.google/sre-book/monitoring-distributed-systems/)
