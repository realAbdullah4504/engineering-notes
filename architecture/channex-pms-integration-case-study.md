# 🏗️ Channex PMS Integration - System Design Case Study

---

## 1. Problem Statement

**What system are we building?**

- **Core problem**: Hotel Property Management System (PMS) needs real-time synchronization with multiple booking channels (Booking.com, Expedia, Airbnb) to prevent overbooking and maintain rate consistency
- **Target users**: Hotel operators managing 10-100 properties, processing 500-5000 bookings/month
- **Scale expectations**: 50 concurrent properties, 10 booking channels, 1000+ bookings/day, <30 second sync latency

---

## 2. Requirements

### Functional Requirements
- Real-time inventory synchronization across all channels
- Unified booking management and guest information
- Rate plan management and pricing updates
- Automated reconciliation and conflict resolution
- Reporting and analytics across all channels

### Non-Functional Requirements
- **Scalability**: Handle 1000+ bookings/day without degradation
- **Availability**: 99.9% uptime during peak booking hours
- **Latency**: <30 seconds for inventory updates, <5 seconds for booking confirmations
- **Reliability**: No lost bookings, guaranteed event processing
- **Security**: Webhook signature verification, API encryption

---

## 3. System Overview

**High-level explanation of how the system works**

- **User flow**: Hotel manager updates room rates in PMS → Channex distributes to all channels → guests book on any channel → webhook notifies PMS → inventory updated across all channels
- **Core idea**: Channex acts as middleware, eliminating direct channel integrations while providing unified management
- **Key components**: Webhook ingestion, event processing queue, booking workers, inventory synchronization, rate management

---

## 4. High-Level Architecture

```
[Booking Channels]
    ↓
[Channex API]
    ↓ (webhooks)
[Webhook Ingestion Layer]
    ↓
[Redis Queue]
    ↓
[Celery Workers]
    ↓
[PostgreSQL PMS Database]
    ↓
[Management Dashboard]
```

- **Webhook Ingestion Layer**: Receives and validates Channex events
- **Redis Queue**: Buffers events for reliable processing
- **Celery Workers**: Process bookings, inventory updates, rate changes
- **PostgreSQL**: Stores bookings, inventory, mappings, audit logs
- **Management Dashboard**: UI for hotel operators

---

## 5. Component Breakdown

### 5.1 Webhook Ingestion Layer
- **Responsibilities**: Receive webhooks, verify signatures, validate schema, queue events
- **Endpoints**: `/webhooks/channex/bookings`, `/webhooks/channex/inventory`, `/webhooks/channex/rates`
- **Validation**: HMAC signature verification, JSON schema validation, rate limiting
- **Rate limiting**: 1000 requests/minute per IP, burst protection

### 5.2 Queue Layer
- **Why queue is used**: Handle webhook bursts, ensure reliable processing, enable retry logic
- **Tool**: Redis with Celery broker (chosen for Python integration and reliability)
- **Queue design**: Priority queues (bookings=high, inventory=medium, rates=low), separate queues per event type

### 5.3 Worker Layer
- **Responsibilities**: Process business logic, update database, handle conflicts, trigger downstream updates
- **Concurrency model**: 10-20 workers per queue, auto-scaling based on queue depth
- **Retry handling**: 3 retries with exponential backoff, Dead Letter Queue for manual inspection

### 5.4 Database Layer
- **DB type**: PostgreSQL (chosen for ACID compliance and complex queries)
- **Schema design**: Bookings table, Inventory table, Rate plans table, External mappings table, Audit log table
- **Indexing strategy**: Composite indexes on (property_id, room_type, date), external_id mappings
- **Scaling approach**: Read replicas for dashboard queries, connection pooling

---

## 6. Data Flow

### Success Path
**Step-by-step flow**

1. Guest books room on Booking.com
2. Channex receives booking, processes validation
3. Channex sends webhook to PMS ingestion endpoint
4. Ingestion layer verifies signature and validates data
5. Event queued to Redis bookings queue (high priority)
6. Celery worker picks up booking event
7. Worker checks for duplicate booking (idempotency)
8. Booking created in PostgreSQL database
9. Inventory updated across all room types
10. Confirmation sent to Channex API
11. Dashboard updated with new booking

### Failure Scenarios
- **Webhook fails** → Channex retries, PMS returns 500, event queued for retry
- **Queue fails** → Redis persistence, backup queue in memory, alert monitoring
- **Worker crashes** → Event redelivery, partial update rollback, deadlock handling
- **Database fails** → Transaction rollback, retry with circuit breaker, fallback to read-only mode
- **Network issues** → Timeout handling, exponential backoff, circuit breaker pattern

---

## 7. Constraints

*System constraints that influence architectural decisions*

- **Team size**: 3-4 engineers (full-stack + backend + DevOps)
- **Budget**: <$2000/month for infrastructure
- **Time to market**: 12-week deadline for MVP
- **Tech familiarity**: Python/Django stack, PostgreSQL, Redis
- **Existing infrastructure**: AWS, existing PMS database structure
- **Compliance requirements**: PCI compliance for payment data, GDPR for guest data
- **Performance requirements**: <30 second inventory sync, <5 second booking processing

---

## 8. API Design

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | /webhooks/channex/bookings | Receive booking events from Channex |
| POST | /webhooks/channex/inventory | Receive inventory change events |
| POST | /webhooks/channex/rates | Receive rate plan updates |
| GET | /api/bookings | Retrieve bookings with filters |
| PUT | /api/inventory | Update room availability |
| POST | /api/rates | Update pricing across channels |

