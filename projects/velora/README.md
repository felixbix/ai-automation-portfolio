# VELORA — AI Hotel Operations System

## Project Overview

VELORA is a portfolio project exploring how AI automation and structured database design can support hotel operations. The concept is based on a fictional luxury property, VELORA Grand Dubai, with a planned capacity of 150 rooms and pricing represented in AED.

The project uses n8n and PostgreSQL to explore reliable guest-service workflows, reservation logic, and operational data management.

**Project type:** AI automation and database engineering portfolio project  
**Status:** In development  
**Primary goal:** Design a reliable foundation for hotel workflows, with data integrity and controlled automation at the center.

## The Problem

Hotel operations involve connected information about guests, rooms, availability, reservations, policies, and service requests. If these records are inconsistent, automated responses may provide incorrect information or create operational problems.

VELORA explores how a database-backed automation system can keep operational records structured and make PostgreSQL the source of truth for hotel data, rather than relying on an AI model to decide facts such as room availability.

## Proposed Solution

VELORA is being designed around PostgreSQL for persistent operational data and n8n for workflow orchestration.

The planned guest-facing interface is Telegram. The broader design explores how incoming guest messages could be validated, matched to the appropriate intent, answered using hotel information, or routed into reservation and service-request workflows.

AI can help interpret natural-language messages, while deterministic business logic and database constraints should govern operational decisions.

## Technology Stack

- **n8n:** Workflow orchestration, conditional logic, and integrations.
- **PostgreSQL:** Persistent relational data, constraints, and operational state.
- **Neon:** Hosted PostgreSQL environment used during database development and testing.
- **Telegram:** Planned guest communication interface.
- **Gemini / LLM integration:** Potential support for intent interpretation and natural-language responses within the broader workflow design.

## Database Architecture

A central part of the project is designing structured records for hotel operations.

The schema foundation developed in PostgreSQL includes areas such as:

- **Hotel inventory:** Room types, physical rooms, rate rules, and inventory blocks.
- **Guests and communication:** Guest records, conversations, and messages.
- **Reservations:** Booking requests, reservations, and reservation price items.
- **Guest services:** Service requests and hotel policies.
- **Reliability and traceability:** Processed events, audit logs, and operation records.

The design separates room categories from individual physical rooms. This supports modeling room types and the actual rooms belonging to each category.

Database constraints and transaction-safe logic are important design considerations for protecting data integrity and reducing the risk of duplicate or conflicting reservation operations.

## Intended Workflow

The project is designed to progress toward the following workflow:

1. Receive a guest message through Telegram.
2. Normalize and validate the incoming event.
3. Identify whether the guest is asking about hotel information, availability, a reservation, or a service request.
4. Retrieve relevant policy or operational information from the appropriate data source.
5. Apply deterministic business rules for decisions that depend on database records.
6. Create or update operational records only when validation and business conditions are satisfied.
7. Return a response to the guest or route the request for human review.
8. Record relevant outcomes to support debugging and operational monitoring.

This describes the intended workflow architecture; it should not be interpreted as confirmation that every stage is already implemented.

## Engineering Principles

### Database as the Source of Truth

Operational facts such as room inventory and reservation state should come from validated database records, not from generated text alone.

### Data Integrity

Relational structure, constraints, and appropriate transaction handling should protect records from inconsistent updates and conflicting operations.

### Duplicate Prevention

Incoming events should be tracked using stable identifiers and database-backed idempotency controls. This helps prevent an event from being processed repeatedly when a delivery is retried.

### Controlled Automation

Actions that affect reservations or guest commitments should follow explicit business rules. Sensitive or ambiguous cases should be routed for appropriate review rather than handled through unsupported assumptions.

### Observability

Event records, audit trails, and operation records are intended to support troubleshooting and make workflow outcomes easier to investigate.

## Current Implementation Status

The PostgreSQL schema foundation was created and tested in a Neon development database in stages.

The current work establishes the database-oriented foundation for the project. The complete end-to-end guest experience, including reservation processing and guest-service automation, remains in development.

The project has no real hotel partner and does not claim live hotel inventory, live rates, payment processing, or production reservations.

## Current Development Priorities

1. Finalize and validate schema relationships and database constraints.
2. Build Telegram intake, normalization, and validation in n8n.
3. Implement hotel FAQ and policy retrieval.
4. Develop availability and reservation workflows with duplicate prevention and appropriate transaction handling.
5. Add service-request routing, human escalation, and operational logging.
6. Test expected behavior, failure scenarios, and recovery paths.

## Skills Demonstrated

- Relational database design
- PostgreSQL and SQL
- Database constraints and data integrity
- Workflow architecture with n8n
- Event processing and idempotency design
- Operational state management
- AI integration planning
- Technical documentation and incremental testing
- 
## Technical Documentation

- [Hotel Operations Architecture](architecture.md) — diagrams of the planned guest interaction and reservation workflows, plus the database foundation and reliability principles.
- - [PostgreSQL Schema Design](schema-design.md) — documents the database structure, inventory and reservation modeling, data integrity principles, and current implementation status.

## Summary

VELORA explores how AI-assisted communication can be combined with database-backed business logic to support hotel operations. Its central engineering principle is that AI can help interpret a guest's request, but operational decisions must be grounded in validated data, explicit rules, and reliable state management.
