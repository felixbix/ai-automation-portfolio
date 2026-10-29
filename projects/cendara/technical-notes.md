# CENDARA — Technical Architecture and Testing Notes

## 1. Purpose

CENDARA explores how AI-assisted information extraction, deterministic qualification rules, and database-backed workflow state can work together in a lead-management system.

The project is built with n8n, PostgreSQL hosted on Neon, Gemini, and Telegram integrations.

These notes document the workflow design, selected tested behaviors, and known limitations. They do not claim that every failure scenario has been tested or that the system is production-ready.

## 2. High-Level Architecture

The main processing path follows this sequence:

1. Receive and normalize an incoming event.
2. Validate the event and required input fields.
3. Track event processing in PostgreSQL.
4. Create or retrieve the corresponding lead.
5. Extract structured information from the enquiry using an LLM.
6. Evaluate the extracted information against deterministic qualification rules.
7. Save the qualification outcome.
8. Update the event lifecycle status.
9. Create and deliver notifications when the configured workflow requires them.
10. Record delivery outcomes and monitor failures that exhaust their retry allowance.

## 3. Workflow Responsibilities

### Event Intake and Validation

Incoming messages are normalized into a consistent structure with event identifiers and message content.

Validation prevents empty or unusable inputs from continuing through the normal lead-processing path.

### Event Lifecycle Management

The PostgreSQL event record tracks processing status. The workflow includes explicit processing and completion updates for the paths tested.

Stable event identifiers and database-backed safeguards are used to reduce duplicate processing. This design still requires testing under concurrent execution and additional retry scenarios.

### AI-Assisted Lead Intelligence

Gemini supports structured extraction from natural-language enquiries.

The fields include customer intent, urgency, budget information when available, requested service, a concise summary, and information that may still be missing.

Extracted fields provide context for qualification; they are not automatically treated as verified business facts.

### Deterministic Qualification

A separate qualification layer evaluates extracted information using configured business rules.

Examples of rule outcomes include:

- Urgent enquiries can be routed for human review.
- Enquiries matching a configured high-intent and budget rule can qualify for a higher-priority outcome.
- Enquiries matching a configured low-intent rule can be marked unqualified.
- If no configured rule matches, the fallback routes the lead to human review instead of automatically rejecting it.

### Notification Delivery and Recovery

Notification delivery is tracked separately from lead qualification and event processing.

The workflow records delivery state, handles successful and failed delivery paths, and supports a bounded retry process with a maximum of three attempts.

Monitoring is intended to identify failed notifications that exhaust the retry allowance and require attention.

## 4. Selected Testing and Debugging Outcomes

### Qualification Fallback

**Issue:** An unmatched qualification rule could leave a lead in a generic pending state without an explicit review route.

**Change:** The fallback was updated to use `needs_review`, with the reason that no configured rule matched and human review is required.

**Outcome:** The revised fallback was exercised against existing test records. This confirms the selected behavior, not exhaustive qualification coverage.

### Event Lifecycle Tracking

**Issue:** Earlier records did not consistently contain processing timestamps, making it difficult to distinguish normal processing from historical records.

**Change:** Explicit workflow steps were added to mark processing and completion, with timestamps recorded during the corresponding execution paths.

**Outcome:** New processing paths were tested for status and timestamp updates. Some historical records were reconciled using available evidence; missing historical timestamps were not fabricated.

### Notification State Consistency

**Issue:** A notification success-handling path was using an update that marked the delivery as failed rather than sent.

**Change:** The success path was corrected to mark a pending notification as `sent`, while the failure path records `failed` and an error message when available.

**Outcome:** The corrected delivery-state handling was tested, including a notification successfully delivered on the third retry attempt.

### Bounded Recovery

**Design:** Eligible failed notifications can be retried up to three times. Exhausted failures are intended to be visible to the monitoring workflow.

**Remaining work:** Broader tests are needed for repeated failures, concurrent execution, retry eligibility, and monitoring accuracy.

## 5. Reliability Controls

The project incorporates several reliability concepts:

- Input validation before normal processing
- Persistent event and lead records
- Event identifiers for duplicate-prevention controls
- Explicit qualification rules and a human-review fallback
- Separate notification delivery state
- Bounded notification retries
- Monitoring and operational metrics
- Database records to support debugging and reconciliation

These controls reduce specific risks but do not establish guaranteed exactly-once execution or eliminate all possible failures.

## 6. Operational Metrics

The dashboard-oriented workflow aggregates information about:

- Total leads and qualification outcomes
- Event processing statuses
- Notification delivery statuses
- Retry eligibility and exhausted failures
- Audit activity

Metrics should be checked against the underlying database records before they are used for operational reporting.

## 7. Known Limitations

CENDARA is an active portfolio project.

- Testing covers selected workflow paths, not every possible scenario.
- Qualification quality depends on AI extraction and rule configuration.
- Historical records may require additional evidence-based review.
- Concurrent event handling and broader failure recovery need further validation.
- No production customer deployment or measured business impact is claimed.

## 8. Next Testing Priorities

1. Test invalid, empty, ambiguous, and incomplete inputs.
2. Test every configured qualification rule and the unmatched-rule fallback.
3. Verify duplicate-event behavior during retries and concurrent execution.
4. Test notification success, failure, retry exhaustion, and monitoring.
5. Compare dashboard metrics with SQL queries against the source tables.
6. Record reproducible test cases and expected versus actual results.

## 9. Security

Before publishing workflow exports, SQL files, or screenshots, remove credentials, API keys, Telegram bot tokens, database connection strings, private chat identifiers, and personal customer data.

Only publish sanitized examples and synthetic test data.

## Summary

CENDARA demonstrates a hybrid AI automation approach: use AI to interpret incoming enquiries, deterministic rules to make qualification decisions, PostgreSQL to preserve operational state, and controlled recovery mechanisms to support reliability.

Its development emphasizes not only the successful workflow path but also the importance of traceability, testing, failure handling, and accurate documentation of limitations.
