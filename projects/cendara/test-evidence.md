# CENDARA — Test Evidence and Validation

## Purpose

This document records selected workflow tests, observed outcomes, debugging changes, and remaining validation work for CENDARA.

The evidence reflects specific development and test executions. It is not a claim of exhaustive testing or production readiness.

## Test Results

| Test area | Expected behavior | Observed result | Status |
|---|---|---|---|
| Input validation | Unusable message content should not proceed through normal lead processing. | A friendly response path for invalid text was tested. | Tested on a selected path |
| Qualification fallback | A lead that matches no configured rule should be routed for review. | The fallback was changed to `needs_review`, with a reason explaining that no rule matched. | Tested on selected records |
| Event lifecycle | New processing paths should record processing and completion states with timestamps. | Explicit processing and completion updates were exercised on new test executions. | Tested on selected paths |
| Notification success handling | Successful delivery should be represented as `sent`, not `failed`. | The success-state update was corrected after a status-handling defect was identified. | Correction implemented; broader regression testing needed |
| Notification retry | Eligible failed notifications should be retried within the configured limit. | A notification was successfully delivered on the third retry attempt. Its database state was subsequently reconciled to reflect the delivery. | Selected recovery case observed |
| Historical record recovery | Historical records should be corrected only when supported by available evidence. | Selected records were reconciled without fabricating missing historical processing timestamps. | Partial, evidence-based recovery |
| Operational metrics | Dashboard counts should agree with the underlying database records. | Metrics are available for monitoring; additional systematic cross-checks remain necessary. | Further validation required |

## Debugging Example: Qualification Fallback

### Problem

When no configured qualification rule matched, the previous fallback could leave a lead in a generic pending state without explicitly routing it for review.

### Change

The fallback was changed to:

- Set qualification status to `needs_review`.
- Leave the qualification score as null when no rule applies.
- Set priority to `normal`.
- Record a reason explaining that no configured rule matched and human review is required.

### Result

The updated behavior was exercised against selected records. This improves the handling of unmatched cases, but every qualification rule and relevant combination still needs systematic testing.

## Debugging Example: Event Lifecycle

### Problem

Some earlier event records lacked processing timestamps, making it difficult to distinguish a normal execution from a historical record that had been reconciled later.

### Change

The workflow gained explicit steps to mark events as processing and completed, with timestamps recorded during those execution paths.

### Result

New test executions demonstrated the status and timestamp updates. Historical records were treated separately, and missing historical timestamps were not invented.

## Debugging Example: Notification State

### Problem

A notification success-handling path contained an update that marked a delivery as failed rather than sent.

### Change

The success update was corrected to record `sent`, while the failure path records `failed` and an error message when available.

### Result

A notification was confirmed delivered on the third retry attempt. Its database state was then reconciled to reflect the successful delivery. Additional regression testing is needed to verify the corrected success and failure paths consistently across more scenarios.

## Remaining Test Priorities

1. Test every qualification rule and the unmatched-rule fallback with representative inputs.
2. Test duplicate events and concurrent processing.
3. Verify notification success, failure, retry eligibility, and retry exhaustion.
4. Reconcile dashboard metrics with direct database queries.
5. Test invalid and incomplete inputs across the relevant workflow branches.
6. Record new test outcomes with reproducible steps and sanitized data.

## Security and Reproducibility

Public examples should use synthetic or sanitized data. Do not include bot tokens, API keys, database credentials, private chat identifiers, webhook secrets, or real customer information.

Where practical, future test records should include a test identifier, starting conditions, expected result, observed result, and outcome.

## Summary

CENDARA's development has included debugging qualification fallback behavior, improving event lifecycle tracking, and correcting notification-state handling. Recording these changes alongside their limitations demonstrates an engineering process focused on observable behavior, evidence-based recovery, and incremental reliability improvements.
