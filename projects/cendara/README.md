# CENDARA — AI Lead Intelligence & Business Automation

## Project Overview

CENDARA is a configurable AI-assisted lead intelligence and business automation platform built with n8n, PostgreSQL, Neon and Google Gemini.

It explores how unstructured business enquiries can be transformed into structured lead records, evaluated against organization-specific qualification rules, and routed for appropriate follow-up.

The project combines AI-powered information extraction with deterministic business logic and database-backed reliability controls.

Rather than focusing only on successful workflow execution, CENDARA also addresses duplicate-event prevention, persistent event tracking, notification delivery, failure logging and recovery.

**Project type:** AI automation and lead intelligence  
**Status:** Active development and testing  
**Primary goal:** Build a configurable foundation for processing, qualifying and routing business leads reliably.

---

## Workflow Overview

The following screenshot illustrates the CENDARA Lead Intelligence Worker in n8n and its connected processing stages.

![CENDARA workflow overview](Screenshot%202026-10-30%20003507.png)

---

## The Problem

Business enquiries frequently arrive as unstructured messages containing incomplete information about customer needs, budget, urgency and requested services.

Manually reviewing these enquiries can be repetitive and make it difficult to prioritize important opportunities.

However, relying entirely on AI-generated decisions introduces another challenge: business decisions may become inconsistent or difficult to audit.

CENDARA addresses this through a hybrid approach:

- AI interprets natural-language enquiries.
- Deterministic business rules evaluate extracted information.
- PostgreSQL maintains structured records and processing state.
- Automation workflows handle routing, notifications and failure recovery.

---

## Solution

CENDARA receives incoming enquiries through Telegram, validates and normalizes the relevant information, and processes the event through a structured workflow.

Gemini assists with extracting information from natural-language messages. A separate qualification layer applies configured business rules to determine the appropriate lead outcome.

Lead information, event-processing state and notification-delivery records are persisted in PostgreSQL.

The architecture separates the fast intake and acknowledgement path from the more involved lead-intelligence processing path.

This allows the system to acknowledge incoming enquiries without making the initial response dependent on completion of the entire AI-processing workflow.

---

## Technology Stack

| Technology | Purpose |
|---|---|
| n8n | Workflow orchestration, branching and recovery |
| PostgreSQL | Persistent lead, event and notification records |
| Neon | Hosted PostgreSQL development environment |
| Google Gemini | AI-assisted information extraction and classification |
| JavaScript | Data normalization, transformation and workflow logic |
| Telegram | Enquiry intake and administrative notifications |
| SQL | Database operations, constraints and integrity checks |

---

## System Architecture

### 1. Event Intake and Validation

Incoming Telegram events are normalized into a consistent structure containing the relevant identifiers and message content.

Validation checks ensure that required information is present before an event proceeds.

The fast intake workflow also provides an acknowledgement to the sender.

### 2. Event Registration and Duplicate Prevention

Incoming events are registered in PostgreSQL with organization, channel and external event identifiers.

The processed-events mechanism helps identify previously received events and prevents duplicate processing paths from creating unwanted side effects.

Database constraints provide additional safeguards around organization-scoped event and lead relationships.

A duplicate-event test confirmed that a previously registered event was identified as an existing event rather than a new one.

### 3. AI-Powered Lead Extraction

Gemini assists with extracting structured information from natural-language enquiries.

The extraction stage covers fields such as:

- Customer intent level
- Urgency
- Whether a budget was provided
- Budget amount, when available
- Requested service
- Concise enquiry summary
- Timeline
- Missing information requiring clarification

The extracted information becomes structured input for the qualification layer.

### 4. Deterministic Qualification and Routing

CENDARA separates AI-based interpretation from business qualification decisions.

The qualification engine evaluates extracted information against organization-specific rules.

Examples of configured outcomes include:

- Urgent enquiries can be flagged for human review.
- High-intent enquiries with budget information can qualify for a higher-priority outcome.
- Unmatched cases can remain pending rather than being automatically rejected.

Qualification results are stored with the lead record, including status, score, priority and supporting qualification data.

### 5. Event Lifecycle Tracking

Event processing uses persistent database state to record progress through the workflow.

The lifecycle includes states such as:

- `received`
- `processing`
- `completed`
- `retryable`

Relevant timestamps and error information support investigation and recovery.

These controls help make processing state observable and reduce duplicate side effects. They do not guarantee exactly-once execution under every possible failure condition.

