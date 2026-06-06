---
trigger: always_on
---
# Decision Map Documentation Rule

## Purpose
This rule defines the standard template and structure for documenting architectural decisions in the engineering notes system. Every decision map follows the ADR (Architecture Decision Record) style to capture engineering reasoning at important forks.

## Core Philosophy

### One Decision = One File
- ✅ **Correct**: Each meaningful architectural decision gets its own file
- ❌ **Wrong**: One giant file containing all decisions

### File Structure
```
architecture/decisions/
  001-deployment-strategy.md
  002-queue-system.md
  003-database-choice.md
  004-scaling-strategy.md
```

### Naming Convention
Use: `00X-name.md`
- Numbers = timeline of thinking
- Descriptive names = clear decision scope
- Examples: `001-deployment-strategy.md`, `002-celery-vs-sqs.md`

## Template Structure

Every decision document MUST follow this structure:

```markdown
# 00X: <Decision Title>

**Status:** Accepted  
**Date:** YYYY-MM-DD  
**Category:** Processing/Deployment/Data/Architecture/Infrastructure  

## Context

**What problem are we solving and why now?**

- Clear problem definition
- Current requirements and constraints
- Why this decision matters at this stage

## Decision

**Chosen option:** <Option Name>

## Options Considered

### Option 1: <Name>
- **Pros**: Advantage 1, Advantage 2
- **Cons**: Trade-off 1, Risk 1

### Option 2: <Name>
- **Pros**: Advantage 1, Advantage 2
- **Cons**: Trade-off 1, Risk 1

### Option 3: <Name> (if applicable)
- **Pros**: Advantage 1, Advantage 2
- **Cons**: Trade-off 1, Risk 1

## Justification

**Why this option, given our current constraints:**

- Team size and expertise alignment
- Current scale and performance requirements
- Implementation speed and complexity tolerance
- Cost considerations and business constraints

## Consequences

### Positive
- Benefit 1
- Benefit 2

### Negative
- Trade-off 1
- Trade-off 2

### Risks
- Risk 1
- Risk 2

## Evolution Triggers

**When should this decision change?**

Should be:
- Prefer measurable signals (metrics, thresholds)
- OR clear qualitative signals (team size, complexity, pain points)

Examples:
✅ "Queue delay becomes noticeable to users"
✅ "Manual scaling becomes frequent"  
✅ "Team grows beyond 5 engineers"
❌ "When system grows"

## Migration Feasibility (Optional)

**Can we evolve from this decision?**

Include ONLY when:
- Decision is hard to reverse
- Migration cost is significant  
- System lock-in is high

Otherwise skip this section.

- Technical feasibility: High/Medium/Low
- Migration complexity: Low/Medium/High
- Key migration enablers
- Major migration blockers
```

## Decision File Creation Rules

### Create New File When:
| Situation | New File? |
|-----------|-----------|
| Choosing Redis vs RabbitMQ | ✅ |
| Choosing ECS vs Kubernetes | ✅ |
| Choosing script vs CI/CD | ✅ |
| Choosing database technology | ✅ |
| Choosing authentication method | ✅ |

### DO NOT Create New File When:
| Situation | New File? |
|-----------|-----------|
| Changing one environment variable | ❌ |
| Updating script logic | ❌ |
| Minor configuration tweaks | ❌ |
| Bug fixes | ❌ |

## Effort Rule (Critical)

Decision documentation should take:

- **Simple decision**: 5–10 minutes
- **Medium decision**: 10–20 minutes  
- **Complex decision**: 20–30 minutes MAX

If it takes longer:
→ You are over-documenting
→ Reduce detail and focus only on WHY

**Priority order:**
1. Build system
2. Make decision  
3. Document briefly

NOT:
1. Document
2. Think
3. Build ❌

## Quality Standards

### Content Requirements
- **Problem-focused**: Start with clear problem definition
- **Options-limited**: 2-4 realistic options only
- **Decision-clear**: One unambiguous choice
- **Justification-concrete**: Based on actual constraints
- **Evolution-specific**: Prefer measurable signals, accept qualitative ones

### Style Requirements
- **Short**: Keep it concise and focused
- **Concrete**: Avoid vague language
- **System-focused**: Not tool tutorials
- **Decision-oriented**: Focus on why, not how

## Anti-Patterns to Avoid

### ❌ Over-splitting
Don't create separate files for:
- `deployment-script.md`
- `deployment-env.md`
- `deployment-cli.md`

These are implementation details, not architectural decisions.

