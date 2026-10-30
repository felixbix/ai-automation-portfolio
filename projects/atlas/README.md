# ATLAS — AI Travel Agent

## Project Overview

ATLAS is a conversational travel-planning prototype built with n8n, Gemini, Telegram, and Google Sheets. It demonstrates how an AI-assisted workflow can collect travel requirements, preserve request state, and generate a preliminary itinerary.

**Project type:** Portfolio prototype  
**Status:** In development  
**Primary goal:** Demonstrate conversational workflow orchestration, structured data handling, and AI-assisted planning.

## Workflow Overview

This screenshot shows the ATLAS travel-planning workflow in n8n, including its main processing stages and connections.

![ATLAS workflow overview](Screenshot%202026-10-30%20010713.png)

## The Problem

Travel-planning requests often arrive as incomplete messages. Before useful recommendations can be prepared, important details such as destination, travel dates, number of travelers, and budget must be collected and organized.

ATLAS explores how a workflow can collect these details progressively instead of requiring the user to provide every field in one message.

## Solution

ATLAS uses a Telegram conversation as the user interface and n8n to coordinate the workflow. It extracts trip details from natural-language messages, checks for missing information, saves the request, and continues collecting details until enough information is available to prepare a preliminary itinerary.

## Architecture

- **Telegram:** Receives travel requests and sends responses.
- **n8n:** Orchestrates workflow execution, validation, conditional routing, and follow-up messages.
- **Gemini:** Extracts structured trip information and assists with itinerary drafting.
- **Google Sheets:** Stores trip details and tracks the request's progress.
- **Sample travel data:** Supports prototype itinerary recommendations without depending on live travel-provider integrations.

## Workflow

1. Receive a travel request through Telegram.
2. Normalize the incoming message and relevant identifiers.
3. Validate that the input contains usable text.
4. Extract available trip details, including origin, destination, duration, departure date, travelers, and budget.
5. Identify missing information and ask the user for it.
6. Persist the trip record and update its progress as information is collected.
7. Generate a preliminary itinerary using sample travel data.
8. Present the result to the user while keeping any booking action outside the automated flow.

## Implementation Highlights

- Tested Telegram message intake and response handling.
- Extracted structured trip information from natural-language requests.
- Added validation for empty or unusable text.
- Used conditional logic to handle incomplete requests.
- Collected trip details across multiple messages.
- Persisted trip records and tracked the next required step in Google Sheets.
- Used Gemini to support preliminary itinerary generation.
- Kept booking actions separate from itinerary drafting.

## Safety and Limitations

ATLAS is a prototype, not a live travel-booking service.

- It does not make actual travel bookings.
- It does not confirm live flight, hotel, or transport availability.
- Sample travel data is used for itinerary development.
- Live travel APIs, production database-backed memory, and a dedicated web-form interface remain potential future improvements.
- Booking actions would require appropriate authorization and confirmation before execution.

## Current Development Priorities

1. Improve handling of invalid, incomplete, and ambiguous travel requests.
2. Test workflow behavior when external services fail.
3. Move persistent trip state to a database when appropriate.
4. Integrate live travel data sources and validate their responses.
5. Expand test coverage and document execution results.

## Skills Demonstrated

- Workflow orchestration with n8n
- Telegram integration
- API and JSON data handling
- AI-assisted structured extraction
- Input validation and conditional routing
- Persistent state management
- Incremental information collection
- Prototype testing and technical documentation
- 
## Technical Documentation

- [Travel Planning Workflow Architecture](architecture.md) — visual overview of Telegram intake, trip-detail extraction, progressive information collection, and itinerary drafting.
- - [Technical Architecture and Testing Notes](technical-notes.md) — implementation details, selected test results, engineering decisions, known limitations, and future improvements.
## Summary

ATLAS demonstrates how AI and deterministic workflow logic can work together to turn an incomplete conversational request into a structured travel-planning workflow. Its focus is on collecting reliable inputs, tracking progress, and producing a useful draft without misrepresenting recommendations as confirmed bookings.
