# 🧩 ID-Based Processing with Job Tracking Pattern

---

## 1. Problem Statement

**What problem does this pattern solve?**

- **What breaks without this pattern:** Processing entire objects in queues causes duplicate processing, race conditions, stale data issues, and no recovery mechanism when workers crash.
- **In what situations does this problem appear:** High-volume asynchronous task processing, long-running background jobs, distributed worker systems, data synchronization pipelines.
- **Why is it important at scale:** Without job tracking, systems cannot guarantee exactly-once processing, cannot recover from failures, and cannot provide observability into processing status.

---

## 2. Pattern Overview

**What is this pattern (in simple terms)?**

> "This pattern enables reliable asynchronous processing by separating ID transmission from data fetching, with a centralized job registry that tracks every job through its lifecycle."

- **Core idea:** Send only identifiers to workers, not full objects; register jobs in a centralized collection before queuing; workers atomically claim jobs before processing.
- **Key benefit:** Guarantees exactly-once processing, enables recovery from crashes, provides full observability, and eliminates race conditions.

---

## 3. When to Use (Decision Criteria)

**Use this pattern when:**
- Processing high-volume data asynchronously (1000+ jobs/day)
- Multiple workers processing the same resource types
- Need guaranteed exactly-once processing semantics
- Jobs take >5 seconds and need failure recovery
- System requires observability into job status
- Data freshness is critical (avoid stale object snapshots)

**Avoid this pattern when:**
- Processing <100 jobs/day (overhead not justified)
- Jobs are idempotent by nature and duplicates harmless
- Single-worker setup with no crash recovery needed
- Processing time <1 second (simpler approach suffices)

---

## 4. Architecture Representation

```
[Database]
    ↓ (query: IDs + metadata)
[Dispatcher Service]
    ↓ (1. create_job PENDING → 2. queue job_id)
[Job Queue (Redis/RabbitMQ)]
    ↓ (job_id only)
[Worker 1]  [Worker 2]  [Worker N]
    ↓           ↓           ↓ (claim_job PENDING→PROCESSING)
[Database] ← (fresh fetch by ID)
    ↓ (process)
    ↓ (mark_job_done)
[Job Status: DONE]
```

**Flow explanation:**
1. **Dispatcher** queries DB for tender IDs, creates PENDING jobs in tender_jobs collection (atomically prevents duplicates via unique constraint)
2. **Queue** receives only tender IDs (not full objects), keeping payloads minimal
3. **Worker** pulls job ID from queue, atomically transitions job from PENDING → PROCESSING (claiming it)
4. **Worker** fetches fresh tender data from database using the ID (ensures no stale data)
5. **Worker** processes tender and marks job DONE (or FAILED on error)

---

## 5. Core Concepts

| Concept | Description |
|--------|-------------|
| **Job Registry** | Centralized collection (tender_jobs) that tracks every job's tender_id, stage, status, worker_id, attempts, timestamps |
| **Atomic Claim** | Single MongoDB update that transitions job from PENDING → PROCESSING only if still PENDING, ensuring exactly one worker succeeds |
| **ID-Based Payload** | Queue carries only tender_id, not full tender object; worker fetches fresh data to avoid stale state |
| **Job Stages** | Logical processing phases (LISTING, DETAIL, CLASSIFY, SCORE) allowing multiple independent jobs per tender |
| **Status Lifecycle** | PENDING → PROCESSING → DONE/FAILED, with explicit transitions tracked in DB |
| **Worker Identity** | Each worker instance gets unique worker_id (e.g., worker_<celery_task_id>) for ownership tracking |
| **Stuck Job Recovery** | Jobs stuck in PROCESSING > timeout (e.g., 30min) can be reset to PENDING by monitoring task |

---

## 6. Execution Flow

**Step-by-step:**

1. **Query Phase** — Dispatcher queries database for tenders needing processing (e.g., `find({status: "new"})`), extracts tender_ids and portal names.
2. **Job Registration Phase** — For each tender_id, call `create_job_if_not_exists(tender_id, stage)`. Unique constraint prevents duplicates; function returns bool indicating if job was newly created.
3. **Queue Preparation Phase** — Query tender_jobs collection for all jobs with status=PENDING and matching stage; collect their tender_ids.
4. **Queue Dispatch Phase** — Group all `process_tender_details_task.s(tender_id, ...)` into a single Celery chord/group; call `apply_async()`.
5. **Worker Receives Job** — Celery worker receives tender_id payload from queue.
6. **Job Claim Phase** — Worker calls `claim_job(tender_id, stage, worker_id)`. This atomically updates one document where `tender_id=X, stage=Y, status="PENDING"` to `status="PROCESSING", worker_id=Z`. Returns True if successful, False if another worker already claimed it.
7. **Data Fetch Phase** — If claim succeeded, worker fetches fresh tender from database using `get_tender_by_id(tender_id)`.
8. **Processing Phase** — Worker performs business logic: download tender documents, extract details, classify, score.
9. **Completion Phase** — On success: `mark_job_done(tender_id, stage, worker_id)` sets status=DONE, updated_at=now. On exception: `mark_job_failed(...)` sets status=FAILED, increments attempts, stores error in job doc if needed.
10. **Retry Logic** — Celery automatically retries failed tasks per task's `max_retries` and `default_retry_delay`. Job status remains PROCESSING during retry; after max retries, status becomes FAILED.
11. **Monitoring Phase** — Administrators query tender_jobs by status to see pending/processing/failed counts; can manually reset failed jobs or re-queue.

