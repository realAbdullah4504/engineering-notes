---
trigger: always_on
---
# Engineering Notes System Rule

This workspace maintains a centralized engineering knowledge system:

engineering-notes/

Its purpose is to store **reusable engineering intelligence**, not project documentation.

This system trains one skill:

> “How engineers classify and reuse technical knowledge across systems.”

---

# 1. Core Mental Model (IMPORTANT)

Every piece of knowledge MUST be classified into one of these 4 layers:

## 🧠 TOOL
WHAT you use

- external libraries
- services
- frameworks
- APIs

Example:
- reCAPTCHA
- Express.js
- Docker
- Nginx

---

## ⚙️ PATTERN
HOW you solve a problem

- reusable solution logic
- system behavior patterns
- design strategies

Example:
- rate limiting strategy
- file-based persistence
- async queue processing
- API integration flow

---

## 🏗️ ARCHITECTURE
HOW systems are structured

- system-level design
- scalability design
- evolution of systems

Example:
- monolith vs microservices
- event-driven systems
- system scaling stages

---

## 🔧 INFRASTRUCTURE
HOW systems run in real world

- deployment
- networking
- runtime environment
- DevOps configuration patterns

Example:
- Nginx reverse proxy
- Docker deployment
- SSL setup
- load balancing

---

# 2. Classification Rule (CRITICAL)

When adding any knowledge, ALWAYS ask:

1. Is it WHAT I USE? → TOOL
2. Is it HOW I SOLVE IT (tool-agnostic)? → PATTERN
3. Is it SYSTEM STRUCTURE? → ARCHITECTURE
4. Is it DEPLOYMENT/RUNTIME? → INFRASTRUCTURE

If unclear → default to PATTERN

---

# 3. Tools Layer

Location:
engineering-notes/tools/

Rules:
- defines external systems only
- no system design logic inside tools
- must include:
  - purpose
  - when to use
  - when NOT to use
  - integration pattern
  - risks/security
  - example usage

---

# 4. Patterns Layer

Location:
engineering-notes/patterns/

Rules:
- describes reusable solution logic
- must NOT mention specific project names
- must be abstract and transferable
- should explain system behavior

Examples:
- async processing pattern
- API gateway pattern
- retry + fallback pattern
- file-based persistence pattern

---

# 5. Architecture Layer

Location:
engineering-notes/architecture/

Rules:
- describes system-level design only
- focuses on scalability, structure, evolution
- includes tradeoffs and decision logic

Includes:
- system evolution stages
- distributed system design
- scaling strategies
- architecture comparisons

---

# 6. Infrastructure Layer

Location:
engineering-notes/infrastructure/

Rules:
- deployment and runtime only
- must be environment-agnostic
- must be reusable templates

Includes:
- Nginx
- Docker
- CI/CD pipelines
- SSL setup
- networking

---

# 7. Decision Map (IMPORTANT BRAIN LAYER)

engineering-notes/architecture/decision-map.md

This file defines:

> WHY a tool/pattern/architecture is chosen

It connects:
- Tool → Pattern → Architecture → Infrastructure

---

# 8. AI Routing Rule

When adding knowledge:

IF it is WHAT I USE → tools/
IF it is HOW I SOLVE → patterns/
IF it is SYSTEM STRUCTURE → architecture/
IF it is DEPLOYMENT → infrastructure/

If it does not generalize → DO NOT store it

---

# 9. Golden Rule

engineering-notes is NOT documentation.

It is an **engineering reasoning system**.

Its goal is:

> To train consistent decision-making across all systems.

---

# 10. Folder Structure

engineering-notes/

├── tools/
├── patterns/
├── architecture/
├── infrastructure/
└── README.md

---

# 11. Final Principle (MOST IMPORTANT)

Do not think:

❌ “Where do I store this?”

Think:

✔ “What role does this play in a system?”