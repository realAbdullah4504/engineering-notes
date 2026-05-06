# Celery

---

## 1. Problem Statement

**What problem does this tool solve in systems?**

- What gap exists without this tool: Synchronous task processing blocks request handling, causes timeouts, and limits system scalability
- Why does it matter in scalable systems: Enables horizontal scaling of background work, prevents system degradation under load, allows fault-tolerant task processing
- What type of systems typically need this: Web applications with long-running tasks, data processing pipelines, notification systems, file processing workflows

---

## 2. When to Use (Decision Criteria)

**Use this tool when:**
- Processing tasks take longer than 30 seconds
- Need to handle >1000 tasks per hour
- Require task retry logic and failure handling
- Need task prioritization and scheduling
- Must process tasks concurrently across multiple workers
- Need periodic task execution (cron-like functionality)

**Do NOT use when:**
- Simple synchronous processing suffices
- Task volume <100 per hour
- No failure recovery needed
- Tasks complete within HTTP request timeout
- Single-threaded processing is acceptable

---

## 3. Core Concepts

| Concept | Description |
|--------|-------------|
| Broker | Message transport (Redis/RabbitMQ) that stores task messages |
| Worker | Process that consumes tasks from broker and executes them |
| Task | Python function decorated with @app.task that can be executed asynchronously |
| Queue | Logical grouping of tasks, enables task routing and prioritization |
| Beat | Scheduler for periodic tasks, similar to cron |
| Result Backend | Storage for task results and status (Redis/Database) |

---

## 4. Architecture Role (System Design View)

**Where does this tool sit in the system?**

[Client/API] → [Service] → [Celery App] → [Broker] → [Worker] → [Database/Result Backend]

- Role in system: Decouples task submission from execution, enables async processing
- Data flow: Tasks are published to broker, workers consume and execute, results stored in backend
- Interaction with other components: Integrates with web frameworks, databases, monitoring tools

---

## 5. Execution Model

- **Sync / Async**: Async (non-blocking task submission)
- **Push vs Pull**: Pull (workers pull tasks from broker)
- **Event-driven / Request-driven**: Both (request-driven task submission, event-driven worker processing)
- **Stateful / Stateless**: Stateless workers, stateful broker/result backend

---

## 6. Scaling Model

**How does this tool scale?**

- **Vertical scaling**: Increase worker concurrency, add more CPU/memory to worker instances
- **Horizontal scaling**: Add more worker processes/containers, distribute across multiple machines
- **Bottlenecks**: Broker throughput, result backend write performance, task serialization
- **Throughput behavior**: Linear scaling with worker count until broker becomes bottleneck

---

## 7. Setup (Minimal Working Setup)

### Example Config
```python
# celery_config.py
from celery import Celery
import os

app = Celery('my_project')
app.conf.update(
    broker_url=os.getenv('CELERY_BROKER_URL', 'redis://localhost:6379/0'),
    result_backend=os.getenv('CELERY_RESULT_BACKEND', 'redis://localhost:6379/0'),
    task_serializer='json',
    accept_content=['json'],
    result_serializer='json',
    timezone='UTC',
    enable_utc=True,
)
```

### Example Code
```python
# tasks.py
from .celery_config import app

@app.task(bind=True, max_retries=3)
def process_data(self, data):
    try:
        # Your processing logic here
        return f"Processed: {data}"
    except Exception as exc:
        raise self.retry(exc=exc, countdown=30)

# Usage
result = process_data.delay({"key": "value"})
task_id = result.id
```

### Docker Compose Setup
```yaml
services:
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
  
  celery-worker:
    build: .
    command: celery -A my_project worker --loglevel=info
    depends_on:
      - redis
    environment:
      - CELERY_BROKER_URL=redis://redis:6379/0
      - CELERY_RESULT_BACKEND=redis://redis:6379/0
```

---

## 8. Key Patterns

Patterns enabled by this tool:

