# CENDARA — Technical Architecture and Testing Notes

## 1. Purpose

CENDARA explores how AI-assisted information extraction, deterministic qualification rules, and database-backed workflow state can work together in a configurable lead-management system.

The project is built with n8n, PostgreSQL hosted on Neon, Google Gemini, JavaScript, and Telegram integrations.

Its engineering focus extends beyond AI-assisted lead processing to include event lifecycle management, duplicate prevention, database integrity, notification delivery, failure logging, and controlled recovery.

These notes document the system architecture, implemented controls, selected test outcomes, and known limitations. They do not claim that every failure scenario has been tested or that the system is production-ready.

**Project status:** Active development and testing.

---

## 2. High-Level Architecture

CENDARA separates incoming-event acknowledgement from the more involved lead-intelligence process.

The general processing sequence is:

1. Receive an incoming Telegram event.
2. Normalize and validate the message.
3. Verify the organization's channel integration.
4. Register the incoming event and identify duplicates.
5. Send a fast acknowledgement.
6. Construct a clean payload for the lead-processing worker.
7. Extract structured information using Gemini.
8. Evaluate the extracted information against configured qualification rules.
9. Persist the lead and qualification results.
10. Update event-processing state.
11. Create administrative notifications when configured conditions are met.
12. Track notification outcomes and handle eligible failures.

This separation allows the intake path to acknowledge a message without waiting for the entire AI-processing workflow to finish.

---

## 3. Technology Stack

| Technology | Responsibility |
|---|---|
| n8n | Workflow orchestration, branching, integrations and recovery |
| PostgreSQL | Persistent records, event state and database safeguards |
| Neon | Hosted PostgreSQL development environment |
| Google Gemini | AI-assisted information extraction and classification |
| JavaScript | Normalization, transformation and workflow logic |
| Telegram | Enquiry intake and administrative notifications |
| SQL | Database queries, constraints, state transitions and reporting |

---

## 4. Database Architecture

CENDARA uses PostgreSQL as its persistent source of truth.

The database is designed around organization-level ownership, allowing business configuration and operational records to be associated with the relevant organization.

The schema includes the following tables:

| Table | Responsibility |
|---|---|
| `organizations` | Organization records |
| `organization_settings` | Organization-level configuration |
| `channel_integrations` | Registered communication integrations |
| `processed_events` | Incoming-event identity and processing state |
| `contacts` | Contact information |
| `conversations` | Conversation records |
| `leads` | Structured lead information and qualification outcomes |
| `ai_processing_runs` | AI-processing records |
| `qualification_rules` | Configurable lead qualification rules |
| `routing_rules` | Organization-specific routing configuration |
| `follow_up_tasks` | Follow-up task records |
| `notification_deliveries` | Administrative notification delivery state |

### Organization-Level Data Integrity

Organization identifiers are used to scope relevant records and relationships.

Database constraints provide additional safeguards against invalid relationships and duplicate lead records.

One implemented safeguard uses:

- `leads.processed_event_id` as a UUID reference.
- `idx_leads_org_processed_event_unique` as a unique organization-scoped index.
- `leads_processed_event_same_org_fk` to enforce the relationship between a lead and its processed event within the same organization.

These controls help prevent the same processed event from creating multiple lead records within the same organization.

They complement application-level duplicate checks rather than replacing them.

---

## 5. Workflow Responsibilities

### 5.1 Event Intake and Validation

The intake workflow normalizes incoming Telegram messages into a consistent structure.

It extracts relevant identifiers and message content, validates required fields, and checks whether the organization has an active channel integration.

The workflow separates valid incoming events from unusable inputs so that invalid messages do not proceed through normal processing.

### 5.2 Event Registration and Idempotency

Incoming events are registered in PostgreSQL using their relevant organization, source-channel and external-event identifiers.

The workflow checks whether an incoming event has already been registered before launching the lead-processing path.

A previously registered event is identified as an existing event rather than being treated as a new one.

Database-backed safeguards provide another level of protection against duplicate lead creation.

The implementation follows an idempotency-oriented design: repeated processing attempts should not produce unintended duplicate lead records for the same organization and processed event.

This does not establish guaranteed exactly-once execution across every possible failure or concurrency scenario.

### 5.3 Fast Acknowledgement

