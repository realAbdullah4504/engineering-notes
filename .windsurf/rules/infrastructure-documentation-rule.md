# Infrastructure Documentation Rule

## Purpose
This rule defines the standard template and structure for documenting infrastructure in the engineering notes system. Every infrastructure document must follow this template to ensure complete coverage of deployment, networking, and operational infrastructure.

## Template Structure

Every infrastructure documentation MUST include the following sections in order:

```markdown
# ⚙️ <System Name> - Infrastructure Document

---

## 1. Infrastructure Overview

**What environment runs this system?**

- Cloud Provider: (AWS / GCP / Azure / Local)
- Deployment Model: (Single server / Distributed / Containerized)
- Environments:
  - Local
  - Staging
  - Production

---

## 2. High-Level Infrastructure Architecture

```
[User]
↓
[DNS]
↓
[Load Balancer]
↓
[Application Services]
↓
[Queue System]
↓
[Workers]
↓
[Database]
↓
[Storage / Cache]
```

- Explain each component briefly
- Mention managed vs self-hosted

---

## 3. Compute Layer

### Services
- API Servers:
  - Type: (EC2 / ECS / Kubernetes)
  - Scaling: (Manual / Auto Scaling)

- Workers:
  - Type:
  - Scaling strategy:

---

## 4. Networking

- VPC setup:
- Public vs Private subnets:
- Security groups:
- Ports exposed:
- Internal communication:

---

## 5. Load Balancing

- Type: (ALB / Nginx / Cloud LB)
- Routing rules:
- Health checks:

---

## 6. Storage Layer

### Database
- Type: (MongoDB / PostgreSQL)
- Hosting: (Managed / Self-hosted)
- Backup strategy:
- Replication:

### Object Storage
- S3 / Blob storage:
- Use case:

### Cache (if any)
- Redis / Memcached:
- Purpose:

---

## 7. Queue & Messaging Infrastructure

- Tool used:
- Deployment (managed / containerized):
- Queue strategy:
- Throughput considerations:

---

## 8. Deployment Strategy

### CI/CD Pipeline
- Tool: (GitHub Actions / CodePipeline)
- Steps:
  1. Build
  2. Test
  3. Deploy

### Deployment Type
- Rolling
- Blue-Green
- Canary

---

## 9. Scaling Strategy

### Horizontal Scaling
- Auto-scaling rules:
- Metrics used (CPU / Queue length / Requests):

### Vertical Scaling
- Instance upgrades:

---

## 10. Observability

### Logging
- Tool:
- Centralization:

### Metrics
- CPU / Memory
- Request latency
- Queue depth

### Monitoring
- Tools:

### Alerts
- Conditions:
- Notification channels:

---

## 11. Reliability & Fault Tolerance

- Multi-AZ deployment:
- Failover strategy:
- Retry mechanisms:
- Dead letter queues:
- Health checks:

---

## 12. Security

- IAM roles / permissions:
- Secrets management:
- HTTPS / TLS:
- Firewall rules:
- API protection:

---

## 13. Cost Optimization

- Auto-scaling policies:
- Spot instances (if used):
- Resource cleanup:
- Monitoring unused resources:

---

## 14. Environment Configuration

### Environment Variables
- Example:

```env
DB_URL=
REDIS_URL=
API_KEY=
```

### Secrets Management
- Tool used:

---

## 15. Local Development Setup

- Docker / docker-compose:
- Services included:
- Differences from production:

---

## 16. Disaster Recovery

- Backup frequency:
- Restore strategy:
- Data retention:

---

## 17. Trade-offs

| Decision | Why | Trade-off |
|----------|-----|-----------|
| Use ECS | Simpler ops | Less control than K8s |
| Use Redis | Fast queue | Memory-based limits |

---

## 18. Future Improvements

- Move to Kubernetes
- Multi-region deployment
- Better auto-scaling rules
```

## Section Requirements

### Mandatory Sections (Must Complete)
- **Infrastructure Overview** - Clear environment definition
- **High-Level Infrastructure Architecture** - Visual diagram and component explanation
- **Compute Layer** - Detailed compute resources
- **Networking** - Complete network setup
- **Storage Layer** - All storage components
- **Deployment Strategy** - CI/CD and deployment approach
- **Observability** - Monitoring and alerting
- **Security** - Security measures and controls
- **Trade-offs** - Infrastructure decision analysis

### Context-Dependent Sections
- **Load Balancing** - Only if load balancer is used
- **Queue & Messaging** - Only if queues are used
- **Scaling Strategy** - Only if scaling is implemented
- **Reliability** - Only if fault tolerance is needed
- **Cost Optimization** - Only if cost is a concern
- **Environment Configuration** - Only if configuration is complex
- **Local Development** - Only if local setup differs
- **Disaster Recovery** - Only if data recovery is needed
- **Future Improvements** - Only if evolution is planned

## Quality Standards

### Content Requirements
- **Infrastructure-focused**: Focus on how the system runs, not how it's built
- **Environment-specific**: Clearly distinguish between local, staging, and production
- **Operations-ready**: Include all operational aspects needed to run the system
- **Security-conscious**: Include security controls and best practices

