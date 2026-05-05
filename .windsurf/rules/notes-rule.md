# Engineering Notes System Rule

This workspace maintains a centralized engineering knowledge system called:

engineering-notes/

Its purpose is to store reusable engineering knowledge across all projects and eliminate duplication of technical understanding.

---

# 1. System Layers (ONLY GLOBAL KNOWLEDGE)

This system contains ONLY reusable engineering knowledge.

There is NO project-specific documentation layer.

Everything stored here MUST be reusable across systems.

---

## 🧠 Tools Layer

All external tools, libraries, and services MUST be documented in:

engineering-notes/tools/

Includes:

- reCAPTCHA v3
- Express.js
- Docker
- Playwright
- Nodemailer
- Helmet
- CORS
- Nginx

Each tool must define:

- Purpose
- When to use
- When NOT to use
- Integration pattern
- Security considerations
- Example usage

---

## ⚙️ Infrastructure Layer

All infrastructure and DevOps patterns MUST be stored in:

engineering-notes/infrastructure/

Includes:

- Nginx reverse proxy patterns
- SSL setup (Certbot)
- Docker deployment patterns
- Load balancing strategies
- Rate limiting at gateway level

Rule:
→ Must be environment-agnostic and reusable  
→ No domain-specific configuration allowed  

---

## 🧠 System Design Patterns Layer

All reusable architectural patterns MUST be stored in:

engineering-notes/patterns/

Includes:

- File-based storage (JSONL/CSV)
- Queue-based processing pattern
- External API integration pattern
- PDF generation (HTML → PDF)
- Rate limiting architecture pattern

Rule:
→ Must describe conceptual system behavior  
→ NOT implementation for any specific application  

---

# 2. Separation Rule (CRITICAL)

Everything in engineering-notes MUST be:

✔ Reusable  
✔ Generic  
✔ Independent of any project  
✔ Abstracted from implementation details  

---

# 3. Tool Documentation Rule

Each tool entry MUST follow this structure:

- What it is
- Problem it solves
- When to use it
- When NOT to use it
- Integration pattern
- Security considerations
- Example usage

---

# 4. Infrastructure Rule

Infrastructure entries MUST:

- Be reusable templates
- Avoid domain or project naming
- Focus on patterns, not configs
- Represent standard deployment strategies

---

# 5. System Pattern Rule

Patterns define HOW systems behave, not how a specific app works.

Examples:

- Stateless API design
- File-based persistence strategy
- Async processing patterns
- External service integration flow

---

# 6. AI Routing Rule

When adding knowledge:

IF it is a tool → tools/  
IF it is infrastructure → infrastructure/  
IF it is a system concept → patterns/  

If it is not reusable → DO NOT include it

---

# 7. Golden Principle

engineering-notes is NOT a project documentation system.

It is a **universal engineering knowledge system**.

Everything stored here must help solve problems across multiple systems.