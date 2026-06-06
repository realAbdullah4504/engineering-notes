---
trigger: always_on
---
# Pattern Documentation Rule

## Purpose
This rule defines the standard template and structure for documenting patterns in the engineering notes system. Every pattern document must follow this template to ensure clear problem-solution mapping and practical implementation guidance.

## Template Structure

Every pattern documentation MUST include the following sections in order:

```markdown
# 🧩 <Pattern Name> Pattern

---

## 1. Problem Statement

**What problem does this pattern solve?**

- What breaks without this pattern?
- In what situations does this problem appear?
- Why is it important at scale?

---

## 2. Pattern Overview

**What is this pattern (in simple terms)?**

> "This pattern enables ______ by ______."

- Core idea:
- Key benefit:

---

## 3. When to Use (Decision Criteria)

**Use this pattern when:**
- Condition 1
- Condition 2
- Condition 3

**Avoid this pattern when:**
- Simpler solution exists
- Adds unnecessary complexity

---

## 4. Architecture Representation

```
[Producer]
↓
[Queue / Broker]
↓
[Consumer / Worker]
```

- Explain each component
- Explain flow

---

## 5. Core Concepts

| Concept | Description |
|--------|-------------|
| Concept 1 | Explanation |
| Concept 2 | Explanation |
| Concept 3 | Explanation |

---

## 6. Execution Flow

**Step-by-step:**

1. Step 1
2. Step 2
3. Step 3
4. Step 4

---

## 7. Variations of the Pattern

- Variation 1 (e.g., Single queue)
- Variation 2 (Fanout)
- Variation 3 (Priority queues)
- Variation 4 (Delayed jobs)

---

## 8. Real-World Mapping

**How this pattern maps to tools:**

| Pattern Concept | Tool Example |
|----------------|-------------|
| Queue | Redis / SQS / Kafka |
| Worker | Celery / BullMQ |
| Producer | API / Service |

---

## 9. Scaling Behavior

- Horizontal scaling:
- Throughput handling:
- Bottlenecks:
- Backpressure handling:

---

## 10. Trade-offs

| Pros | Cons |
|------|------|
| Scalable | Increased complexity |
| Async processing | Debugging harder |

---

## 11. Failure Scenarios

- What if worker crashes?
- What if queue is overloaded?
- What if message is lost?

**Handling strategies:**
- Retries
- DLQ (Dead Letter Queue)
- Idempotency

---

## 12. Observability

- Metrics:
- Logs:
- Alerts:

---

## 13. Anti-Patterns

- Misuse 1
- Misuse 2
- Misuse 3

---

## 14. Integration with Your Systems

**Where you will use this pattern:**

- Current project:
- Future use:
- With which tools:

---

## 15. Example Use Cases

- Background job processing
- Email sending
- Video processing
- Data pipelines

---

## 16. Evolution Path (VERY IMPORTANT)

**How this pattern evolves:**

1. Simple (Sync processing)
2. Add queue
3. Add multiple workers
4. Add fanout / pub-sub
5. Move to event-driven architecture
```

## Section Requirements

### Mandatory Sections (Must Complete)
- **Problem Statement** - Clear problem definition and context
- **Pattern Overview** - Simple explanation with core benefit
- **When to Use** - Decision criteria for pattern application
- **Architecture Representation** - Visual diagram and component explanation
- **Core Concepts** - Key concepts in table format
- **Execution Flow** - Step-by-step process
- **Real-World Mapping** - Tool mapping table
- **Trade-offs** - Pros and cons analysis
- **Evolution Path** - Pattern maturation stages

### Context-Dependent Sections
- **Variations** - Only if pattern has meaningful variations
- **Scaling Behavior** - Only if scaling is relevant
- **Failure Scenarios** - Only if pattern has failure modes
- **Observability** - Only if monitoring is needed
- **Anti-Patterns** - Only if there are common misuses
- **Integration** - Only if pattern applies to your systems
- **Use Cases** - Only if there are specific applications

## Quality Standards

### Content Requirements
- **Problem-focused**: Clearly articulate the problem before solution
- **Decision-driven**: Provide clear criteria for when to use
- **Tool-agnostic**: Focus on pattern concepts, not specific tools
- **Evolution-aware**: Show how patterns mature over time

### Style Requirements
- **Simple language**: Explain complex concepts simply
- **Visual diagrams**: Use ASCII/mermaid for architecture
- **Structured tables**: Use tables for concepts and mappings
- **Step-by-step flows**: Break execution into clear steps

## Enforcement

### When Creating New Pattern Docs
1. Use this template exactly as provided
2. Complete all mandatory sections
3. Include context-dependent sections only if relevant
4. Ensure problem statement is clear and specific
5. Include at least 3 evolution stages

### When Updating Existing Pattern Docs
1. Map existing content to this template
2. Fill in missing mandatory sections
3. Add decision criteria if missing
4. Include tool mapping for practical application
5. Update evolution path with real experience

