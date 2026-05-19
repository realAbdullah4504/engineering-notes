# 🧩 DB + Queue (Push Style) Pattern

---

## 1. Problem Statement

**What problem does this pattern solve?**

- What breaks without this pattern: When jobs are created via API requests but need immediate execution without polling delay, and you need both durable tracking and real-time processing
- In what situations does this problem appear: API-driven job creation, event-driven systems, user-initiated tasks, real-time processing requirements
- Why is it important at scale: Eliminates polling overhead, provides immediate job execution, maintains durable audit trail while enabling fast processing

---

## 2. Pattern Overview

**What is this pattern (in simple terms)?**

> "This pattern enables immediate job execution by writing to database for durability and pushing to queue for real-time processing."

- Core idea: Write job to DB as source of truth, immediately push to queue to trigger execution
- Key benefit: Combines durable tracking with immediate execution, no polling delay

---

## 3. When to Use (Decision Criteria)

**Use this pattern when:**
- Jobs are created via API requests (explicit event trigger)
- Real-time or near-real-time execution is required
- You need both durability and speed
- Job volume is high (polling would be inefficient)
- Event-driven architecture is feasible

**Avoid this pattern when:**
- Jobs are created by external systems without API triggers
- Job creation is unpredictable or time-based only
- Simple polling suffices for your use case
- Queue infrastructure adds unnecessary complexity

---

## 4. Architecture Representation

```
[API Request]
      ↓
[DB - Jobs Table (CREATED status)]
      ↓ (durable record)
[Queue - Job Message]
      ↓ (immediate trigger)
[Worker Pool]
      ↓
[DB - Update Job Status]
```

- **API Request**: Explicit trigger that creates job
- **DB**: Source of truth for job state, audit trail, recovery
- **Queue**: Execution signal, immediate trigger for workers
- **Worker Pool**: Consumes queue messages, executes jobs
- **DB Update**: Workers update job status after execution

---

## 5. Core Concepts

| Concept | Description |
|--------|-------------|
| Dual-write | Write to both DB (durability) and queue (execution) |
| Push-based | Workers receive jobs via queue (not polling) |
| Event-driven | API request acts as scheduler trigger |
| Source of Truth | Database is authoritative record |
| Execution Signal | Queue message triggers immediate processing |
| Recovery Layer | DB enables retry if queue message is lost |

---

## 6. Execution Flow

**Step-by-step:**

1. API request received (job creation trigger)
2. Job inserted into DB with status "CREATED"
3. Job message pushed to queue immediately
4. Worker receives queue message
5. Worker updates job status to "RUNNING"
6. Worker executes job logic
7. Worker updates job status to "COMPLETED" or "FAILED"

---

## 7. Variations of the Pattern

- **Single queue**: All jobs in one queue, workers compete
- **Priority queues**: Separate queues for different job priorities
- **Fanout**: Single job pushed to multiple queues (multiple worker types)
- **Delayed jobs**: Queue with delay before execution
- **Retry queues**: Failed jobs go to retry queue with backoff

---

## 8. Real-World Mapping

**How this pattern maps to tools:**

| Pattern Concept | Tool Example |
|----------------|-------------|
| DB | MongoDB, PostgreSQL, Redis |
| Queue | Redis, RabbitMQ, SQS, Kafka |
| Worker | BullMQ, Celery, custom workers |
| API | Express.js, FastAPI, any web framework |

---

## 9. Scaling Behavior

- **Horizontal scaling**: Add more workers, queue distributes load automatically
- **Throughput handling**: Limited by queue throughput and worker processing speed
- **Bottlenecks**: Queue performance, worker processing time, DB write performance
- **Backpressure handling**: Queue depth monitoring, worker auto-scaling, rate limiting

---

## 10. Trade-offs

| Pros | Cons |
|------|------|
| Immediate execution (no polling delay) | More complex infrastructure |
| Efficient at high job volumes | Requires queue management |
| Natural horizontal scaling | Dual-write complexity |
| Durable audit trail | Queue message loss possible |
| Event-driven architecture | Need both DB and queue |

---

## 11. Failure Scenarios

- **What if worker crashes after receiving message?**: DB still has job, can retry from DB
- **What if queue is unavailable?**: Job exists in DB, can implement fallback polling
- **What if DB is unavailable?**: Cannot create job, API returns error
- **What if queue message is lost?**: DB has record, can implement recovery mechanism

**Handling strategies:**
- Implement job timeout (reset RUNNING to PENDING)
- Use persistent queues (Redis with AOF, Kafka)
- Implement dead letter queue for failed messages
- Add recovery job to re-queue stale jobs

---

## 12. Observability

- **Metrics**: Queue depth, jobs per second, job latency, worker processing time
- **Logs**: Job creation events, queue publish events, worker consumption, status transitions
- **Alerts**: Queue depth too high, jobs stuck in CREATED/RUNNING, queue unavailable

---

## 13. Anti-Patterns

- **Only queue, no DB**: Lost jobs if queue crashes, no audit trail
- **Only DB, no queue**: Polling overhead, delayed execution
- **No job timeout**: Jobs stuck in RUNNING state forever
- **Ignoring queue failures**: Jobs never executed if queue is down

---

## 14. Integration with Your Systems

**Where you will use this pattern:**

- **Current project**: Node.js job processing system (API-driven job creation)
- **Future use**: User-initiated tasks, real-time data processing, event-driven workflows
- **With which tools**: Express.js, MongoDB, Redis/BullMQ, custom workers

---

## 15. Example Use Cases

- User-initiated file processing
- Email sending triggered by API
- Real-time data transformation
- Webhook processing
- API-driven batch jobs

---

## 16. Evolution Path (VERY IMPORTANT)

**How this pattern evolves:**

1. **Simple (Single queue + single worker)**: Basic push system, one worker processes jobs
2. **Multiple workers**: Add worker pool, queue distributes load automatically
3. **Priority queues**: Separate queues for different priorities, workers subscribe accordingly
4. **Fanout / Pub-Sub**: Single job triggers multiple worker types (e.g., processing + notification)
5. **Event-driven architecture**: Full decoupling with event sourcing, multiple consumers, complex workflows