After successful intake validation and event registration, the workflow sends an acknowledgement to the sender.

The acknowledgement is separated from the more involved AI-processing worker.

This design reduces the dependency between the initial response and slower downstream processing.

### 5.4 Lead Intelligence Worker

The lead-intelligence worker receives a normalized payload rather than relying on the original Telegram event structure.

The worker validates the supplied data before proceeding with extraction and qualification.

This creates a clearer boundary between message intake and downstream business processing.

### 5.5 AI-Assisted Information Extraction

Gemini interprets natural-language enquiries and extracts structured information.

The extraction fields include:

- `urgency`
- `intent_level`
- `budget_provided`
- `budget_amount`
- `service_requested`
- `need_summary`
- `timeline`
- `missing_information`

The extracted fields provide structured context for subsequent business logic.

AI-generated information is treated as extracted input, not automatically as verified business fact.

### 5.6 Deterministic Qualification

The qualification layer evaluates extracted information against organization-specific rules.

This keeps language interpretation separate from business decisions.

The demonstrated rule configuration includes:

- An urgent-enquiry rule that routes the lead for human review.
- A high-intent and budget-based rule that can produce a qualified outcome with higher priority.
- A low-intent rule for identifying enquiries that meet configured unqualified criteria.
- A fallback for enquiries that do not match an active rule.

The unmatched-rule fallback uses `needs_review` rather than automatically rejecting the enquiry.

Qualification results are persisted with the lead record, including qualification status, score, priority and supporting qualification data.

---

## 6. Event Lifecycle Management

CENDARA maintains persistent event-processing state in PostgreSQL.

The event lifecycle includes states such as:

- `received`
- `processing`
- `completed`
- `retryable`

The relevant workflow steps record processing transitions and timestamps.

Error information is also retained where applicable to support investigation and recovery.

Explicit state transitions help distinguish newly received events, active processing, completed work and events that may require another processing attempt.

New processing paths have been tested for status and timestamp updates.

Historical records are treated separately from normal processing. Reconciliation is performed only where supporting evidence exists; missing historical timestamps or execution histories are not fabricated.

---

## 7. Notification Delivery and Recovery

Notification delivery is tracked independently of lead qualification and event-processing status.

The `notification_deliveries` table records relevant information such as:

- Organization identifier
- Related processed event
- Notification type
- Destination
- Delivery status
- Attempt count
- Last error
- Additional notification details
- Relevant timestamps

The notification workflow supports:

1. Creating a notification delivery record.
2. Attempting Telegram delivery.
3. Recording successful delivery.
4. Recording failed delivery and available error information.
5. Retrying eligible failed notifications.
6. Identifying notifications that have exhausted the retry allowance.
7. Recording administrative failure reports where applicable.

### Bounded Retry Policy

The implemented retry design uses a maximum of three delivery attempts.

Exhausted notifications are identified for operational attention rather than being retried indefinitely.

The distinction between notification state and lead-processing state is important: a lead can be processed successfully even when its administrative notification has not been delivered.

---

## 8. Operational Monitoring

CENDARA includes database-backed operational records and dashboard-oriented metrics workflows.

The monitoring design considers:

- Total lead records
- Qualification outcomes
- Event-processing statuses
- Notification delivery statuses
- Failed and exhausted notification attempts
- Audit activity

The underlying database records remain the reference point for validating reported metrics.

Further testing is required to confirm that all dashboard metrics remain accurate across different event and recovery scenarios.

---

## 9. Selected Testing and Debugging Outcomes

Development has involved testing individual workflow stages, examining database records, identifying inconsistencies and correcting the affected logic.

### 9.1 Duplicate-Event Detection

**Purpose:** Confirm that a previously registered external event is not treated as a new event.

**Test result:** A repeated event was identified with `is_new_event = false`.

**Significance:** The duplicate-checking path correctly identified the event as already registered in the tested scenario.

### 9.2 Lead Creation and Duplicate Prevention

**Purpose:** Verify that a valid worker payload can create a lead and that duplicate-lead safeguards are enforced.

**Test result:** Lead creation and the organization-scoped processed-event uniqueness safeguard were tested.

**Significance:** The database provides additional protection against duplicate lead records associated with the same processed event.

### 9.3 Qualification Fallback

