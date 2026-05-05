# 🧠 Engineering Mental Model: Tools → Patterns → Architecture

This document defines the core mental model for structuring engineering knowledge and building systems.

---

# 1. Core Model


TOOLS → PATTERNS → ARCHITECTURE


Or:


Tools = WHAT you use
Patterns = HOW you solve problems
Architecture = HOW everything is structured together


---

# 2. Tools Layer (Execution Layer)

## Definition
Concrete technologies, libraries, or services used to implement systems.

## Characteristics
- Replaceable
- Technology-specific
- Implementation-focused

## Examples
- Redis
- reCAPTCHA v3
- Express.js
- Docker
- Nginx

## Key Insight
Tools execute logic but do NOT define system behavior.

You can swap tools without changing the core system design.

---

# 3. Patterns Layer (Decision Layer)

## Definition
Reusable solutions to common engineering problems.

## Characteristics
- Tool-independent
- Define flow and decision-making
- Reusable across systems

## Examples
- Session Management Pattern
- Retry with Backoff Pattern
- Queue-Based Processing Pattern
- Risk-Based Request Filtering Pattern

## Pattern Flow (Generic)


Request → Evaluate → Decide → Act


## Key Insight
Patterns define HOW a problem is solved, not WHAT tool is used.

---

# 4. Architecture Layer (Structure Layer)

## Definition
High-level system structure that organizes components and patterns.

## Characteristics
- Defines system boundaries
- Defines component interaction
- Combines multiple patterns

## Examples
- Monolith Architecture
- Microservices Architecture
- Event-Driven Architecture
- Redis-backed Session Architecture

## Key Insight
Architecture defines WHERE things live and HOW they connect.

---

# 5. Full System Relationship


ARCHITECTURE
↓ defines structure

PATTERNS
↓ define behavior inside structure

TOOLS
↓ execute the behavior


---

# 6. Real Example: Session System

## Architecture
Session-based authentication system

Components:
- API server
- Session store
- Middleware

## Pattern
Session Management Pattern

Flow:

Request → Load Session → Validate → Attach User → Continue


## Tools
- Redis (storage)
- Express middleware
- Cookies

---

# 7. Real Example: Bot Protection

## Architecture
Public API protection system

## Pattern
Risk-Based Request Filtering Pattern

Flow:

Request → Risk Score → Decision → Allow / Block


## Tools
- reCAPTCHA v3
- Rate limiter
- WAF

---

# 8. Classification Rules (CRITICAL)

When documenting anything, classify it using:

## Tool
- Is it a technology or service?
- Can it be installed or integrated?

→ Put in `tools/` 

---

## Pattern
- Is it a reusable logic or flow?
- Does it describe decision-making?

→ Put in `patterns/` 

---

## Architecture
- Does it describe system structure?
- Does it connect multiple components?

→ Put in `architecture/` 

---

# 9. Golden Rule


Tools = execution
Patterns = behavior
Architecture = structure


---

# 10. Senior Engineering Workflow

Always think in this order:

1. Design Architecture
2. Choose Patterns
3. Select Tools

---

# 11. Common Mistakes to Avoid

❌ Writing tool-specific logic inside patterns  
❌ Treating tools as architecture  
❌ Mixing project-specific code into patterns  
❌ Designing systems starting from tools  

---

# 12. Final Insight

You are not building features.

You are building:

- Structured systems (Architecture)
- With reusable logic (Patterns)
- Using interchangeable tools (Tools)
