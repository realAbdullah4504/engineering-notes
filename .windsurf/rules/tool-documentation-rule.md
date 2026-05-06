---
trigger: always_on
---
# Tool Documentation Rule

## Purpose

This rule defines the standard template and structure for documenting tools in the engineering notes system. Every tool documentation must follow this template to ensure consistency, completeness, and decision-making clarity.

## Template Structure

Every tool documentation MUST include the following sections in order:

```markdown
# <Tool Name>

---

## 1. Problem Statement

**What problem does this tool solve in systems?**

- What gap exists without this tool?
- Why does it matter in scalable systems?
- What type of systems typically need this?

---

## 2. When to Use (Decision Criteria)

**Use this tool when:**
- Condition 1
- Condition 2
- Condition 3

**Do NOT use when:**
- Simpler alternative exists
- Overengineering risk
- Specific anti-patterns

---

## 3. Core Concepts

| Concept | Description |
|--------|-------------|
| Concept 1 | Explanation |
| Concept 2 | Explanation |
| Concept 3 | Explanation |

---

## 4. Architecture Role (System Design View)

**Where does this tool sit in the system?**

[Client/API] → [Service] → [Tool] → [Worker/Processor] → [Database]

- Role in system:
- Data flow:
- Interaction with other components:

---

## 5. Execution Model

- Sync / Async:
- Push vs Pull:
- Event-driven / Request-driven:
- Stateful / Stateless:

---

## 6. Scaling Model

**How does this tool scale?**

- Vertical scaling:
- Horizontal scaling:
- Bottlenecks:
- Throughput behavior:

---

## 7. Setup (Minimal Working Setup)

### Example Config
```yaml
# minimal config
```

### Example Code

```python
# minimal usage
```

---

## 8. Key Patterns

Patterns enabled by this tool:

- Queue-based processing
- Fanout / Pub-Sub
- Retry mechanisms
- Backpressure handling
- Scheduling

---

## 9. Trade-offs

| Pros | Cons |
|------|------|
| Advantage 1 | Limitation 1 |
| Advantage 2 | Limitation 2 |

---

## 10. Failure Scenarios & Handling

- What happens if worker dies?
- What happens if tool crashes?
- Retry strategies:
- Data loss risks:
- Observability needs:

---

## 11. Observability & Monitoring

- Metrics to track:
- Logs:
- Alerts:
- Tools used:

---

## 12. Production Considerations

- Security:
- Config management:
- Resource usage:
- Cost considerations:
- Deployment strategy:

---

## 13. Integration with Your Stack

How YOU will use this (important for your roadmap):

- In current project:
- In future Stage (Stage 3 / 4 / 5):
- With which tools (AWS / Docker / etc):

---

## 14. Real Use Cases

- Use case 1
- Use case 2
- Use case 3

---

## 15. Anti-Patterns

- Misuse 1
- Misuse 2
- Misuse 3

---

## 16. Commands & Debugging

```bash
# useful commands
```

---

## 17. Summary (Mental Model)

1–2 lines max:

"`<Tool>` is used for solving complex problems by enabling efficient processing in distributed systems."

## Section Requirements

### Mandatory Sections (Must Complete)
- **Problem Statement** - Clear articulation of the problem
- **When to Use** - Decision criteria with specific conditions
- **Core Concepts** - Table format with key concepts
- **Architecture Role** - System diagram and component interaction
- **Execution Model** - All execution characteristics
- **Scaling Model** - Complete scaling analysis
- **Setup** - Working configuration and code examples
- **Summary** - One-sentence mental model

### Context-Dependent Sections
- **Key Patterns** - Only if tool enables specific patterns
- **Trade-offs** - Only if there are meaningful trade-offs
- **Failure Scenarios** - Only if tool has failure modes
- **Observability** - Only if monitoring is relevant
- **Production Considerations** - Only if production deployment applies
- **Integration** - Only if tool integrates with your stack
- **Use Cases** - Only if there are specific use cases
- **Anti-Patterns** - Only if there are common misuses
- **Commands** - Only if there are useful CLI commands

## Quality Standards

### Content Requirements
- **Decision-focused**: Each section must help with decision-making
- **System-oriented**: Focus on system role, not just features
- **Practical**: Include working examples, not theory
- **Comparative**: Always consider alternatives and trade-offs

### Style Requirements
- **Concise**: Each section should be 2-4 bullet points maximum
- **Actionable**: Information should be immediately useful
- **Specific**: Avoid generic statements, be concrete
- **Complete**: Cover all decision aspects for the tool

## Enforcement

### When Creating New Tool Docs
1. Use this template exactly as provided
2. Complete all mandatory sections
3. Include context-dependent sections only if relevant
4. Review for decision-making clarity

### When Updating Existing Tool Docs
1. Map existing content to this template
2. Fill in missing mandatory sections
3. Add decision criteria and system role clarity
4. Remove redundant or theoretical content

## File Organization

### Location

- Tool documentation should be placed in `tools/` directory
- Use kebab-case filenames: `tool-name.md`
- Group related tools in subdirectories: `tools/backend/`, `tools/frontend/`

### Cross-References
- Link to related tools in "When to Use" section
- Reference in `tools/tool-registry.md`
- Include in relevant architecture documentation

## Examples

### Good Example
```markdown
## 2. When to Use (Decision Criteria)

**Use this tool when:**
- Processing >1000 tasks per hour
- Tasks require retry logic and failure handling
- Need task prioritization and scheduling

**Do NOT use when:**
- Simple synchronous processing suffices
- Task volume <100 per hour
- No failure recovery needed
```

### Bad Example

```markdown
## 2. When to Use

Use Celery when you need background processing.
```

## Review Checklist

Before marking a tool doc as complete:

- [ ] Problem statement clearly identifies the gap
- [ ] Decision criteria are specific and actionable
- [ ] Architecture role shows system integration
- [ ] Scaling model addresses bottlenecks
- [ ] Setup examples work out of the box
- [ ] Trade-offs consider alternatives
- [ ] Summary captures the essence in 1-2 lines
- [ ] All sections follow the template structure
- [ ] Content is decision-focused, not feature-focused