**Issue:** An enquiry that did not match a configured qualification rule could remain in a generic pending state without an explicit review outcome.

**Change:** The fallback was updated to use `needs_review`.

**Test result:** The revised fallback was exercised against test records.

**Significance:** Unmatched enquiries can be distinguished from enquiries that were explicitly qualified or rejected.

### 9.4 Event Lifecycle Tracking

**Issue:** Earlier event records did not consistently contain processing timestamps.

**Change:** Explicit workflow steps were implemented to record processing and completion transitions.

**Test result:** New processing paths were checked for the relevant status and timestamp updates.

**Historical handling:** Selected historical records were reconciled using available evidence. Missing historical execution details were not invented.

### 9.5 Notification State Consistency

**Issue:** A notification success-handling path contained an incorrect status update that marked a successful notification as failed.

**Change:** The success path was corrected to record `sent`, while the failure path records `failed` and available error information.

**Test result:** Notification delivery-state handling was tested, including a successful delivery on a later permitted attempt.

**Significance:** Notification records now distinguish successful delivery from failed delivery in the tested paths.

### 9.6 Failure Logging and Recovery Monitoring

**Purpose:** Verify that failed administrative notification attempts are recorded and can be identified for operational attention.

**Test result:** Failure logging paths and exhausted-notification monitoring were exercised.

**Significance:** Failed notification attempts can be investigated using their persistent delivery records instead of relying only on workflow execution history.

---

## 10. Reliability Controls

The system incorporates the following reliability concepts:

| Control | Purpose |
|---|---|
| Input validation | Prevent invalid input from entering normal processing |
| Persistent event records | Maintain event identity and processing state |
| Duplicate-event detection | Identify previously registered events |
| Database uniqueness constraints | Reduce duplicate lead creation |
| Organization-scoped relationships | Protect tenant-level data integrity |
| Deterministic qualification rules | Make business decisions consistent and explainable |
| Human-review fallback | Avoid automatically rejecting unmatched cases |
| Separate notification records | Distinguish lead processing from message delivery |
| Bounded retries | Prevent unlimited notification attempts |
| Failure logging | Support investigation and recovery |
| Operational metrics | Improve visibility into system behaviour |

These controls reduce specific risks but do not eliminate every possible failure.

In particular, concurrent execution, external service outages and complex recovery scenarios require further testing.

---

## 11. Known Limitations

CENDARA remains an active portfolio project.

- Qualification quality depends on extraction accuracy and rule configuration.
- Testing covers selected workflow paths rather than every possible scenario.
- Concurrent event processing requires further validation.
- Broader failure-recovery scenarios need additional testing.
- Some historical records may require evidence-based review.
- Dashboard metrics require continued comparison against underlying database records.
- Industry-specific configuration and additional channel integrations remain development areas.
- Production deployment, customer usage and measured business impact have not been established.

---

## 12. Next Testing Priorities

1. Test empty, invalid, incomplete and ambiguous incoming messages.
2. Verify every configured qualification rule and the unmatched-rule fallback.
3. Test duplicate-event handling during retries and concurrent executions.
4. Test notification success, failure, retry limits and exhausted-retry monitoring.
5. Compare dashboard metrics with direct SQL queries against source tables.
6. Verify organization-level isolation and database relationships.
7. Record reproducible test cases with expected and actual results.
8. Review security and deployment requirements before any production use.

---

## 13. Security Considerations

Before publishing workflow exports, SQL files, screenshots or configuration examples:

- Remove API keys and credentials.
- Remove Telegram bot tokens.
- Remove database connection strings.
- Exclude `.env` files.
- Sanitize private chat identifiers and personal information.
- Use synthetic test records in public demonstrations.
- Avoid publishing sensitive internal configuration.

Public project documentation should demonstrate the system's engineering design without exposing credentials or private operational data.

---

## Summary

CENDARA combines AI-assisted information extraction, deterministic business qualification, PostgreSQL-backed persistence and controlled notification recovery.

Its engineering approach emphasizes separating AI interpretation from business decisions, maintaining traceable processing state, protecting against duplicate records, and making failures observable.

The project demonstrates that reliable automation involves more than connecting services together. It requires clear system boundaries, database integrity, explicit state management, testing and honest documentation of limitations.
