---
trigger: always_on
---
# Architecture Documentation Rule

## Purpose
This rule defines the standard template and structure for documenting system architecture in the engineering notes system. Every architecture document must follow this template to ensure comprehensive system design coverage and decision clarity.

## Template Structure

Every system architecture documentation MUST include the following sections in order:

```markdown
# 🏗️ <System Name> - Architecture Document

---

## 1. Problem Statement

**What system are we building?**

- Core problem:
- Target users:
- Scale expectations (users / requests / data):

---

## 2. Requirements

### Functional Requirements
- Feature 1
- Feature 2
- Feature 3

### Non-Functional Requirements
- Scalability (expected load)
- Availability (SLA/SLO)
- Latency requirements
- Reliability
- Security

---

## 3. System Overview

**High-level explanation of how the system works**

- User flow:
- Core idea:
- Key components:

---

## 4. High-Level Architecture

```
[Client]
↓
[API Gateway / Backend]
↓
[Services Layer]
↓
[Queue / Async Processing]
↓
[Workers]
↓
[Database]
```

- Describe each block briefly
- Explain interaction flow

---

## 5. Component Breakdown

### 5.1 API Layer
- Responsibilities:
- Endpoints:
- Validation:
- Rate limiting:

---

### 5.2 Service Layer
- Business logic:
- Orchestration:
- Sync vs Async decisions:

---

### 5.3 Queue / Messaging Layer
- Why queue is used:
- Tool (Celery / Kafka / SQS etc):
- Queue design (single / multiple / priority):

---

### 5.4 Worker Layer
- Responsibilities:
- Concurrency model:
- Retry handling:

---

### 5.5 Database Layer
- DB type (SQL / NoSQL):
- Schema design:
- Indexing strategy:
- Scaling approach:

---

## 6. Data Flow

**Step-by-step flow**

1. User sends request
2. API validates
3. Service processes
4. Task pushed to queue
5. Worker consumes
6. Data stored / result returned

---

## 7. API Design

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | /example | Create resource |
| GET | /example | Fetch resource |

---

## 8. Data Model

### Example Schema

```json
{
  "id": "string",
  "name": "string",
  "status": "pending | completed"
}
```

---

## 9. Scaling Strategy

### Stage 1 (Simple)
- Single server
- No queue

### Stage 2 (Intermediate)
- Add queue
- Background workers

### Stage 3 (Advanced - Horizontal Scaling)
- Multiple workers
- Load balancer
- Auto-scaling

### Future Scaling
- Microservices
- Event-driven architecture
- Sharding / partitioning

---

## 10. Reliability & Fault Tolerance

- Retry mechanisms:
- Dead Letter Queue (DLQ):
- Circuit breakers:
- Timeout handling:

---

## 11. Observability

- Logging:
- Metrics:
- Monitoring tools (CloudWatch / Prometheus / Grafana):
- Alerts:

---

## 12. Deployment Architecture

### Local
- Docker / docker-compose

### Production
- ECS / Kubernetes / EC2
- Load balancer
- CI/CD pipeline

---

## 13. Security Considerations

- Authentication / Authorization
- Rate limiting
- Data validation
- Secrets management

---

## 14. Cost Considerations

- Compute cost
- Queue cost
- Database cost
- Optimization strategies

---

## 15. Trade-offs

| Decision | Why | Trade-off |
|----------|-----|-----------|
| Use queue | Async processing | Added complexity |
| No real-time DB | Simplicity | Delay in data |

---

## 16. Future Improvements

- Feature improvements
- Scaling upgrades
- Performance optimizations

---

## 17. Postmortem (Very Important)

### What Works
- Points

### Limitations
- Points

### Assumptions
- Points

### When to Move to Next Stage
- Trigger conditions
```

## Section Requirements

### Mandatory Sections (Must Complete)
- **Problem Statement** - Clear system definition and scope
- **Requirements** - Both functional and non-functional
- **System Overview** - High-level explanation
- **High-Level Architecture** - Visual diagram and flow
- **Component Breakdown** - Detailed component analysis
- **Data Flow** - Step-by-step process flow
- **Scaling Strategy** - Multi-stage evolution plan
- **Postmortem** - Critical self-assessment

