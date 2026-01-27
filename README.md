AI Inbound Carrier Sales Agent

## Overview

This project implements an **AI-powered inbound carrier sales assistant** for freight brokerages.  
The system autonomously handles inbound carrier calls: authenticating carriers, presenting available loads, negotiating pricing, logging outcomes, and exposing real-time performance metrics through a dashboard.

It replicates the full workflow of a human carrier sales agent, combining **voice AI**, **backend decision logic**, and **live analytics** into a production-ready proof of concept.

Built as part of the **HappyRobot technical challenge**.

---

## Key Capabilities

- Inbound voice agent for carrier calls  
- MC number authentication (FMCSA + fallback)  
- Load search and presentation  
- Automated multi-round rate negotiation (up to 3 rounds)  
- Acceptance / rejection handling  
- Call outcome logging and sentiment classification  
- Real-time metrics and dashboard visualization  
- Secure, containerized deployment  

--- 

## System Architecture

The system consists of three main components:

### 1. FastAPI Backend
Central business logic and data layer:
- Carrier verification
- Load retrieval
- Negotiation rules
- Call result logging
- Metrics aggregation

All functionality is exposed via authenticated HTTP APIs.

### 2. HappyRobot Workflow
Inbound conversational layer:
- Handles carrier calls
- Collects MC number
- Presents load details
- Manages negotiation dialogue
- Executes decision paths based on backend responses

### 3. Live Dashboard
A real-time HTML dashboard that visualizes:
- Call volume
- Acceptance vs rejection
- Negotiation efficiency
- Revenue metrics
- Recent call outcomes

---

## Backend API

### Authentication
**POST** `/api/authenticate`

Validates the carrier MC number using the FMCSA API.  
If the FMCSA service is unavailable or restricted, the system automatically falls back to mock data to ensure uninterrupted demos.

---

### Load Retrieval
**GET** `/api/loads`

Returns available freight loads from a local `loads.json` file.

Each load includes:
- `load_id`
- `origin` / `destination`
- `pickup_datetime` / `delivery_datetime`
- `equipment_type`
- `loadboard_rate`
- `miles`, `weight`, `dimensions`
- `commodity_type`, `num_of_pieces`, `notes`

---

### Negotiation
**POST** `/api/negotiate`

Implements realistic broker–carrier pricing logic:
- Compares carrier offer against the board rate
- Accepts offers within a configurable margin (`MAX_OVER_PCT`, default 10%)
- Returns counteroffers when needed
- Automatically rejects after 3 unsuccessful rounds

Negotiation state (rounds, offers, outcome) is tracked and used for metrics.

---

### Call Result Logging
**POST** `/api/call/result`

Stores the final outcome of each call:
- MC number
- Load ID
- Final price
- Accepted / rejected status
- Negotiation rounds
- Transcript summary
- Sentiment classification

Data is stored in memory for the demo and can be replaced with a persistent database in production.

---

### Metrics
**GET** `/api/metrics`

Aggregates performance indicators:
- Total calls
- Accepted / rejected offers
- Acceptance rate
- Average negotiation rounds
- Revenue metrics
- Recent call history

---

## HappyRobot Workflow Logic

The inbound workflow follows a structured decision flow:

1. Inbound call starts
2. Request MC number
3. Authenticate carrier  
   - If invalid → polite rejection
4. Retrieve and present load
5. Ask for carrier’s rate
6. Negotiation loop:
   - Accept → finalize
   - Counteroffer → continue (max 3 rounds)
   - Reject after limit → no-deal message
7. Log call result
8. End call or transfer (if applicable)

Conditional paths handle:
- Accepted deals
- Ongoing negotiations
- Failed negotiations
- Fallback behavior when no condition matches

---

## Dashboard

The dashboard provides real-time visibility into system performance.

### Features
- KPI summary (calls, acceptance rate, revenue)
- Accepted vs rejected negotiations
- Revenue distribution
- Recent call table with sentiment
- Date range and quick filters

The frontend refreshes automatically via AJAX without page reloads.

---

## Security

- All API endpoints require an `x-api-key` header
- Environment variables managed via `.env`
- Sensitive keys excluded from version control
- HTTPS enforced in deployment

---

## Deployment

- Fully containerized using Docker
- Deployed on Railway
- FastAPI served with Uvicorn
- Configuration via environment variables:
  - `API_KEY`
  - `FMCSA_API_KEY`
  - `MAX_OVER_PCT`
  - `LOADS_FILE`

If FMCSA access fails, the system transparently switches to mock mode.

---

## Conclusion

This project demonstrates a complete **end-to-end AI carrier sales automation system**:

- Natural voice interaction
- Deterministic backend logic
- Controlled negotiation behavior
- Transparent metrics and analytics

It shows how conversational AI can be safely and realistically applied to freight brokerage operations, reducing manual workload while maintaining pricing discipline and operational visibility.

---

## Production Extensions

- Persistent database (PostgreSQL)
- Real TMS integration
- Call transfer to human sales reps
- Advanced sentiment analysis
- Multi-load selection logic
