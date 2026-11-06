# Felix Edinoh | AI Automation Developer

I design and build practical AI-powered automation systems that connect workflows, APIs, language models and databases to solve business problems.

My focus is on building reliable systems with structured data, clear business logic, validation and failure recovery.

**Technical Focus:** n8n · JavaScript · PostgreSQL · REST APIs · Webhooks · Gemini · AI-assisted extraction · Deterministic business rules · Idempotency · Error handling · Operational monitoring

> This repository documents portfolio projects and their implementation status. It distinguishes tested prototype behavior from planned work and does not claim production deployment or customer outcomes without evidence.

---

## Featured Projects

### [CENDARA — AI Lead Intelligence](projects/cendara/README.md)

A configurable AI-assisted lead intelligence and follow-up platform built around structured event processing, lead qualification and reliable notification delivery.

**Technical highlights:**
- Multi-tenant organization and integration structure.
- PostgreSQL-backed event and lead management.
- Duplicate-event prevention and idempotency safeguards.
- Gemini-powered information extraction and classification.
- Deterministic qualification and routing rules.
- Telegram administrative notifications.
- Notification delivery tracking, failure logging and recovery safeguards.

**Status:** Active development and testing.

### [VELORA — AI Hotel Operations](projects/velora/README.md)

An AI-powered hotel operations prototype exploring structured guest-service workflows and database-backed hotel information management.

**Technical highlights:**
- PostgreSQL database foundation using Neon.
- Structured room, inventory and reservation data.
- Separation of database facts from AI-generated responses.
- Workflow architecture for guest-service automation.

**Status:** Prototype development.

### [ATLAS — AI Travel Agent](projects/atlas/README.md)

A conversational travel-planning prototype using n8n, Gemini, Telegram and structured travel data.

**Technical highlights:**
- Conversational travel requirement collection.
- AI-assisted extraction of trip information.
- Structured itinerary generation.
- Conversation state and input validation.
- Booking actions designed around user approval.

**Status:** Prototype in development.

---

## Engineering Approach

1. Understand the business problem and define expected system behaviour.
2. Validate incoming data before processing.
3. Use AI for language understanding and deterministic rules for consistent decisions.
4. Store persistent information in structured databases.
5. Design for duplicate events, failures, retries and human review.
6. Verify functionality through workflow executions and database queries.
7. Document limitations, test results and future improvements.

---

## Technical Skills

| Area | Technologies and Concepts |
|---|---|
| Automation | n8n, workflow orchestration |
| Programming | JavaScript |
| Databases | PostgreSQL, SQL, Neon |
| Integrations | REST APIs, webhooks, JSON |
| AI | Gemini, extraction, classification |
| Messaging | Telegram, Gmail |
| Reliability | Validation, idempotency, retries, error handling, logging |

---

## Repository Structure

```text
ai-automation-portfolio/
│
├── README.md
│
└── projects/
    ├── atlas/
    │   └── README.md
    │
    ├── velora/
    │   └── README.md
    │
    └── cendara/
        └── README.md
```

---

## Connect

- **Portfolio:** https://felixbix.github.io/
- **LinkedIn:** https://www.linkedin.com/in/felix-edinoh-85438331a/
- **X:** https://x.com/Felix_bix
- **Email:** eddymeyoncy5@gmail.com

---

## Security

Never commit API keys, Telegram bot tokens, database credentials, `.env` files or private customer information.

Use environment variables, sanitized examples and synthetic test data in public demonstrations.
