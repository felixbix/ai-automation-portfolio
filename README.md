# Felix Bix | AI Automation Developer

I build practical AI-powered automation systems that connect workflows, APIs, language models, and databases to solve business problems.

**Focus areas:** n8n · JavaScript · PostgreSQL · API integration · AI-assisted extraction · deterministic business rules · idempotency · error handling · operational monitoring

> This repository documents portfolio projects and their current implementation status. It distinguishes tested prototype behavior from planned work; it does not claim production deployment or customer outcomes without evidence.

## Featured Projects

### ATLAS — AI Travel Agent
A conversational travel-planning prototype that collects trip requirements, preserves request state, and drafts a preliminary itinerary.

**Status:** Prototype in progress. Tested using sample travel data and Google Sheets persistence. Live travel-provider integrations and real bookings are not implemented.

### VELORA — AI Hotel Operations
A hotel-operations concept for a fictional Dubai property, designed around guest workflows and PostgreSQL-backed operational data.

**Status:** PostgreSQL schema and architecture foundation created and checked in Neon. Full end-to-end reservation and guest-service workflows remain in development.

### CENDARA — AI Lead Intelligence & Business Automation
An n8n and PostgreSQL lead-processing workflow covering event intake, lead qualification, urgent admin notifications, notification retries, and operational metrics.

**Status:** Core paths have been exercised. Event lifecycle tracking and a no-match qualification fallback have been improved. Some historical recovery and additional failure-path testing remain ongoing.

## My Engineering Approach

1. Start with the business problem and define expected behavior.
2. Validate inputs and keep persistent state in structured records.
3. Use AI for language understanding and deterministic rules for consistent decisions.
4. Design for duplicate events, failures, retries, and human review.
5. Verify changes with workflow executions and database queries.
6. Document limitations and identify the next improvements.

## Repository Structure

Detailed project documentation will be organized into:

- `projects/atlas/` — Travel-planning case study
- `projects/velora/` — Hotel-operations case study
- `projects/cendara/` — Lead-intelligence case study

## Contact

- LinkedIn: Add your profile URL
- X: Add your profile URL
- Email: Add your professional email
- Portfolio website: Add your published Canva website URL when ready

## Security

Never upload API keys, bot tokens, database credentials, `.env` files, or private customer information. Use sanitized examples and synthetic test data in public demonstrations.
