# Capacity and cost assumptions

This document is an assumption-based planning boundary, not a claim that the local demo supports one million concurrent users.

## Current verified scope

- Registered users: fictional seed accounts plus local development accounts; no production user population.
- Daily active users: not measured.
- Peak concurrency: not measured.
- Order rate: not measured.
- Tracking connections: short-polling customer tracking, not a measured realtime fanout workload.
- Local stack: one backend container, Postgres/PostGIS, Redis, NATS, and MinIO on the developer machine.
- Evidence: backend and frontend CI are green; Docker health and route registration pass; no load test or reachable deployment is claimed.

## Affordable demo ceiling

For the intended learning/showcase use, keep the development environment bounded to approximately 50 initial users, low double-digit concurrent sessions, and a small number of simultaneous demo orders. These are operating limits to avoid accidental paid usage, not measured service-level guarantees.

The current bottlenecks are the single backend process, Postgres connection pool, polling traffic, and local machine resources. The mock worker uses persisted due timestamps and should remain restart-safe, but its throughput has not been load-tested.

## One-million-registered-user growth path

One million registered accounts mainly affects identity storage, indexes, retention, and asynchronous communication. Before pursuing that target, measure a representative deployment for:

1. authentication requests per second and OTP provider quotas;
2. restaurant discovery reads and cache hit rate;
3. order writes per second and idempotency contention;
4. payment webhook/event rate and reconciliation backlog;
5. active tracking sessions, poll frequency, and delivery state fanout;
6. database CPU, storage growth, connection saturation, Redis memory, and queue lag.

The staged path is: managed Postgres with read replicas only after measurements, bounded Redis caching, durable event/outbox processing, independently scalable workers, rate limits/backpressure, and a measured realtime delivery channel. Kubernetes and additional brokers are not justified by registration count alone.

## Cost boundary

No paid resource was purchased or silently enabled. Local development uses Docker images and mock/dev providers. The existing infra repository describes optional Railway, Supabase, Cloudflare R2, Vercel, and HCP Terraform resources, but cloud cost, credentials, deployment reachability, backups, and restore evidence remain user-owned setup work.

Before any shared deployment, set an explicit monthly budget, resource ceilings, data retention, backup policy, TLS/access controls, and alert thresholds. Do not apply Terraform or create billable provider resources without user approval and a clean plan.