### ❌ Including Implementation Details
- No code snippets
- No configuration examples
- No command-line instructions
- No specific metrics or thresholds
- No operational procedures

### ❌ Mixing Documentation Levels
Decision documents should NOT include:
- **Architecture details**: System design, component breakdown
- **Infrastructure details**: Deployment scripts, monitoring setup
- **Operational details**: CPU thresholds, queue limits, scaling configs

These belong in:
- `architecture/` for system design
- `infrastructure/` for deployment and operations
- `runbook.md` for operational procedures

### ❌ Vague Evolution Triggers
- "When we scale more" → ❌
- "When request rate exceeds 10K/second" → ✅

## File Organization

### Location
- All decision files go in `architecture/decisions/`
- Use sequential numbering (001, 002, 003...)
- No gaps in numbering

### Cross-References
- Reference decisions in architecture documents
- Link related decisions in "Evolution Triggers"
- Update decision map index

## Review Checklist

Before marking a decision document as complete:

- [ ] Problem statement is clear and specific
- [ ] 2-4 realistic options are presented
- [ ] Each option has balanced pros/cons
- [ ] One clear decision is made
- [ ] Justification references actual constraints
- [ ] Evolution triggers are specific and measurable
- [ ] No implementation details included
- [ ] File follows naming convention
- [ ] Content is decision-focused, not tutorial

## Integration with Other Rules

### Architecture Documentation Rule
- Reference decisions in architecture components
- Use decisions to explain architectural choices
- Include decision evolution in system scaling

### Tool Documentation Rule
- Reference tool decisions in tool docs
- Use decision criteria for tool selection
- Include decision rationale in tool choices

### Pattern Documentation Rule
- Reference decisions in pattern applications
- Use decisions to explain pattern choices
- Include decision evolution in pattern maturation

## Mental Model

Think of decision maps as:
- **"Snapshots of your engineering brain at important forks"** ✅
- **"Why we chose this deployment strategy at this stage"** ✅
- **"Logs of what you did"** ❌
- **"Configuration documentation"** ❌
- **"Tool tutorials"** ❌

## Abdullah's Optimized Rule

Decision maps are thinking tools, not documentation tasks

**Core behavior:**
Write only when a real decision is made
Keep it short and sharp
Focus on:
- tradeoffs
- reasoning  
- future change triggers

**Avoid becoming:**
❌ Documentation-heavy engineer
❌ Tool-obsessed
❌ Process slow

**Become:**
✅ Decision-driven engineer
✅ System thinker
✅ Fast executor

## Three-Level Separation (Critical)

Decision documents must maintain strict separation:

### Level 1: Decisions (WHY)
- **What**: Choice between alternatives
- **Focus**: Rationale, trade-offs, evolution triggers
- **Example**: ECS vs Kubernetes vs EC2

### Level 2: Architecture (WHAT & HOW)
- **What**: System design and structure
- **Focus**: Components, data flow, scaling strategy
- **Example**: Celery ECS architecture with Redis broker

### Level 3: Operations (HOW TO RUN)
- **What**: Deployment and monitoring
- **Focus**: Scripts, thresholds, procedures
- **Example**: CPU thresholds, queue limits, deployment commands

**Never mix levels in one document.**

## Example Application

### File: `001-deployment-strategy.md`

**Problem**: How do we deploy and manage services?

**Options**:
1. ECS (Elastic Container Service)
2. EC2 with custom scripts
3. Kubernetes

**Decision**: ECS

**Justification**: Team size (2-3 engineers), current scale (simple web app), need for managed service, cost constraints.

**Evolution Triggers**: When team grows beyond 5 engineers, when we need complex orchestration, when cost becomes less constraining.

## Evolution of Decision Maps

### Stage 1: Initial Decisions
- Core technology choices
- Basic deployment strategy
- Simple architectural patterns

### Stage 2: Scaling Decisions
- Performance optimizations
- More complex deployment patterns
- Advanced architectural choices

### Stage 3: Mature Decisions
- Microservices architecture
- Advanced scaling patterns
- Complex operational decisions

## Maintenance

### Updating Decisions
- When evolution triggers are met
- When assumptions change
- When new options become viable

### Archiving Decisions
- Keep old decisions for historical context
- Mark superseded decisions clearly
- Maintain decision evolution chain

## Decision Map Index

Maintain an index file (`architecture/decisions/README.md`) that lists:
- All decisions with numbers
- Brief description of each
- Current status (active/superseded)
- Links to related decisions
