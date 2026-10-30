# ATLAS — Test Evidence and Validation

## 1. Purpose

This document records selected development tests and observed behavior for ATLAS, a conversational AI travel-planning prototype built with n8n, Gemini, Telegram, and Google Sheets.

The evidence covers message intake, input validation, structured travel-detail extraction, progressive information collection, and request persistence.

It does not claim exhaustive testing or production deployment.

## 2. Testing Environment

- **Workflow platform:** n8n
- **AI integration:** Gemini
- **Communication interface:** Telegram
- **Persistence layer:** Google Sheets
- **Project type:** Travel-planning prototype

## 3. Selected Test Results

| Test area | Expected behavior | Observed result | Status |
|---|---|---|---|
| Telegram intake | Receive incoming travel requests. | Telegram messages were received and processed through the workflow. | Tested |
| Input validation | Handle messages without usable request text. | An invalid-input response path was exercised. | Tested |
| Structured extraction | Extract trip details from natural-language messages. | Origin, destination, and duration were extracted in a selected test. | Tested |
| Progressive information collection | Identify missing travel details and request them. | Follow-up messages were used to collect departure date, traveler count, and budget. | Tested |
| Persistent request state | Save collected details and update request progress. | Google Sheets records were updated as additional information was supplied. | Tested |
| Preliminary itinerary | Prepare a draft when enough information is available. | A preliminary itinerary was generated using sample travel data. | Selected prototype path exercised |
| Actual booking | Create a confirmed travel reservation. | No booking was made. | Not implemented |

## 4. Example Travel Request Test

A selected prototype test used a travel request from Lagos to Ghana.

The workflow progressively collected and structured the following information:

| Field | Test value |
|---|---|
| Origin | Lagos |
| Destination | Accra, Ghana |
| Duration | Five days |
| Departure date | 15 November 2026 |
| Travelers | 2 |
| Budget | ₦500,000 |

The request was used to exercise the extraction and progressive information-collection process.

A preliminary itinerary was generated using sample travel data. It was not a live travel search or a confirmed booking.

## 5. Debugging and Implementation Observations

### Invalid Input Handling

The workflow was exercised with unusable input to verify that it could return an invalid-input response rather than treating every message as a valid travel request.

### Structured Travel Data

Gemini was used to extract travel information from natural-language messages into structured fields.

The extracted information was then used by the workflow to determine which details were available and which were still missing.

### Progressive Collection

The workflow collected missing details through successive Telegram messages rather than requiring every field in the first request.

### Google Sheets Persistence

The trip record was updated as the request progressed.

During development, a column-mapping issue involving the spreadsheet's later columns was corrected by using the appropriate `next_step` header.

This helped restore the intended request-progress mapping.

## 6. Engineering Decisions

### Validate Before Processing

Incoming messages should be normalized and checked before they proceed into the main extraction workflow.

### Ask for Missing Details

The workflow should request information that has not been supplied rather than inventing travel dates, budgets, or traveler details.

### Separate AI Extraction from Workflow Control

Gemini assists with language understanding, while n8n handles validation, conditions, request progression, and persistence.

### Keep Itinerary Drafting Separate from Booking

A preliminary itinerary is not a confirmed travel arrangement. Booking actions remain outside the current prototype.

## 7. Known Limitations

- Sample travel data is used instead of live travel-provider information.
- Live flight, hotel, and transport availability is not verified.
- The workflow does not create actual bookings.
- Google Sheets is the current persistence layer.
- Further testing is required for interrupted conversations, duplicate messages, conflicting inputs, and external service failures.
- No production deployment or measured customer impact is claimed.

## 8. Remaining Test Priorities

1. Test ambiguous destinations and conflicting trip details.
2. Test malformed dates, missing fields, and invalid budget values.
3. Test repeated Telegram messages and interrupted conversations.
4. Test Gemini errors and malformed structured outputs.
5. Test Google Sheets failures and recovery behavior.
6. Evaluate database-backed request state as the project evolves.
7. Document additional test executions with expected and observed outcomes.

## 9. Security Considerations

Public examples must not expose Telegram bot tokens, API keys, webhook secrets, database credentials, private chat identifiers, or personal travel information.

Use sanitized screenshots and synthetic test records when publishing technical evidence.

## Summary

ATLAS has been exercised through selected tests covering Telegram intake, invalid-input handling, AI-assisted travel-detail extraction, progressive information collection, and Google Sheets persistence.

The prototype demonstrates how conversational AI and deterministic workflow control can work together to transform an incomplete travel request into a structured planning process.