### Context-Dependent Sections
- **API Design** - Only if system has APIs
- **Data Model** - Only if system has data persistence
- **Reliability** - Only if system has failure modes
- **Observability** - Only if monitoring is needed
- **Deployment** - Only if deployment is relevant
- **Security** - Only if security concerns exist
- **Cost** - Only if cost is a factor
- **Trade-offs** - Only if meaningful decisions were made
- **Future Improvements** - Only if evolution is planned

## Quality Standards

### Content Requirements
- **System-focused**: Focus on how components work together
- **Decision-driven**: Explain why each architectural choice was made
- **Evolution-ready**: Include scaling stages and migration paths
- **Reality-grounded**: Include limitations and assumptions

### Style Requirements
- **Visual**: Use diagrams and tables where appropriate
- **Hierarchical**: Break complex systems into layers
- **Flow-oriented**: Show data and control flow clearly
- **Complete**: Cover all major architectural aspects

## Enforcement

### When Creating New Architecture Docs
1. Use this template exactly as provided
2. Complete all mandatory sections
3. Include context-dependent sections only if relevant
4. Ensure scaling strategy includes at least 3 stages
5. Complete the postmortem section honestly

### When Updating Existing Architecture Docs
1. Map existing content to this template
2. Fill in missing mandatory sections
3. Add scaling stages if missing
4. Include honest postmortem assessment
5. Update trade-offs table with real decisions

## File Organization

### Location
- Architecture documentation should be placed in `architecture/` directory
- Use kebab-case filenames: `system-name.md`
- Group related architectures in subdirectories: `architecture/system-evolution/stage-1/`

### Cross-References
- Link to related tools in component sections
- Reference in `architecture/README.md`
- Include scaling stages in system evolution docs

## Diagram Standards

### High-Level Architecture
- Use ASCII art or mermaid diagrams
- Show clear data flow direction
- Label all components
- Include external dependencies

### Component Breakdown
- Use numbered subsections (5.1, 5.2, etc.)
- Include responsibilities bullet points
- Show interaction patterns
- Note technology choices

## Review Checklist

Before marking an architecture doc as complete:

- [ ] Problem statement clearly defines system scope
- [ ] Requirements include both functional and non-functional
- [ ] High-level architecture has clear diagram
- [ ] Component breakdown covers all major parts
- [ ] Data flow is step-by-step and complete
- [ ] Scaling strategy has at least 3 evolution stages
- [ ] Postmortem includes honest limitations
- [ ] Trade-offs table explains real decisions
- [ ] All sections follow the template structure
- [ ] Content is system-focused, not implementation-focused

## Integration with Other Rules

### Tool Documentation Rule
- Reference tool docs in Component Breakdown sections
- Use tool decision criteria when choosing technologies
- Include tool scaling models in system scaling strategy

### Documentation System Rule
- Place architecture docs in correct directory
- Follow document purpose mapping (architecture.md for system design)
- Keep operational content separate (use runbook.md for operations)

## Examples

### Good Problem Statement
```markdown
## 1. Problem Statement

**What system are we building?**

- Core problem: Users need to process large CSV files asynchronously and get results via email
- Target users: Data analysts processing 1-10GB files, 100-1000 users
- Scale expectations: 1000 concurrent users, 10GB files, 1-hour processing time
```

### Bad Problem Statement
```markdown
## 1. Problem Statement

We need to build a file processing system.
```

### Good Component Breakdown
```markdown
### 5.3 Queue / Messaging Layer
- Why queue is used: Async processing of large files, prevent request timeouts
- Tool: Celery with Redis broker (chosen for Python integration)
- Queue design: Priority queue for small files, separate queue for large files
```

### Bad Component Breakdown
```markdown
### 5.3 Queue Layer
We use a queue to process files.
```

## Evolution Tracking

### Version Control
- Tag architecture versions in git
- Use semantic versioning for major changes
- Include migration guides between stages

### Change Management
- Document architectural decisions in `decisions.md`
- Track why decisions were made
- Note when assumptions change

## Common Pitfalls to Avoid

### Don't Mix Implementation Details
- Architecture = "What the system is and how it's designed"
- Implementation = "How to build it"
- Keep runbook.md for operational commands

### Don't Skip the Postmortem
- Always include limitations
- Document assumptions explicitly
- Define clear stage migration triggers

### Don't Forget Scaling
- Always include at least 3 scaling stages
- Show evolution path clearly
- Consider both vertical and horizontal scaling