- **Queue-based processing**: Async task execution with retry logic
- **Fanout / Pub-Sub**: Multiple workers processing same task queue
- **Retry mechanisms**: Automatic retry with exponential backoff
- **Backpressure handling**: Queue depth management, worker scaling
- **Periodic tasks**: Scheduled execution using Celery Beat
- **Task chaining**: Sequential task execution with data passing
- **Task groups**: Parallel task execution with result aggregation

---

## 9. Trade-offs

| Pros | Cons |
|------|------|
| Enables horizontal scaling | Increased infrastructure complexity |
| Fault-tolerant with retries | Debugging async tasks is harder |
| Built-in monitoring (Flower) | Requires message broker dependency |
| Supports task prioritization | Memory overhead for task queues |
| Mature and battle-tested | Learning curve for advanced features |

---

## 10. Failure Scenarios & Handling

- **What happens if worker dies**: Tasks are re-queued and picked up by other workers (if task_reject_on_worker_lost=True)
- **What happens if tool crashes**: Broker maintains task queue, workers can reconnect and resume processing
- **Retry strategies**: Exponential backoff, max retries, custom retry conditions
- **Data loss risks**: Broker failure (mitigated with Redis persistence), unacknowledged tasks
- **Observability needs**: Monitor queue depth, worker health, task failure rates

---

## 11. Observability & Monitoring

- **Metrics to track**: Queue length, task processing time, worker count, failure rate, retry rate
- **Logs**: Task execution logs, worker logs, error traces
- **Alerts**: Queue depth threshold, worker down, high failure rate
- **Tools used**: Flower (web UI), Prometheus metrics, custom monitoring

---

## 12. Production Considerations

- **Security**: Broker authentication, network isolation, task data encryption
- **Config management**: Environment variables for broker URLs, separate configs per environment
- **Resource usage**: Memory limits per worker, CPU allocation, broker sizing
- **Cost considerations**: Worker instances, broker resources, monitoring overhead
- **Deployment strategy**: Containerized workers, auto-scaling based on queue depth

---

## 13. Integration with Your Stack

How YOU will use this (important for your roadmap):

- **In current project**: Background tender processing, concurrent task execution with Redis broker
- **In future Stage (Stage 3 / 4 / 5)**: Multi-queue task routing, distributed workers across multiple nodes, event-driven architecture
- **With which tools**: Redis (broker), Docker (containerization), Flower (monitoring), future integration with message queues like RabbitMQ

---

## 14. Real Use Cases

- **Background job processing**: Email sending, file uploads, report generation
- **Data processing pipelines**: ETL workflows, data validation, batch processing
- **Notification systems**: Push notifications, SMS sending, email campaigns
- **File processing**: Image resizing, video transcoding, document conversion
- **API rate limiting**: Deferred processing for high-volume requests
- **Periodic maintenance**: Data cleanup, cache warming, health checks

---

## 15. Anti-Patterns

- **Using for real-time processing**: Celery adds latency, not suitable for sub-second responses
- **Long-running tasks (>24 hours**: Can cause worker resource exhaustion, better for streaming architectures
- **Large task payloads**: Overloads broker memory, use external storage for large data
- **Synchronous waiting for results**: Defeats purpose of async processing
- **Ignoring worker health**: Without monitoring, failed workers can go unnoticed

---

## 16. Commands & Debugging

```bash
# Start worker
celery -A my_project worker --loglevel=info --concurrency=4

# Start beat scheduler
celery -A my_project beat --loglevel=info

# Monitor with Flower
celery -A my_project flower --port=5555

# Check active tasks
celery -A my_project inspect active

# Check worker stats
celery -A my_project inspect stats

# Purge all queues (development only)
celery -A my_project purge

# List registered tasks
celery -A my_project inspect registered
```

---

## 17. Summary (Mental Model)

"Celery is used for solving complex background processing problems by enabling efficient, fault-tolerant task execution in distributed systems."