---

## 7. Variations of the Pattern

- **Simple Single-Stage** — One job per tender (e.g., just DETAIL stage). Minimal implementation with only one stage constant.
- **Multi-Stage Pipeline** — Multiple independent stages per tender (LISTING → DETAIL → CLASSIFY → SCORE). Each stage tracked separately; allows parallelization across stages and easy restart from failed stage.
- **Priority Queue Integration** — Add `priority` field to tender_jobs (1-10). Workers check priority field and process high-priority tenders first; dispatcher sets priority based on deadline or value.
- **Batch Job Grouping** — Create one parent_job_id that groups multiple tender_ids; track batch-level status in addition to individual job statuses. Useful for bulk operations needing atomic success/failure semantics.
- **Distributed Worker Pools** — Workers on different machines/containers all claim from same queue. Atomic DB claiming prevents cross-machine race conditions.
- **Delayed Job Scheduling** — Add `scheduled_at` field to tender_jobs; dispatcher sets future time; monitoring task checks for scheduled jobs and transitions them to PENDING when due.
- **Chunked Processing** — For extremely large datasets, dispatcher creates jobs in chunks (e.g., 1000 IDs per job) rather than one-per-ID; worker processes chunk and marks chunk-level done.

---

## 8. Real-World Mapping

**How this pattern maps to tools:**

| Pattern Concept | Tool Example |
|----------------|-------------|
| Job Registry Collection | MongoDB `tender_jobs`, PostgreSQL `job_queue` table, Redis Sorted Set |
| Atomic Claim Operation | MongoDB `update_one({..., status:"PENDING"}, {$set:{status:"PROCESSING"}})`, PostgreSQL `UPDATE ... WHERE status='PENDING' RETURNING *` |
| Queue Broker | Redis (Celery), RabbitMQ, AWS SQS, Google Pub/Sub |
| Worker Framework | Celery (Python), Sidekiq (Ruby), BullMQ (Node.js), Hangfire (.NET) |
| Worker Identity | Celery's `task.request.id`, AWS Lambda `requestId`, custom UUID |
| Unique Constraint | MongoDB unique index, PostgreSQL unique constraint, database-level constraint |
| Monitoring Queries | MongoDB find({status:"PENDING"}), SQL `SELECT * FROM jobs WHERE status = 'FAILED'` |
| Stuck Job Detection | Periodic Celery beat task, cron job querying `updated_at < now()-30min AND status="PROCESSING"` |

---

## 9. Scaling Behavior

- **Horizontal scaling:** Workers scale linearly; more workers → higher throughput; DB indexing on `(tender_id, stage)` and `status` keeps claim operations fast even with millions of jobs.
- **Throughput handling:** MongoDB can handle ~10K atomic claims/sec on moderate hardware; queue broker (Redis/RabbitMQ) handles ~50K msg/sec; bottleneck usually database I/O during fresh fetch phase.
- **Bottlenecks:** Database connection pool (workers fetching fresh data); unique index contention if many tenders share same tender_id prefix; queue broker memory if backlog huge.
- **Backpressure handling:** If queue grows, slow down dispatcher (monitor queue depth); use Celery worker `--autoscale` to spawn more workers on backlog; implement rate limiting per portal.

---

## 10. Trade-offs

| Pros | Cons |
|------|------|
| Exactly-once processing guarantee | Added complexity (job registry, claiming logic) |
| Fresh data on every worker (no stale snapshots) | Two DB round-trips per job (create + claim) |
| Full observability (query any job status) | Extra storage for job documents (~100 bytes each) |
| Automatic duplicate prevention (unique index) | Monitoring required (stuck jobs, failed queues) |
| Crash recovery (reset stuck jobs) | Slightly higher latency vs direct queue |
| Supports retry/backoff naturally | Requires discipline to always use job lifecycle |

---

## 11. Failure Scenarios

- **What if worker crashes while PROCESSING?** Job remains in PROCESSING status with worker_id set. Monitoring task `reset_stuck_jobs()` finds jobs where `updated_at < now()-30min AND status="PROCESSING"` and resets them to PENDING; next worker will claim and retry.
- **What if queue is overloaded?** Broker memory spikes; dispatcher should monitor queue length and throttle; enable worker autoscaling; consider priority queues for critical tenders.
- **What if message is lost?** Broker confirms delivery; if message truly lost, job remains PENDING and will be picked up by next dispatcher run (dispatcher queries all PENDING jobs, re-queues missing ones).
- **What if database connection fails during fetch?** Celery task retries automatically; job stays PROCESSING; after max_retries, job marked FAILED; admin can manually reset to PENDING.
- **What if job claim race condition occurs?** Atomic `update_one` with filter `status="PENDING"` ensures only one worker succeeds; loser receives False and exits gracefully (no processing).

