# 001: Choose ECS Fargate for Celery Deployment

**Status:** Accepted  
**Date:** 2026-05-09  
**Category:** Processing  

## Context

**What problem are we solving and why now?**

Need scalable background task processing for 100-1000 concurrent tasks with auto-scaling and 24/7 availability. The deployment script shows a production-ready ECS Fargate setup with Redis broker, supporting both worker and beat services.

## Decision

**Chosen option:** Deploy Celery workers on ECS Fargate using Redis as the message broker.

## Options Considered

### Option 1: ECS Fargate with Redis (Chosen)
- **Pros**: Serverless, managed scaling, AWS integration, pay-per-use, no EC2 management
- **Cons**: Less control than EC2, potential cold starts, vendor lock-in

### Option 2: EC2 Auto Scaling Group with Celery
- **Pros**: Full control, predictable performance, cost-effective at scale, no vendor lock-in
- **Cons**: Operational overhead, patching responsibility, complex setup, requires 24/7 ops

### Option 3: Kubernetes with EKS
- **Pros**: Most flexible, standard orchestration, advanced features, portable
- **Cons**: High complexity, steep learning curve, expensive for small workloads, overkill for current needs

## Justification

**Why this option, given our current constraints:**

- **Team size (2-3 engineers)** with AWS experience - Fargate reduces operational burden
- **Budget constraints** - Pay-per-use model is cost-effective for variable workloads
- **Time to market (8 weeks)** - Fargate setup is faster than EC2 or K8s
- **Existing AWS infrastructure** - Leverages current AWS services and expertise
- **Performance requirements (<100ms task processing)** - Fargate provides consistent performance
- **Scalability needs (100-1000 concurrent tasks)** - Auto-scaling handles variable load

## Implementation Details

**Deployment Architecture:**
- ECS Fargate cluster with public subnets
- Redis ElastiCache as message broker
- Separate services for workers and beat scheduler
- Environment-based configuration via deploy.env
- JSON template rendering for task definitions

**Key Components:**
- Worker service: Handles background task processing
- Beat service: Manages scheduled tasks
- Task definitions: Container configurations with resource limits
- Auto-scaling: Based on queue depth and CPU utilization

## Consequences

**Positive:**
- Reduced operational overhead (no EC2 management)
- Automatic scaling based on demand
- Integrated with AWS monitoring and logging
- Simplified deployment process

**Negative:**
- AWS vendor lock-in
- Less control over underlying infrastructure
- Potential for higher costs at very large scale

## Monitoring Requirements

- ECS service metrics (CPU, memory, task count)
- Redis monitoring (memory usage, connection count)
- Celery queue depth monitoring
- CloudWatch logs aggregation
- Auto-scaling activity tracking

## When to Reconsider

- Monthly cost exceeds $2000 (evaluate EC2 cost-effectiveness)
- Need for specialized hardware or custom kernel modules
- Team grows to 10+ engineers with dedicated DevOps
- Multi-cloud strategy becomes a priority

## Related Decisions

- [002]: Redis as Message Broker Selection
- [003]: Auto-scaling Strategy for Workers
- [004]: Logging and Monitoring Setup
