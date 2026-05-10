# Channex

---

## 1. Problem Statement

**What problem does this tool solve in systems?**

- What gap exists without this tool: Hotel systems need unified management across multiple booking channels (Booking.com, Expedia, etc.) without manual synchronization
- Why does it matter in scalable systems: Prevents overbooking, rate inconsistencies, and manual overhead across distributed hotel operations
- What type of systems typically need this: Multi-channel hospitality platforms, property management systems, hotel booking engines

---

## 2. When to Use (Decision Criteria)

**Use this tool when:**
- Managing inventory across 3+ booking channels simultaneously
- Need real-time availability synchronization (sub-minute updates)
- Require unified rate plan management across external channels
- Processing 100+ bookings per day across multiple channels

**Do NOT use when:**
- Single-channel operation (direct website only)
- Low volume (<10 bookings/day)
- Simple property with single room type
- Manual synchronization is acceptable

---

## 3. Core Concepts

| Concept | Description |
|--------|-------------|
| Webhooks | Event-driven notifications for booking/availability changes |
| Inventory Sync | Real-time room availability synchronization |
| Rate Plans | Pricing structures mapped across channels |
| Mapping | External channel IDs ↔ internal property/room IDs |
| Channel Manager | Middleware layer between PMS and booking channels |
| Delta Updates | Incremental changes rather than full sync |

---

## 4. Architecture Role (System Design View)

**Where does this tool sit in the system?**

[Booking Channels] → [Channex API] → [Webhook Ingestion] → [Queue] → [Workers] → [PMS Database]

- Role in system: External integration middleware for hotel distribution
- Data flow: Bidirectional sync between external channels and internal PMS
- Interaction with other components: Event-driven updates to internal booking system

---

## 5. Execution Model

- **Sync / Async**: Async (webhook-driven) + sync (API polling fallback)
- **Push vs Pull**: Push (webhooks) + Pull (periodic reconciliation)
- **Event-driven / Request-driven**: Primarily event-driven with request-driven fallbacks
- **Stateful / Stateless**: Stateless API, but maintains mapping state

---

## 6. Scaling Model

**How does this tool scale?**

- **Vertical scaling**: Limited by API rate limits (typically 1000 requests/hour)
- **Horizontal scaling**: Can handle multiple properties via webhook fanout
- **Bottlenecks**: External API rate limits, webhook delivery delays
- **Throughput behavior**: 10-100 events/second per property typical

---

## 7. Setup (Minimal Working Setup)

### Example Config
```yaml
# channex-config.yml
api_endpoint: "https://api.channex.io/v1"
webhook_secret: "your-webhook-secret"
rate_limit: 1000  # requests per hour
sync_interval: 300  # seconds for fallback polling
```

### Example Code

```python
# webhook handler
import hmac
import hashlib

def verify_webhook(payload, signature, secret):
    expected = hmac.new(
        secret.encode(), 
        payload, 
        hashlib.sha256
    ).hexdigest()
    return hmac.compare_digest(expected, signature)

def handle_booking_webhook(event_type, data):
    if event_type == "booking.created":
        process_new_booking(data)
    elif event_type == "booking.updated":
        update_existing_booking(data)
```

---

## 8. Key Patterns

Patterns enabled by this tool:

- External Event-Driven Integration
- Webhook-based Synchronization
- Delta Update Processing
- Idempotent Event Handling
- Cross-System State Reconciliation

---

## 9. Trade-offs

| Pros | Cons |
|------|------|
| Unified multi-channel management | External dependency |
| Real-time synchronization | Rate limiting constraints |
| Reduces manual overbooking | Complex webhook handling |
| Built-in reconciliation | Limited control over external systems |

---

## 10. Failure Scenarios & Handling

- **What happens if webhook delivery fails?**: Retry with exponential backoff, fallback to API polling
- **What happens if Channex API is down?**: Queue events locally, retry with circuit breaker pattern
- **Retry strategies**: 3 retries with 1s, 5s, 15s intervals, then Dead Letter Queue
- **Data loss risks**: Duplicate events, missing events, delayed webhooks
- **Observability needs**: Webhook delivery tracking, event deduplication metrics

---

## 11. Observability & Monitoring

- **Metrics to track**: Webhook delivery rate, processing latency, duplicate events, API rate limit usage
- **Logs**: Event IDs, processing status, retry attempts, mapping resolution
- **Alerts**: Webhook failures > 5%, API rate limit exceeded, processing backlog > 1000 events
- **Tools used**: Prometheus metrics, structured logging, webhook monitoring dashboard

---

## 12. Production Considerations

- **Security**: Webhook signature verification, API key rotation, HTTPS only
- **Config management**: Environment-specific endpoints, rate limit configurations
- **Resource usage**: Queue sizing for webhook bursts, worker pool sizing
- **Cost considerations**: API usage tiers, webhook infrastructure costs
- **Deployment strategy**: Blue-green deployment for webhook handlers

---

## 13. Integration with Your Stack

How YOU will use this (important for your roadmap):

- **In current project**: PMS integration for hotel booking system
- **In future Stage (Stage 3 / 4 / 5)**: Multi-property hospitality platform, event-driven architecture
- **With which tools**: Redis queue, Celery workers, PostgreSQL database, Nginx reverse proxy

---

## 14. Real Use Cases

- Hotel chain managing 50+ properties across 10 booking channels
- Vacation rental platform with real-time availability sync
- Property management system integrating with OTA distribution
- Revenue management system with automated rate updates

---

## 15. Anti-Patterns

- **Ignoring idempotency**: Processing duplicate webhooks multiple times
- **Synchronous processing**: Blocking webhook responses with long operations
- **No fallback polling**: Relying solely on webhooks without reconciliation
- **Hardcoded mappings**: Not maintaining external ↔ internal ID mappings

---

## 16. Commands & Debugging

```bash
# Test webhook delivery
curl -X POST http://localhost:3000/webhooks/channex \
  -H "X-Channex-Signature: sha256=..." \
  -d '{"event":"booking.created","data":{...}}'

# Check API rate limits
curl -H "Authorization: Bearer $TOKEN" \
  https://api.channex.io/v1/rate_limits

# Verify webhook signature
python -c "
import hmac, hashlib
secret = 'your-secret'
payload = b'webhook-data'
signature = hmac.new(secret.encode(), payload, hashlib.sha256).hexdigest()
print(signature)
"
```

---

## 17. Summary (Mental Model)

"`Channex` is a channel manager middleware that enables real-time hotel inventory and booking synchronization across multiple distribution channels through event-driven webhooks and API integration."
