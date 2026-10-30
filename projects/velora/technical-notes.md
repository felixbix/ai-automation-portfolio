# VELORA — Technical Architecture and Engineering Notes

## 1. Project Purpose

VELORA is a database-oriented AI hotel operations project built around a fictional luxury hotel, VELORA Grand Dubai.

It explores how n8n, PostgreSQL, and AI-assisted communication can support hotel enquiries, reservation workflows, guest information, and operational services.

The central engineering objective is to combine AI-assisted interpretation with structured database records and deterministic business logic.

## 2. Technology Stack

- **n8n:** Workflow orchestration and application integration.
- **PostgreSQL:** Relational database and persistent operational state.
- **Neon:** Hosted PostgreSQL development environment.
- **Telegram:** Planned guest communication interface.
- **Gemini / LLM integration:** Intended support for natural-language understanding and guest communication.

## 3. Database Engineering

The PostgreSQL schema was developed in stages in a Neon development environment.

The schema foundation covers:

- Room categories and physical room records.
- Rate rules and inventory blocks.
- Guest and conversation records.
- Booking requests and reservation records.
- Reservation price components.
- Guest service requests.
- Hotel policies and knowledge articles.
- Processed events, audit logs, and operation records.

### Room Types and Physical Rooms

A room category and an individual hotel room represent different things.

For example, Deluxe is a room category, while Room 204 is a specific physical room belonging to that category.

This distinction supports structured inventory management and more accurate reservation logic.

### Database as the Source of Truth

AI-generated text should not determine operational facts such as room availability, reservation status, or pricing.

Those decisions should be based on validated database records and explicit application rules.

## 4. Data Integrity and Reliability

The architecture emphasizes several important engineering principles.

### Relational Integrity

Primary keys, foreign keys, uniqueness constraints, and appropriate validation rules help maintain consistent relationships between records.

### Event Idempotency

Incoming events should be identified using stable identifiers and database-backed safeguards to reduce duplicate processing and unintended side effects.

This design should be validated under retries and concurrent execution.

### Transaction Safety

Reservation-related operations should use appropriate database transactions and constraints to protect operational state from inconsistent updates.

### Auditability

Audit logs and operational records provide a foundation for investigating important system actions and understanding workflow outcomes.

## 5. Intended Workflow Responsibilities

The planned hotel operations workflow separates responsibilities:

1. Receive and normalize guest messages.
2. Validate incoming data.
3. Identify the guest's request type.
4. Retrieve relevant hotel policies or operational records.
5. Apply deterministic rules to reservation and service decisions.
6. Persist validated outcomes.
7. Return a response or request human review when appropriate.

This is the intended architecture, not a claim that every workflow stage is already operating.

## 6. Database Development and Implementation

The PostgreSQL schema foundation was created in stages using Neon.

The project includes table structures covering inventory, guests, conversations, reservations, hotel knowledge, service requests, and operational tracking.

The database work establishes the foundation for later n8n integration.

The complete end-to-end guest experience remains in development.

## 7. Important Engineering Decisions

- Keep room categories separate from physical rooms.
- Use PostgreSQL for persistent operational facts.
- Separate booking requests from reservation records.
- Track incoming events to support duplicate-prevention controls.
- Use AI for interpreting messages rather than making unsupported operational decisions.
- Apply appropriate review and approval controls before sensitive reservation actions.

## 8. Known Limitations

VELORA is a portfolio project, not a live hotel deployment.

- The complete guest-facing Telegram workflow remains in development.
- Live hotel inventory and availability are not connected.
- Production reservation processing is not claimed.
- Payment processing is not implemented.
- Additional testing is required for concurrency, transaction recovery, and integrated workflow behavior.

## 9. Future Engineering Priorities

1. Validate schema relationships and database constraints.
2. Implement and test Telegram guest intake.
3. Develop hotel policy retrieval.
4. Build database-backed availability and reservation workflows.
5. Implement guest-service routing and human escalation.
6. Test duplicate events, concurrent requests, and recovery scenarios.
7. Document reproducible test cases and observed results.

## Summary

VELORA demonstrates a database-first approach to AI hotel operations. Its engineering focus is on structured relational data, data integrity, event processing, controlled automation, and reliable operational decisions.

The project aims to show how AI can improve communication while PostgreSQL and deterministic business logic protect the integrity of hotel operations.
