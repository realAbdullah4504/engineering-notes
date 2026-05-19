# 🧩 DB + Scheduler (Polling Style) Pattern

---

## 1. Problem Statement

**What problem does this pattern solve?**

- What breaks without this pattern: When jobs need to be executed but there's no explicit event trigger (e.g., external data sources updating randomly, no API requests creating jobs)
- In what situations does this problem appear: Crawling systems, periodic data sync, monitoring external systems, time-based job execution
- Why is it important at scale: Enables reliable job execution when job creation is external/unpredictable, provides durable job tracking without complex event systems

---

## 2. Pattern Overview

**What is this pattern (in simple terms)?**

> "This pattern enables reliable job execution for time-driven or external systems by periodically polling a database for pending jobs."

- Core idea: Store jobs in database as source of truth, use scheduler to periodically check and execute pending jobs
- Key benefit: Eliminates need for event triggers when job creation is external or time-based

---

## 3. When to Use (Decision Criteria)

**Use this pattern when:**
- Jobs are created by external systems (no API trigger)
- Job availability is time-based or unpredictable
- You need periodic checking of external resources
- Event-driven architecture is not feasible
- Job volume is moderate (polling overhead acceptable)

**Avoid this pattern when:**
- Jobs are created via API requests (use push-based instead)
- Real-time execution is critical (polling introduces delay)
- Job volume is very high (polling becomes inefficient)
- Event triggers are available

---

## 4. Architecture Representation

```
[External System / Time Trigger]
      ↓
[DB - Jobs Table (PENDING status)]
      ↓
[Scheduler (Celery Beat / Cron)]
      ↓ (periodic polling)
[Worker Pool]
      ↓
[DB - Update Job Status]
```

- **External System**: Creates jobs or makes data available
- **DB**: Source of truth for job state (PENDING, RUNNING, COMPLETED)
- **Scheduler**: Periodically checks DB for PENDING jobs
- **Worker Pool**: Executes jobs pulled from DB
- **DB Update**: Workers update job status after execution

---

## 5. Core Concepts

| Concept | Description |
|--------|-------------|
| Polling | Periodic checking of DB for pending jobs |
| Source of Truth | Database is the authoritative record of job state |
| Pull-based | Workers actively pull jobs from DB (not pushed to them) |
| Scheduler | Time-based trigger (cron, Celery beat) that initiates polling |
| Job Status Tracking | State transitions (PENDING → RUNNING → COMPLETED/FAILED) |

---

## 6. Execution Flow

**Step-by-step:**

1. External system creates job or makes data available
2. Job is inserted into DB with status "PENDING"
3. Scheduler triggers at configured interval (e.g., every 30 seconds)
4. Scheduler queries DB for jobs with "PENDING" status
5. Worker picks up pending job(s)
6. Worker updates job status to "RUNNING"
7. Worker executes job logic
8. Worker updates job status to "COMPLETED" or "FAILED"

---

## 7. Variations of the Pattern

- **Single worker polling**: One scheduler checks DB, one worker processes
- **Multiple workers with shared DB**: Scheduler checks DB, multiple workers compete for jobs
- **Priority-based polling**: Scheduler checks high-priority jobs first
- **Window-based polling**: Only poll during specific time windows

---

## 8. Real-World Mapping

**How this pattern maps to tools:**

| Pattern Concept | Tool Example |
|----------------|-------------|
| DB | MongoDB, PostgreSQL, Redis |
| Scheduler | Celery Beat, cron, APScheduler |
| Worker | Celery workers, custom worker processes |
| Polling Logic | SQL queries, ORM queries |

---

## 9. Scaling Behavior

- **Horizontal scaling**: Add more workers to process jobs in parallel
- **Throughput handling**: Limited by polling interval and DB query performance
- **Bottlenecks**: DB query performance, polling frequency, worker concurrency
- **Backpressure handling**: Workers can limit concurrent jobs, scheduler can adjust polling interval

---

## 10. Trade-offs

| Pros | Cons |
|------|------|
| Simple to implement | Polling introduces delay |
| No event infrastructure needed | Wasteful if no jobs available |
| Durable job tracking | Scaling limited by DB performance |
| Easy to audit and retry | Not real-time |
| Works with external systems | Polling overhead at scale |

---

## 11. Failure Scenarios

- **What if scheduler crashes?**: Jobs remain in PENDING state, resume when scheduler restarts
- **What if worker crashes?**: Job stuck in RUNNING state, need timeout/recovery mechanism
- **What if DB is unavailable?**: Scheduler fails to query, jobs not processed
- **What if polling interval is too long?**: Jobs wait longer before execution

**Handling strategies:**
- Implement job timeout (reset RUNNING to PENDING after timeout)
- Use DB transactions for atomic status updates
- Monitor scheduler health
- Adjust polling interval based on load

---

## 12. Observability

- **Metrics**: Polling frequency, jobs processed per interval, job age, worker queue depth
- **Logs**: Scheduler polling events, job status transitions, worker execution logs
- **Alerts**: Scheduler not polling, jobs stuck in PENDING/RUNNING too long

---

## 13. Anti-Patterns

- **Polling too frequently**: Excessive DB load, diminishing returns
- **No job timeout**: Jobs stuck in RUNNING state forever
- **Single point of failure**: Scheduler crash stops all job processing
- **No priority handling**: Critical jobs wait behind non-critical ones

---

## 14. Integration with Your Systems

**Where you will use this pattern:**

- **Current project**: Celery crawler system (web scraping, checking external portals)
- **Future use**: Periodic data sync, monitoring external APIs, time-based batch processing
- **With which tools**: Celery Beat, MongoDB, custom workers

---

## 15. Example Use Cases

- Web scraping (crawling new listings that appear over time)
- Periodic data synchronization with external systems
- Monitoring external APIs for changes
- Batch processing at scheduled intervals
- Cleanup jobs (e.g., delete old records)

---

## 16. Evolution Path (VERY IMPORTANT)

**How this pattern evolves:**

1. **Simple (Single scheduler + single worker)**: Basic polling, one worker processes jobs sequentially
2. **Multiple workers**: Add worker pool for parallel processing, shared DB coordination
3. **Priority queues**: Implement job priorities, critical jobs processed first
4. **Hybrid approach**: Add queue for immediate execution when events available, keep polling for external jobs
5. **Event-driven migration**: Move to push-based pattern when event triggers become available