**Handling strategies:**
- **Retries** — Celery exponential backoff (default: 3 retries, 60s delay).
- **DLQ (Dead Letter Queue)** — Jobs failing after max retries go to FAILED status; admin reviews via `get_jobs_by_status(FAILED)`.
- **Idempotency** — Processing logic should be idempotent; job claiming prevents double runs but defensive idempotency adds safety.
- **Monitoring alerts** — Alert on: (a) PENDING > 1000, (b) PROCESSING > 100, (c) FAILED > 10 in last hour.

---

## 12. Observability

- **Metrics:**
  - `jobs_pending_total` (Gauge) — count of PENDING jobs
  - `jobs_processing_total` (Gauge) — count of PROCESSING jobs by worker_id
  - `jobs_failed_total` (Counter) — total FAILED jobs
  - `jobs_done_total` (Counter) — total DONE jobs
  - `job_claim_duration_seconds` (Histogram) — time from PENDING to PROCESSING
  - `job_processing_duration_seconds` (Histogram) — time from PROCESSING to DONE

- **Logs:**
  - Worker logs: `"Claimed job tender_123 stage DETAIL worker_abc"`
  - Dispatcher logs: `"Created 5 new jobs, skipped 2 duplicates"`
  - Reset task logs: `"Reset 12 stuck jobs from PROCESSING to PENDING"`
  - Structured JSON logs with tender_id, stage, worker_id for aggregation.

- **Alerts:**
  - Pending jobs > 10,000 for >10min
  - Processing jobs > 100 for >30min (possible stuck workers)
  - Failed jobs rate > 10/min
  - Queue depth (Redis list length) > 50,000

---

## 13. Anti-Patterns

- **Dispatching without job creation** — Sending tender_id directly to queue without first creating tender_jobs doc leads to duplicates; ALWAYS create job BEFORE queueing.
- **Processing without claiming** — Worker fetching tender and processing before claiming allows race conditions; ALWAYS claim first.
- **Updating job status without atomic filter** — Updating job status with `tender_id` only (no `status="PROCESSING"` filter) can overwrite another worker's claim; ALWAYS include status in filter.
- **Not using unique constraint** — Relying solely on application logic for deduplication; ALWAYS enforce at DB level.
- **Fetching tender before claiming** — Reading DB before claiming results in wasted I/O if claim fails; ALWAYS claim before any DB fetch.
- **Missing timestamp updates** — Not touching `updated_at` on status changes makes stuck job detection impossible; ALWAYS update timestamps.
- **Hard-coded worker IDs** — Using static worker names prevents identifying specific task instances; ALWAYS use dynamic worker_id (Celery task.request.id).
- **Not resetting stuck jobs** — Letting PROCESSING jobs accumulate forever; ALWAYS schedule periodic reset_stuck_jobs task.

---

## 14. Integration with Your Systems

**Where you will use this pattern:**
- **Current project:** Tender details processing pipeline — details_task_dispatcher enqueues tender IDs; process_tender_details_task claims and processes each tender.
- **Future use:** Tender classification stage, tender scoring stage, document download stage — all can follow same pattern with separate stages.
- **With which tools:** MongoDB (tender_jobs collection), Celery (queue & workers), Redis (broker), FastAPI (dispatcher endpoint).

---

## 15. Example Use Cases

- **Background job processing** — Asynchronous tender detail enrichment, document downloads, PDF parsing.
- **Email sending** — Send email jobs tracked individually; retry failed sends; avoid duplicate emails.
- **Video processing** — Transcoding jobs claimed by workers; track processing status; resume failed uploads.
- **Data pipelines** — ETL jobs pulling from source DB, transforming, loading to warehouse with exact-once guarantees.

---

## 16. Evolution Path (VERY IMPORTANT)

**How this pattern evolves:**

1. **Simple (Sync processing)** — Direct function calls; no queue; blocking; no tracking (for trivial <1s jobs).
2. **Add queue** — Basic task queue (Celery) enqueuing tender objects; no job registry; duplicates possible; no recovery.
3. **Add ID-based payload** — Send only tender_id instead of full object; worker fetches fresh data; reduces network payload; still no duplicates prevention.
4. **Add job registry (tender_jobs collection)** — Create job document before queueing; unique constraint prevents duplicates; full status tracking.
5. **Add atomic claiming** — Worker updates job from PENDING→PROCESSING with filter; prevents race conditions; multiple workers safe.
6. **Add recovery mechanisms** — Stuck job detection and reset; failed job retry; manual re-queue; monitoring dashboard.
7. **Add multi-stage** — Split processing into LISTING→DETAIL→CLASSIFY→SCORE stages; independent job tracking per stage; partial retry possible.
8. **Add priority & scheduling** — Priority field on jobs; scheduler sets future run times; worker priority ordering; SLA tracking.
9. **Move to event-driven** — Jobs emit events on status change; other services subscribe; complete decoupling; event sourcing possible.

---

## File Organization

This pattern is saved as: `patterns/id-based-processing-with-job-tracking.md`