## File Organization

### Location
- Pattern documentation should be placed in `patterns/` directory
- Use kebab-case filenames: `pattern-name.md`
- Group by category: `patterns/guides/`, `patterns/methodology/`, `patterns/reference/`

### Cross-References
- Link to related patterns in variations section
- Reference in `patterns/README.md`
- Include in architecture and infrastructure docs

## Diagram Standards

### Architecture Representation
- Use simple ASCII art or mermaid diagrams
- Show clear flow direction
- Label all components
- Keep it simple and focused

### Evolution Path
- Use numbered stages
- Show progression clearly
- Include complexity indicators
- Note trigger conditions for evolution

## Concept Mapping Standards

### Core Concepts Table
- Use concept-description format
- Keep explanations concise
- Focus on pattern-specific concepts
- Avoid generic definitions

### Real-World Mapping
- Map abstract concepts to concrete tools
- Include multiple tool examples where possible
- Show tool-agnostic nature of pattern
- Note tool-specific considerations

## Review Checklist

Before marking a pattern doc as complete:

- [ ] Problem statement clearly defines the issue
- [ ] Pattern overview has simple one-sentence explanation
- [ ] When to Use section has specific decision criteria
- [ ] Architecture representation has clear diagram
- [ ] Core concepts are well-defined in table format
- [ ] Execution flow is step-by-step and complete
- [ ] Real-world mapping shows tool implementations
- [ ] Trade-offs table is balanced and honest
- [ ] Evolution path shows at least 3 stages
- [ ] All sections follow the template structure
- [ ] Content is pattern-focused, not tool-focused

## Integration with Other Rules

### Tool Documentation Rule
- Reference tool docs for real-world mapping
- Use tool decision criteria in pattern application
- Include tool-specific considerations

### Architecture Documentation Rule
- Reference patterns in architecture components
- Show how patterns enable architectural decisions
- Include pattern evolution in system scaling

### Infrastructure Documentation Rule
- Reference patterns in infrastructure design
- Show how patterns influence infrastructure choices
- Include pattern-specific infrastructure needs

## Examples

### Good Problem Statement
```markdown
## 1. Problem Statement

**What problem does this pattern solve?**

- What breaks without this pattern: Synchronous processing blocks user requests, causes timeouts
- In what situations does this problem appear: Long-running tasks (>30 seconds), high-volume processing
- Why is it important at scale: Prevents system degradation, enables horizontal scaling
```

### Bad Problem Statement
```markdown
## 1. Problem Statement

We need to process things asynchronously.
```

### Good Pattern Overview
```markdown
## 2. Pattern Overview

**What is this pattern (in simple terms)?**

> "This pattern enables non-blocking task processing by decoupling request handling from execution."

- Core idea: Separate task submission from task execution
- Key benefit: Improves system responsiveness and scalability
```

### Bad Pattern Overview
```markdown
## 2. Pattern Overview

This pattern uses queues to process tasks.
```

### Good Evolution Path
```markdown
## 16. Evolution Path (VERY IMPORTANT)

**How this pattern evolves:**

1. Simple (Sync processing) - Direct function calls, blocking
2. Add queue - Basic task queue, single worker
3. Add multiple workers - Horizontal scaling, load distribution
4. Add fanout / pub-sub - Multiple consumers, event-driven
5. Move to event-driven architecture - Full decoupling, event sourcing
```

### Bad Evolution Path
```markdown
## 16. Evolution Path

Start simple, then add more features.
```

## Pattern Categories

### Structural Patterns
- How components are organized
- Relationship between parts
- Architectural organization

### Behavioral Patterns
- How components interact
- Communication patterns
- Flow control

### Creational Patterns
- How objects are created
- Instantiation strategies
- Resource management

### Concurrency Patterns
- How parallelism is handled
- Synchronization approaches
- Resource sharing

## Common Pitfalls to Avoid

### Don't Focus on Tools
- Patterns = "What and why"
- Tools = "How"
- Keep patterns tool-agnostic

### Don't Skip Decision Criteria
- Always include when to use/avoid
- Be specific about conditions
- Consider complexity trade-offs

### Don't Forget Evolution
- Show how patterns mature
- Include complexity growth
- Note transition triggers

## Pattern Evolution Tracking

### Version Control
- Tag pattern versions in git
- Use semantic versioning for major changes
- Include migration guides between stages

### Community Contributions
- Document pattern usage in real projects
- Collect lessons learned
- Update based on practical experience

## Pattern Validation

### Real-World Testing
- Document actual implementations
- Include performance metrics
- Note unexpected challenges

### Peer Review
- Get feedback from other engineers
- Validate decision criteria
- Check for missing variations

## Teaching and Learning

### Pattern Education
- Use patterns for teaching concepts
- Create pattern-based exercises
- Build pattern libraries

### Pattern Recognition
- Train engineers to spot patterns
- Develop pattern identification skills
- Create pattern decision trees
