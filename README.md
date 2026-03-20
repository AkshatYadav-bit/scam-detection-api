# HCL-GUVI API - Scam Detection & Intelligence Extraction System

A FastAPI-based intelligent system designed to detect, analyze, and respond to potential scam messages. The system leverages agent orchestration with multiple personas to engage users and extract intelligence from suspicious communications.

## Overview

This API provides real-time scam detection capabilities combined with an intelligent agent-based response system. It analyzes incoming messages for scam indicators, extracts sensitive information (UPI IDs, bank accounts, URLs), and generates contextual responses using persona-based dialogue policies.

## Project Structure

```
api/
├── main.py                  # FastAPI application entry point
├── auth.py                  # API key verification
├── schemas.py              # Pydantic models for request/response validation
├── session_store.py        # Session management and storage
├── metrics.py              # Metrics tracking and reporting
├── callback.py             # Result callback handling
│
├── agent/                  # Agent orchestration modules
│   ├── orchestrator.py     # Main agent coordinator
│   ├── personas.py         # Persona selection logic
│   ├── policy.py          # Dialogue policy decision engine
│   └── responder.py       # Response generation
│
├── detection/              # Scam detection modules
│   └── scam_detector.py    # Keyword-based scam analysis
│
└── extraction/             # Intelligence extraction modules
    └── intelligence.py     # Regex-based extraction (UPI, banks, URLs)
```

## Features

### 1. **Scam Detection**
- Keyword-based analysis across multiple scam indicators:
  - **Urgency**: "urgent", "immediately", "asap", "now"
  - **Account Threats**: "blocked", "suspended", "limited", "frozen"
  - **Payment Solicitation**: "pay", "payment", "upi", "bank", "transfer"
  - **Authority Impersonation**: "bank", "officer", "support", "kyc"
  - **Credential Requests**: "otp", "pin", "password"
  - **Malicious Links**: "http", "www", URLs
- Signal scoring system for risk assessment

### 2. **Intelligence Extraction**
Automatically extracts sensitive information from messages:
- **UPI IDs**: Pattern matching for Indian payment identifiers
- **Bank Accounts**: Detection of account-like numerical sequences
- **Phishing URLs**: Extraction of suspicious links
- Confidence scoring for each extracted item

### 3. **Agent Orchestration**
Multi-component intelligent response system:
- **Dialogue Policy**: Determines next actions based on message analysis
- **Personas**: Multiple persona selection for context-aware responses
- **Responder**: Generates appropriate engagement messages

### 4. **Session Management**
- Per-session tracking of conversations
- Message history preservation
- Conversation context maintenance

### 5. **Metrics & Monitoring**
- Request/response tracking
- Performance metrics
- System health monitoring

## API Endpoints

### Health Check
```http
GET /
```
Returns system status.

### Message Processing
```http
POST /message
Header: X-API-KEY: <your_api_key>
```

**Request Schema:**
```json
{
  "session_id": "string",
  "sessionId": "string",
  "message": "string or object",
  "conversationHistory": [
    {
      "sender": "string",
      "text": "string",
      "timestamp": "integer"
    }
  ],
  "metadata": {}
}
```

**Response Schema:**
```json
{
  "success": "boolean",
  "data": {
    "reply": "string",
    "persona": "string",
    "decision": {},
    "intelligence": {
      "upi_ids": [],
      "bank_accounts": [],
      "phishing_urls": []
    },
    "scam_signals": {}
  }
}
```

## Installation & Setup

### Prerequisites
- Python 3.8+
- FastAPI
- Pydantic

### Installation
1. Clone the repository
2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

### Configuration

1. **Set API Key**
   ```bash
   export HONEYPOT_API_KEY="your_secure_api_key"
   ```
   (Default for development: "CHANGE_ME")

2. **Start the server**
   ```bash
   uvicorn api.main:app --reload
   ```

## Usage Example

```bash
curl -X POST http://localhost:8000/message \
  -H "X-API-KEY: your_api_key" \
  -H "Content-Type: application/json" \
  -d '{
    "sessionId": "user_123",
    "message": "Your account has been blocked. Send payment to UPI: example@bank immediately!"
  }'
```

## Module Descriptions

### `auth.py`
Handles API key authentication via HTTP headers. Raises 401 Unauthorized if key is missing or invalid.

### `scam_detector.py`
Analyzes message text against predefined keyword patterns across 6 scam indicator categories. Returns signal scores (0-1) for each category.

### `intelligence.py`
Uses regex patterns to extract:
- UPI identifiers (pattern: `word@word`)
- Bank account numbers (9-18 digits)
- URLs (http/https links)

### `orchestrator.py`
Coordinates agent components:
1. Applies dialogue policy
2. Selects appropriate persona
3. Generates contextual response

### `responder.py`
Generates replies based on detected action and session context.

### `personas.py`
Manages different agent personas (e.g., helpful support, cautious advisor) for context-appropriate engagement.

### `session_store.py`
Maintains conversation state and message history per session ID.

### `schemas.py`
Pydantic models ensuring data validation for:
- Incoming message requests
- Outgoing responses
- Intelligence items
- Message history

## Error Handling

- **Missing API Key**: Returns 401 Unauthorized
- **Missing Session ID**: Returns ValueError
- **Invalid Request Schema**: Returns 422 Unprocessable Entity

## Performance Considerations

- Message analysis is performed in real-time
- Session storage is in-memory (suitable for development; consider persistent storage for production)
- Regex patterns are pre-compiled for efficiency

## Security Notes

- **API Key**: Change default "CHANGE_ME" in production
- **Input Validation**: All requests validated via Pydantic schemas
- **Message Sanitization**: Consider adding input sanitization for production
- **Rate Limiting**: Implement rate limiting for production deployment

## Future Enhancements

- Machine learning-based scam classification
- Multi-language support
- Persistent session storage (database integration)
- Real-time alerting system
- Enhanced dialogue with NLP
- User feedback integration for model improvement

## Contributing

Follow these guidelines:
1. Maintain session isolation
2. Add tests for new detection patterns
3. Update schemas for API changes
4. Document persona behaviors

## Support & Maintenance

For issues or feature requests, contact me at akshatyadavcv@gmail.com
**Last Updated**: February 2026  
**Version**: 1.0.0
