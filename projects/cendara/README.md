# CENDARA — AI Lead Intelligence & Business Automation

## Project Overview

CENDARA is an AI-assisted lead-management and business-automation project built with n8n, PostgreSQL, and Gemini. It explores how incoming business enquiries can be converted into structured lead records, evaluated against explicit qualification rules, and routed for follow-up.

The project emphasizes not only AI-based information extraction but also operational reliability, including event tracking, duplicate prevention, notification delivery, and recovery from workflow failures.

**Project type:** AI automation and lead intelligence  
**Status:** Active development and testing  
**Primary goal:** Build a configurable foundation for processing, qualifying, and routing business leads reliably.

## Workflow Overview

The following screenshot shows the CENDARA Lead Intelligence Worker in n8n, including its lead-processing, qualification, event-tracking, and notification-handling paths.

![CENDARA Lead Intelligence Worker workflow overview](cendara-workflow-overview.png)
## The Problem

Business enquiries often arrive as unstructured messages containing incomplete information about a customer's needs, budget, urgency, and requested service.

Manually reviewing every enquiry can be repetitive and make it harder to prioritize urgent opportunities. However, relying exclusively on AI-generated decisions can also create inconsistency and make outcomes difficult to audit.

CENDARA explores a hybrid approach: use AI to interpret the message, then apply deterministic business rules to make qualification and routing decisions.

## Solution

The workflow receives incoming events, validates the relevant input, processes lead information, and stores structured records in PostgreSQL.

Gemini supports the extraction and interpretation of information from natural-language enquiries. A separate qualification layer evaluates the resulting data against configured rules. Depending on the outcome, the workflow can update lead records and initiate notifications for cases requiring attention.

Additional workflows support notification retries, delivery-state tracking, and operational metrics.

## Technology Stack

- **n8n:** Workflow orchestration, branching, integrations, and recovery workflows.
- **PostgreSQL:** Persistent lead data, event records, and notification-delivery state.
- **Neon:** Hosted PostgreSQL development environment.
- **Gemini:** AI-assisted extraction and interpretation of lead information.
- **Telegram:** Message intake and administrative notifications.
- **JavaScript:** Data transformation and qualification logic within workflow code nodes.

## System Architecture

### 1. Event Intake and Validation

Incoming events are normalized into a consistent structure containing identifiers and message content.

Validation checks help prevent unusable inputs from progressing through the processing workflow.

### 2. Lead Intelligence

The AI extraction stage is designed to identify useful information from a natural-language enquiry, including:

- Customer intent
- Urgency
- Budget information, when supplied
- Requested service
- A concise summary
- Missing information that may need follow-up

These extracted fields provide structured context for later processing.

### 3. Deterministic Qualification

The qualification layer evaluates extracted information against configured business rules.

This separates language interpretation from business decisions. For example, an urgent enquiry can be routed for human review, while other qualification outcomes depend on the applicable configured rules.

When no rule matches, the fallback routes the lead to human review rather than treating the absence of a matching rule as an automatic rejection.

### 4. Event Lifecycle Tracking

Processed events use database-backed status tracking to record workflow progress.

The event lifecycle includes states such as received, processing, and completed, with timestamps recorded by the relevant workflow steps.

Stable event identifiers and database-backed safeguards help reduce duplicate processing. These controls are designed to support idempotent behavior; they do not imply that every possible failure or delivery scenario has been eliminated.

### 5. Notification Delivery and Recovery

Administrative notifications have their own delivery records and statuses.

The project includes a retry workflow that attempts eligible failed notification deliveries, with a maximum of three attempts. Successful and failed delivery paths update the stored notification status.

Monitoring also helps identify notifications that have exhausted their retry allowance and may require manual attention.

### 6. Operational Metrics

A dashboard-oriented metrics workflow aggregates information about leads, event processing, qualification outcomes, notification delivery, and audit activity.

The purpose is to make operational state more visible and support investigation when workflow behavior differs from expectations.

## Engineering Principles

### AI for Interpretation, Rules for Decisions

AI assists with understanding unstructured enquiries. Explicit qualification rules determine how extracted information maps to business outcomes.

### Reliable Event Processing

Stable event identifiers and persistent processing state help the workflow recognize previously received events and reduce duplicate side effects.

### Separate Notification State

Notification delivery is tracked independently from lead qualification and event processing. This makes it easier to distinguish a successfully processed lead from a message that still needs delivery or recovery.

### Failure Handling

The project includes success and failure paths, retry limits, and monitoring for exhausted notification retries. These mechanisms improve recoverability but still require testing across realistic failure scenarios.

### Traceability

Persistent lead, event, notification, and audit records provide evidence for debugging and reviewing workflow outcomes.

## Implementation and Testing

Development and testing have covered key parts of the lead-processing workflow, including:

- Event intake, validation, and persistent event records.
- Lead extraction and qualification using configured rules.
- A human-review fallback when no qualification rule matches.
- Event status and timestamp updates for new processing paths.
- Administrative notification delivery-state tracking.
- A bounded notification retry workflow and recovery monitoring.
- Dashboard-oriented operational metrics.

Testing also exposed inconsistencies in notification-state updates and incomplete historical event records. The notification success update was corrected, and selected historical records were reconciled using available evidence.

Historical recovery remains distinct from the normal live processing path. Reconciliation does not mean that earlier records have been given timestamps or execution histories that were never captured.

## Current Limitations

CENDARA remains a portfolio project under active development.

- Qualification quality depends on the extraction output and configured business rules.
- The current implementation has been tested through selected paths, not every possible failure scenario.
- Some historical records require further review or recovery.
- Additional work is needed to validate duplicate-event handling under concurrent execution and real delivery failures.
- Industry-specific configuration, broader channel integrations, and production deployment are future development areas.

No production customer deployment or measured business outcome is claimed.

## Current Development Priorities

1. Expand tests for missing fields, ambiguous enquiries, and unmatched qualification rules.
2. Verify event idempotency under retries and concurrent execution.
3. Exercise notification success, failure, retry limits, and exhausted-retry monitoring.
4. Reconcile remaining historical records only where evidence supports a correction.
5. Validate dashboard metrics against underlying database records.
6. Improve configuration and documentation so the system can be adapted to different business workflows.

## Skills Demonstrated

- AI-assisted structured data extraction
- n8n workflow orchestration
- PostgreSQL and SQL
- JavaScript-based data transformation
- Deterministic business-rule evaluation
- Event lifecycle management
- Idempotency and duplicate-prevention design
- Notification delivery tracking
- Bounded retries and recovery workflows
- Operational metrics and auditability
- Debugging, testing, and technical documentation
- 
## Technical Documentation

- [Workflow Architecture Diagram](architecture.md) — visual overview of lead processing and notification recovery.
- [Technical Architecture and Testing Notes](technical-notes.md) — implementation details, debugging outcomes, reliability controls, and remaining test priorities.
## Summary

CENDARA explores a practical approach to AI-assisted lead management: extract useful information with AI, apply explicit rules to business decisions, persist operational state, and design recovery paths for failures.

Its central engineering lesson is that a useful automation system must do more than process a successful example. It should also make its decisions traceable, track important state transitions, and provide a controlled way to recover when things go wrong.
