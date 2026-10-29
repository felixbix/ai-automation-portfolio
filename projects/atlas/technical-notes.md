# ATLAS — Technical Architecture and Testing Notes

## 1. Purpose

ATLAS is a conversational travel-planning prototype built with n8n, Telegram, Gemini, and Google Sheets.

The project explores how natural-language requests can be converted into structured travel requirements, saved as persistent request state, and processed progressively until enough information is available to prepare a preliminary itinerary.

These notes document the current implementation, selected tested behavior, design decisions, and future development priorities.

## 2. High-Level Architecture

The prototype follows this processing sequence:

1. Receive a travel request through Telegram.
2. Normalize incoming message data and relevant identifiers.
3. Validate that the message contains usable text.
4. Extract available trip details using Gemini.
5. Identify which required details are missing.
6. Retrieve or update the trip record in Google Sheets.
7. Track request progress and the next required step.
8. Ask the user for missing information when necessary.
9. Prepare a preliminary itinerary using sample travel data when sufficient information is available.
10. Return the draft itinerary through Telegram.

The workflow separates collecting and organizing requirements from generating the itinerary.

## 3. Technology Responsibilities

### Telegram

Telegram provides the conversational interface for travel requests and follow-up questions.

### n8n

n8n coordinates message intake, validation, workflow branching, AI integration, data persistence, and responses.

### Gemini

Gemini assists with extracting structured trip information from natural-language messages and drafting a preliminary itinerary.

The workflow should validate extracted values rather than treating all model output as automatically correct.

### Google Sheets

Google Sheets stores trip information and records request progress, including the trip identifier, status, and next required step.

This gives the prototype persistent request state across multiple messages.

### Sample Travel Data

The itinerary stage uses sample travel data. It does not establish live availability, current prices, or confirmed travel arrangements.

## 4. Progressive Information Collection

A travel request may arrive with only some of the required information.

ATLAS is designed to collect details incrementally, including:

- Origin and destination
- Trip duration
- Departure date
- Number of travelers
- Budget

When details are missing, the workflow requests the next required value rather than assuming it.

As the user responds, the workflow updates the existing trip record and advances the request to the next stage.

## 5. Input Validation

Incoming text is normalized and checked for usable content before the regular extraction flow continues.

Invalid or unsupported input should produce a friendly response rather than proceeding as if a valid travel request had been received.

Further testing should cover ambiguous destination names, malformed dates, conflicting details, repeated messages, and requests that cannot be handled by the prototype.

## 6. Persistence and Request State

Google Sheets provides the prototype's persistence layer.

The stored trip record allows the workflow to track what has already been collected and what information is still required.

A defined status and next-step field make the workflow's progress easier to inspect than relying exclusively on the conversation history.

The current implementation is a prototype; the storage approach may evolve as requirements for stronger data integrity, concurrent updates, and recovery become clearer.

## 7. Selected Implementation and Testing

The prototype has been exercised through the following paths:

### Telegram Intake

Travel requests were received through Telegram and passed into the n8n workflow.

### Structured Extraction

A natural-language request was processed to extract available trip details, including origin, destination, and trip duration.

### Invalid Input Handling

A friendly response path was tested for messages that did not contain usable request text.

### Progressive Data Collection

The workflow collected missing travel details through successive messages, including departure date, number of travelers, and budget.

### Persistent State

Google Sheets records were updated as additional trip information was supplied. The request status and next required step were also advanced.

### Preliminary Itinerary

Gemini was selected to assist with itinerary drafting. Recommendations use sample travel data rather than live travel-provider availability.

These observations demonstrate selected prototype behavior; they do not constitute exhaustive testing of every input, failure, or conversation sequence.

## 8. Engineering Decisions

### Separate Extraction from Workflow Control

Gemini helps interpret natural language, while n8n handles validation, branching, state updates, and the decision to request missing details.

This separation makes the workflow's behavior easier to inspect and test.

### Ask Rather Than Assume

Missing travel details should be collected from the user instead of being invented by the workflow.

### Persist Progress

Trip information is stored as the conversation advances, allowing the prototype to continue from previously collected details.

### Keep Booking Separate

Itinerary drafting is not equivalent to booking. ATLAS does not make actual travel reservations and must not describe a draft as a confirmed arrangement.

## 9. Known Limitations

ATLAS remains a portfolio prototype.

- Travel recommendations rely on sample data.
- Live flight, hotel, and transport availability is not verified.
- The prototype does not make actual travel bookings.
- Google Sheets is used for persistent request state; stronger database-backed state management may be considered later.
- Additional tests are needed for duplicate messages, interrupted conversations, conflicting user inputs, and failures during model or storage operations.
- No production deployment or measured customer impact is claimed.

## 10. Future Development Priorities

1. Test incomplete, ambiguous, conflicting, and invalid travel requests.
2. Improve handling of repeated messages and interrupted conversations.
3. Test recovery from Gemini and Google Sheets errors.
4. Strengthen request-state integrity and duplicate-message handling.
5. Evaluate PostgreSQL for more robust persistent state management.
6. Integrate live travel data sources with response validation.
7. Consider a web-form interface alongside Telegram.
8. Document reproducible tests and their observed results.

## 11. Security Considerations

Before publishing workflow exports or screenshots, remove bot tokens, API keys, credentials, private chat identifiers, webhook secrets, and personal travel information.

Use sanitized examples and synthetic test data in the public portfolio.

## Summary

ATLAS demonstrates how a conversational AI workflow can convert an incomplete travel request into a structured planning process. Its principal engineering lessons are progressive data collection, input validation, persistent state management, separation of AI extraction from workflow control, and clear boundaries between itinerary recommendations and real-world bookings.
