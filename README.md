# Sahayak AI

A voice-first, multilingual AI assistant for accessing government services in India. Built entirely on AWS services.

## Overview

Sahayak AI helps citizens access government services through natural voice conversations in Hindi and English. The system uses:
- **Amazon Transcribe** for speech-to-text
- **Amazon Bedrock** for natural language understanding and response generation
- **Amazon Polly** for text-to-speech
- **Amazon DynamoDB** for data storage
- **AWS Lambda** for serverless orchestration
- **Amazon API Gateway** for RESTful API

## Project Structure

```
sahayak-ai/
├── src/                    # Source code
│   ├── lambda/            # Lambda function code
│   ├── models/            # Data models
│   ├── services/          # AWS service integrations
│   └── utils/             # Utility functions
├── infrastructure/        # AWS CDK infrastructure code
├── tests/                 # Test suite
│   ├── unit/             # Unit tests
│   ├── integration/      # Integration tests
│   └── property/         # Property-based tests
├── data/                  # Mock data
│   ├── schemes/          # Government scheme data
│   └── applications/     # Mock application data
├── web-ui/               # Frontend web interface
└── docs/                 # Documentation
```

## Setup

### Prerequisites
- Python 3.11+
- AWS CLI configured with appropriate credentials
- Node.js 18+ (for AWS CDK)

### Installation

1. Create and activate virtual environment:
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

3. Install AWS CDK (if not already installed):
```bash
npm install -g aws-cdk
```

4. Set up environment variables:
```bash
cp .env.example .env
# Edit .env with your AWS configuration
```

### Deployment

1. Bootstrap CDK (first time only):
```bash
cd infrastructure
cdk bootstrap
```

2. Deploy infrastructure:
```bash
cdk deploy
```

3. Upload mock data:
```bash
python scripts/upload_mock_data.py
```

## Testing

Run all tests:
```bash
pytest tests/
```

Run with coverage:
```bash
pytest --cov=src tests/
```

Run property-based tests:
```bash
pytest tests/property/ -v
```

## Usage

### Web UI
Access the web interface at the CloudFront URL provided after deployment.

### API
```bash
# Query endpoint
POST /api/query
{
  "audio": "base64-encoded-audio",
  "sessionId": "uuid",
  "language": "hi" | "en"
}

# Create new session
POST /api/session/new

# Health check
GET /api/health
```

## Architecture

The system follows a serverless architecture:
1. User speaks into web UI
2. Audio sent to API Gateway
3. Lambda orchestrates AWS services:
   - Transcribe converts speech to text
   - Bedrock understands intent and generates response
   - DynamoDB provides scheme data
   - Polly converts response to speech
4. Audio response returned to user

## MVP Scope

This MVP includes:
- Hindi and English language support
- 5-6 government schemes
- Voice-based interaction
- Eligibility checking
- Application guidance
- Status checking (mock data)
- Error handling and recovery

## License

MIT License - See LICENSE file for details

## Contributing

This is a hackathon MVP project. For production deployment, additional security, compliance, and government integration work is required.