---

## 9. Data Model

### Booking Schema
```json
{
  "id": "uuid",
  "external_id": "channex_booking_123",
  "property_id": "uuid",
  "room_type_id": "uuid",
  "guest_info": {
    "name": "John Doe",
    "email": "john@example.com"
  },
  "check_in": "2024-06-15",
  "check_out": "2024-06-18",
  "status": "confirmed | pending | cancelled",
  "channel": "booking.com | expedia | airbnb",
  "created_at": "timestamp",
  "updated_at": "timestamp"
}
```

### Inventory Schema
```json
{
  "property_id": "uuid",
  "room_type_id": "uuid",
  "date": "2024-06-15",
  "available_rooms": 5,
  "total_rooms": 10,
  "blocked_rooms": 2,
  "last_updated": "timestamp"
}
```

---

## 10. Scaling Strategy

### Stage 1 (MVP - Current)
- Single server deployment
- Basic webhook processing
- Simple queue with 2-3 workers
- Direct database writes

### Stage 2 (Growth)
- Add Redis cluster for queue reliability
- Multiple workers per queue (5-10)
- Database read replicas for dashboard
- Basic monitoring and alerting

### Stage 3 (Scale)
- Load balancer with multiple app servers
- Auto-scaling workers based on queue depth
- Database sharding by property_id
- Advanced monitoring and circuit breakers

### Future Scaling
- Event streaming with Kafka for audit trail
- Microservices architecture
- Multi-region deployment
- Machine learning for demand forecasting

---

## 11. Reliability & Fault Tolerance

- **Retry mechanisms**: 3 retries with exponential backoff (1s, 5s, 15s)
- **Dead Letter Queue**: Failed events stored for manual inspection
- **Circuit breakers**: Stop processing when error rate > 10%
- **Timeout handling**: 30 second webhook timeout, 5 minute worker timeout
- **Idempotency**: Event ID tracking to prevent duplicate processing

---

## 12. Observability

- **Logging**: Structured JSON logs with event IDs, processing times, error details
- **Metrics**: Webhook rate, processing latency, queue depth, error rates, database performance
- **Monitoring tools**: Prometheus metrics, Grafana dashboards, ELK stack for logs
- **Alerts**: Queue backlog > 1000, error rate > 5%, webhook delivery failures > 2%

---

## 13. Deployment Architecture

### Local
- Docker Compose with Redis, PostgreSQL, Django app
- Local webhook testing with ngrok
- Mock Channex API for development

### Production
- AWS ECS Fargate for container orchestration
- Application Load Balancer with SSL termination
- RDS PostgreSQL with Multi-AZ deployment
- ElastiCache Redis cluster
- CloudWatch for monitoring and logging

---

## 14. Security Considerations

- **Authentication**: HMAC signature verification for webhooks
- **Authorization**: API key authentication for internal APIs
- **Rate limiting**: 1000 requests/minute per IP
- **Data validation**: JSON schema validation for all inputs
- **Secrets management**: AWS Secrets Manager for API keys and database credentials
- **Encryption**: TLS 1.3 for all communications, encrypted database fields

---

## 15. Cost Considerations

- **Compute cost**: $400/month (ECS Fargate, auto-scaling)
- **Queue cost**: $150/month (ElastiCache Redis cluster)
- **Database cost**: $300/month (RDS PostgreSQL with Multi-AZ)
- **Monitoring cost**: $100/month (CloudWatch, custom metrics)
- **Total**: ~$950/month at scale, well under $2000 budget

---

## 16. Trade-offs

| Decision | Why | Trade-off |
|----------|-----|-----------|
| Use Redis queue | Fast, Python integration | Memory-based limits |
| PostgreSQL database | ACID compliance, complex queries | Scaling complexity |
| Webhook-first approach | Real-time updates | Dependency on Channex reliability |
| Single database for MVP | Faster development | Scaling limitations later |

---

## 17. Future Improvements

- **Event streaming**: Add Kafka for complete audit trail and replay capability
- **Microservices**: Split booking, inventory, and rate management into separate services
- **Advanced analytics**: Real-time occupancy forecasting and dynamic pricing
- **Mobile app**: Native mobile application for hotel managers
- **Multi-tenancy**: Support for multiple hotel chains on single platform

---

## 18. Postmortem (Very Important)

### What Works
- Webhook-based real-time synchronization prevents overbooking
- Queue-based processing handles burst traffic during peak hours
- Idempotency handling prevents duplicate bookings
- Single database simplifies development and debugging

### Limitations
- Single database becomes bottleneck at high scale
- Memory-based queue limits event retention
- No built-in replay capability for missed events
- Tight coupling to Channex API limits flexibility

### Assumptions
- Channex webhook delivery is reliable (95%+ success rate)
- Hotel operations follow standard booking patterns
- Peak load is predictable (seasonal variations)
- Data consistency is more important than availability

### When to Move to Next Stage
- Queue depth consistently > 5000 events
- Database query latency > 100ms for 90th percentile
- Error rate > 2% for webhook processing
- Team size grows beyond 5 engineers
- Need to support >1000 concurrent properties

---

## 19. Related Decisions

- [001]: Queue System Selection (Redis vs SQS vs RabbitMQ)
- [002]: Database Choice (PostgreSQL vs MongoDB)
- [003]: Deployment Strategy (ECS vs Kubernetes vs EC2)
- [004]: Webhook Security (HMAC vs JWT vs API Key)
