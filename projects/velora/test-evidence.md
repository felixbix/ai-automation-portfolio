# VELORA — Database Test Evidence and Validation

## 1. Purpose

This document records selected development and validation work performed on VELORA's PostgreSQL database foundation.

VELORA is an AI hotel operations portfolio project built around a fictional property, VELORA Grand Dubai.

The evidence in this document relates primarily to database development. It does not claim that the complete guest-facing hotel automation system is operational.

## 2. Development Environment

- **Database:** PostgreSQL
- **Hosting:** Neon development environment
- **Automation platform:** n8n
- **Project:** VELORA Hotel Operations System

## 3. Database Foundation

The PostgreSQL schema was created in stages. Its table groups cover:

- Room types and physical rooms
- Rate rules and inventory blocks
- Guests, conversations, and messages
- Booking requests and reservations
- Reservation price items
- Guest service requests
- Hotel policies and knowledge articles
- Processed events, audit logs, and operation records

## 4. Selected Development and Validation Evidence

| Area | Development or validation activity | Status |
|---|---|---|
| Database setup | PostgreSQL development database provisioned through Neon. | Completed |
| Schema creation | Table structures created in stages. | Completed |
| Table structure | Created tables and schema objects checked during development. | Tested |
| Room modeling | Room categories were modeled separately from physical rooms. | Included in schema design |
| Reservation modeling | Booking requests and reservation records were represented separately. | Included in schema design |
| Operational tracking | Processed-event, audit-log, and operation-record structures were created. | Included in schema foundation |
| End-to-end guest workflows | Complete Telegram guest intake and reservation processing. | Not yet completed |

These entries describe the database foundation and selected development checks. They should not be interpreted as evidence that every constraint, relationship, transaction, or workflow has passed comprehensive testing.

## 5. Important Database Design Decisions

### Separate Room Categories from Physical Rooms

A room category represents a type of accommodation, while a physical room represents an individual unit.

This separation supports a more accurate hotel inventory model.

### Separate Booking Requests from Reservations

A booking request represents an enquiry or requested action. A reservation record represents a distinct operational state.

Keeping them separate supports validation and controlled reservation processing.

### Maintain Operational Records

Processed events, audit logs, and operation records provide a foundation for tracking system activity and investigating future workflow issues.

### Use PostgreSQL for Operational Facts

Availability, pricing, and reservation status should be determined from validated database records and explicit business rules.

AI-generated responses should not independently establish those facts.

## 6. Outstanding Validation Work

The following tests remain important before the database can support a complete hotel operations workflow:

1. Verify actual primary-key and foreign-key relationships.
2. Test uniqueness and check constraints.
3. Test invalid and duplicate records.
4. Validate transaction behavior for reservation-related operations.
5. Test concurrent availability and reservation requests.
6. Test rollback and recovery behavior after failures.
7. Validate database integration with the intended n8n workflows.

## 7. Current Limitations

- The database foundation is in development, not a production hotel environment.
- The complete guest-facing Telegram workflow remains unfinished.
- Live hotel inventory and rates are not connected.
- Payment processing and production reservations are not implemented.
- Further constraint, concurrency, and transaction testing is required.

## 8. Security

Public documentation must not include database connection strings, passwords, API keys, private guest information, or unrestricted database exports.

Use sanitized schema information and synthetic records for public demonstrations.

## Summary

VELORA's database development establishes a structured foundation for hotel inventory, guest communication, reservations, and operational tracking. The next engineering stage is to validate the database constraints and implement the complete guest-facing workflows with appropriate integrity and recovery controls.