### Style Requirements
- **Visual**: Use diagrams to show infrastructure flow
- **Hierarchical**: Break infrastructure into logical layers
- **Configuration-focused**: Include actual configuration examples
- **Cost-aware**: Consider cost implications of decisions

## Enforcement

### When Creating New Infrastructure Docs
1. Use this template exactly as provided
2. Complete all mandatory sections
3. Include context-dependent sections only if relevant
4. Ensure environment differences are clearly documented
5. Include real configuration examples

### When Updating Existing Infrastructure Docs
1. Map existing content to this template
2. Fill in missing mandatory sections
3. Add environment-specific details
4. Update security controls as needed
5. Review cost optimization strategies

## File Organization

### Location
- Infrastructure documentation should be placed in `infrastructure/` directory
- Use kebab-case filenames: `system-name-infrastructure.md`
- Group by cloud provider: `infrastructure/aws/`, `infrastructure/gcp/`

### Cross-References
- Link to architecture docs for system context
- Reference in `infrastructure/README.md`
- Include deployment scripts in appropriate directories

## Diagram Standards

### High-Level Infrastructure Architecture
- Use ASCII art or mermaid diagrams
- Show data flow and dependencies
- Label all infrastructure components
- Indicate managed vs self-hosted services

### Network Diagrams
- Show VPC structure
- Indicate public vs private subnets
- Include security groups and NACLs
- Show internet-facing vs internal components

## Configuration Standards

### Environment Variables
- Use `.env` format for examples
- Include all required variables
- Mark sensitive variables clearly
- Provide example values where safe

### Infrastructure as Code
- Include Terraform/CloudFormation snippets if used
- Show resource definitions
- Explain variable usage
- Document module structure

## Review Checklist

Before marking an infrastructure doc as complete:

- [ ] Infrastructure overview clearly defines environment
- [ ] High-level architecture has complete diagram
- [ ] Compute layer covers all services and workers
- [ ] Networking includes VPC, subnets, and security
- [ ] Storage layer covers database, cache, and object storage
- [ ] Deployment strategy includes CI/CD pipeline
- [ ] Observability covers logging, metrics, and alerts
- [ ] Security includes IAM, secrets, and network controls
- [ ] Trade-offs table explains real infrastructure decisions
- [ ] All sections follow the template structure
- [ ] Content is infrastructure-focused, not application-focused

## Integration with Other Rules

### Architecture Documentation Rule
- Reference architecture docs for system context
- Align infrastructure components with architecture layers
- Ensure scaling strategies match between docs

### Tool Documentation Rule
- Reference tool docs for infrastructure tool choices
- Use tool setup guides for infrastructure configuration
- Include tool-specific scaling and reliability patterns

### Documentation System Rule
- Place infrastructure docs in correct directory
- Follow document purpose mapping (infrastructure docs for operational concerns)
- Keep implementation details separate from architecture

## Examples

### Good Infrastructure Overview
```markdown
## 1. Infrastructure Overview

**What environment runs this system?**

- Cloud Provider: AWS
- Deployment Model: Containerized with ECS
- Environments:
  - Local: Docker Compose
  - Staging: ECS Fargate, single AZ
  - Production: ECS Fargate, multi-AZ
```

### Bad Infrastructure Overview
```markdown
## 1. Infrastructure Overview

We use AWS for our system.
```

### Good Compute Layer
```markdown
## 3. Compute Layer

### Services
- API Servers:
  - Type: ECS Fargate
  - Scaling: Auto-scaling, 2-10 instances, target CPU 70%

- Workers:
  - Type: ECS Fargate
  - Scaling: Queue-based, 1-20 instances based on queue depth
```

### Bad Compute Layer
```markdown
## 3. Compute Layer

We run our services on containers.
```

## Security Requirements

### Minimum Security Controls
- All services run in private subnets
- All traffic encrypted in transit (TLS)
- Secrets stored in dedicated secrets manager
- IAM roles follow principle of least privilege
- All public resources behind WAF/firewall

### Documentation Requirements
- Document all IAM roles and permissions
- Include security group rules
- Show network ACLs if used
- Document compliance requirements if applicable

## Cost Documentation

### Cost Tracking
- Include estimated monthly costs for each component
- Document cost drivers and scaling impact
- Include cost optimization strategies
- Show cost differences between environments

### Optimization Strategies
- Auto-scaling policies to minimize idle resources
- Spot instance usage where appropriate
- Resource scheduling for non-production environments
- Storage lifecycle policies

## Disaster Recovery Standards

### Backup Requirements
- Document backup frequency and retention
- Include restore procedures
- Show RTO/RPO targets
- Document cross-region replication if used

### Testing Requirements
- Document disaster recovery testing schedule
- Include test results and lessons learned
- Update procedures based on test outcomes

## Common Pitfalls to Avoid

### Don't Mix Application Code
- Infrastructure = "How the system runs and operates"
- Application = "What the system does"
- Keep application code in separate repositories

### Don't Skip Security
- Always include security controls
- Document compliance requirements
- Show audit logging if needed

### Don't Forget Cost
- Always consider cost implications
- Include cost optimization strategies
- Document cost monitoring approaches