### 6. Notification Delivery and Recovery

Administrative notifications are tracked independently of lead qualification and event processing.

CENDARA maintains notification records containing delivery status, attempt count, destination and failure information.

The notification workflow includes:

- Successful delivery tracking.
- Failed delivery logging.
- A bounded retry policy of up to three attempts.
- Detection of exhausted notifications.
- Administrative failure reporting.

Separating notification state from lead-processing state makes it easier to identify whether a lead was processed successfully but its notification still requires attention.

### 7. Operational Monitoring

Database records provide visibility into event processing, lead qualification and notification delivery.

The project also explores dashboard-oriented operational metrics to help monitor system activity and investigate unexpected workflow outcomes.

Further validation of metric accuracy and dashboard completeness remains part of the development work.

---

## Engineering Principles

### AI for Interpretation, Rules for Decisions

AI helps interpret unstructured enquiries. Explicit business rules control qualification outcomes, making decisions easier to explain, test and adjust.

### Database-Backed Reliability

Persistent records, unique constraints and organization-scoped relationships help protect data integrity and reduce duplicate processing.

### Separation of Responsibilities

Lead qualification, event processing and notification delivery maintain distinct states. This makes failures easier to isolate and investigate.

### Bounded Recovery

Retry limits and failure logging provide controlled recovery paths rather than allowing unlimited retry attempts.

### Traceability

Structured event, lead, AI-processing and notification records provide evidence for debugging and reviewing workflow outcomes.

---

## Implementation and Testing

Development and testing have covered several important parts of the system.

| Test area | Verified behaviour |
|---|---|
| Event intake | Message normalization and validation |
| Duplicate events | Previously registered events identified as existing |
| Lead processing | Clean worker payload construction and validation |
| Lead creation | Database persistence and duplicate-lead safeguards |
| Qualification | Configured qualification outcomes persisted |
| Urgent notifications | Successful administrative alert delivery |
| Failure handling | Failed notification status and failure logging |
| Recovery monitoring | Identification of exhausted notification attempts |

Additional database safeguards include organization-scoped event relationships and a unique lead constraint involving the processed event identifier.

Testing also exposed inconsistencies in notification-state updates and incomplete historical event records.

The notification success update was corrected, and selected historical records were reconciled using available evidence.

Historical reconciliation remains separate from normal event processing. It does not recreate timestamps or execution histories that were never captured.

---

## Current Limitations

CENDARA remains a portfolio project under active development.

- Qualification quality depends on the accuracy of extracted information and configured business rules.
- Testing covers selected execution paths rather than every possible scenario.
- Concurrent event-processing behaviour requires further validation.
- Real-world delivery failures and extended recovery scenarios need broader testing.
- Some historical records require further review.
- Additional channel integrations and industry-specific configuration remain future development areas.
- Production deployment, customer usage and measured business outcomes have not been established.

No production customer deployment or measured business outcome is claimed.

---

## Current Development Priorities

1. Expand testing for missing fields, ambiguous enquiries and unmatched qualification rules.
2. Validate idempotency under retries and concurrent event execution.
3. Test notification delivery failures, retry limits and exhausted-retry reporting.
4. Reconcile historical records only where sufficient evidence exists.
5. Validate operational metrics against underlying database records.
6. Improve organization-level configuration and documentation.
7. Strengthen security and deployment readiness.

---

## Skills Demonstrated

- AI-assisted structured information extraction
- n8n workflow orchestration
- PostgreSQL database design and SQL
- JavaScript-based workflow logic
- Deterministic business-rule evaluation
- Event lifecycle management
- Idempotency and duplicate-prevention design
- Database constraints and data integrity
- Notification delivery tracking
- Bounded retries and failure recovery
- Operational monitoring and traceability
- Workflow debugging and testing
- Technical documentation

---

## Technical Documentation

- [Workflow Architecture Diagram](architecture.md) — visual overview of lead processing and notification recovery.
- [Technical Architecture and Testing Notes](technical-notes.md) — implementation details, debugging outcomes, reliability controls and remaining test priorities.

---

## Summary

CENDARA explores a practical engineering approach to AI-assisted lead management.

It combines AI-based information extraction, deterministic qualification, persistent database records and controlled recovery workflows.

Its central principle is that reliable automation requires more than a successful workflow execution. Systems should also validate their inputs, protect data integrity, make processing state observable and provide clear recovery paths when things go wrong.
