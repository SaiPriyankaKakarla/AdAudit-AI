# AdAudit AI

AdAudit AI is a multimodal LLMOps compliance auditing system that analyzes YouTube advertisements for brand safety and regulatory compliance.

The system accepts a YouTube video URL, extracts transcript and visual text from the video, retrieves relevant policy rules using RAG, and generates a structured PASS or FAIL audit report using Azure OpenAI, LangGraph, Azure AI Search, and Azure AI Video Indexer.

This project was built to demonstrate practical AI engineering skills across multimodal AI, retrieval augmented generation, workflow orchestration, cloud AI services, API development, and production observability.

---

## Project Overview

Advertising and brand teams often need to review video content before publishing to ensure that it follows platform policies, disclosure requirements, and brand safety rules.

AdAudit AI automates that review process by combining:

- Video understanding
- OCR and transcript extraction
- Compliance document retrieval
- LLM based reasoning
- Structured API output
- Workflow tracing and cloud telemetry

The result is an end to end AI system that can inspect advertisement content and return a clear compliance report.

---

## What the System Does

Given a YouTube video URL, AdAudit AI performs the following steps:

1. Downloads the video using `yt-dlp`
2. Uploads and processes the video through Azure AI Video Indexer
3. Extracts transcript and OCR text from the video
4. Retrieves relevant compliance rules from Azure AI Search
5. Sends the video content and retrieved rules to Azure OpenAI GPT 4o
6. Generates a structured PASS or FAIL compliance report
7. Exposes the workflow through a FastAPI backend
8. Tracks LLM workflow execution with LangSmith
9. Tracks backend telemetry with Azure Monitor and Application Insights

---

## Architecture

```text
YouTube Video URL
        ↓
Video Download Service
        ↓
Azure AI Video Indexer
        ↓
Transcript + OCR Extraction
        ↓
Azure AI Search
RAG Based Policy Retrieval
        ↓
Azure OpenAI GPT 4o
Compliance Reasoning
        ↓
LangGraph Workflow
        ↓
FastAPI API Response
        ↓
LangSmith + Azure Monitor Observability
```
---
## Key Features

- Multimodal video analysis using transcript and OCR signals
- RAG based compliance checking against advertising and platform policy documents
- Azure OpenAI GPT 4o based audit reasoning
- Azure AI Search vector index for compliance rule retrieval
- Azure AI Video Indexer integration for transcript and visual text extraction
- LangGraph based workflow orchestration
- FastAPI backend with Swagger API documentation
- LangSmith tracing for LLM workflow observability
- Azure Monitor and Application Insights for backend telemetry
- Structured JSON audit output
- Secure configuration using `.env.example` and ignored secrets

---

## Tech Stack

| Area | Tools |
|---|---|
| Language | Python |
| API Framework | FastAPI |
| Workflow Orchestration | LangGraph |
| LLM Framework | LangChain |
| LLM Provider | Azure OpenAI GPT 4o |
| Embeddings | Azure OpenAI text embedding model |
| Vector Search | Azure AI Search |
| Video Processing | Azure AI Video Indexer |
| Storage | Azure Blob Storage |
| Observability | LangSmith, Azure Monitor, Application Insights |
| Telemetry | OpenTelemetry |
| Package Management | uv |
| Validation | Pydantic |
| Video Download | yt-dlp |

---

## Repository Structure

```text
AdAudit-AI/
│
├── README.md
├── .env.example
├── .gitignore
├── pyproject.toml
├── uv.lock
├── main.py
│
├── backend/
│   ├── data/
│   │   ├── 1001a-influencer-guide-508_1.pdf
│   │   └── youtube-ad-specs.pdf
│   │
│   ├── scripts/
│   │   └── index_documents.py
│   │
│   └── src/
│       ├── api/
│       │   ├── server.py
│       │   └── telemetry.py
│       │
│       ├── graph/
│       │   ├── state.py
│       │   ├── nodes.py
│       │   └── workflow.py
│       │
│       └── services/
│           └── video_indexer.py
```

---

## Main Components

### FastAPI Backend

The API layer exposes the compliance workflow through HTTP endpoints.

Main endpoints:

```text
POST /audit
GET /health
```

The `/audit` endpoint accepts a YouTube video URL and returns a structured compliance report.

---

### LangGraph Workflow

LangGraph is used to orchestrate the audit pipeline.

Current workflow:

```text
START → Video Indexer Node → Audit Node → END
```

The graph maintains shared state across the workflow, including:

- Video URL
- Video ID
- Transcript
- OCR text
- Compliance results
- Final status
- Final report
- Errors

---

### Azure AI Video Indexer

Azure AI Video Indexer processes the uploaded video and extracts:

- Spoken transcript
- OCR text visible in the video
- Video metadata

This allows the system to analyze both what is said and what appears visually in the advertisement.

---

### Azure AI Search

Azure AI Search is used as the vector search layer for RAG.

Compliance documents are loaded, chunked, embedded, and indexed. During an audit, the system retrieves the most relevant policy context based on the video transcript and OCR content.

---

### Azure OpenAI

Azure OpenAI GPT 4o performs the final compliance reasoning.

The model receives:

- Transcript
- OCR text
- Retrieved compliance rules

It returns a structured JSON report containing:

- PASS or FAIL status
- Final summary
- Compliance issues
- Severity levels
- Descriptions of violations

---

### LangSmith Observability

LangSmith is used to trace the LLM workflow.

It helps debug:

- LangGraph execution flow
- LLM calls
- Retrieval steps
- Prompt inputs
- Model outputs
- Errors during workflow execution

---

### Azure Monitor Telemetry

Azure Application Insights and OpenTelemetry are used to monitor the backend API.

This captures:

- API requests
- Response times
- Errors
- Logs
- Backend performance
- Application health

---

## API Usage

Start the API server:

```bash
uv run uvicorn backend.src.api.server:app --reload
```

Open Swagger UI:

```text
http://localhost:8000/docs
```

---

## Example API Request

```http
POST /audit
Content-Type: application/json
```

```json
{
  "video_url": "https://youtu.be/example"
}
```

---

## Example API Response

```json
{
  "session_id": "sample-session-id",
  "video_id": "vid_12345678",
  "status": "FAIL",
  "final_report": "The video contains compliance issues related to unclear sponsorship disclosure and unsupported claims.",
  "compliance_results": [
    {
      "category": "Disclosure",
      "severity": "HIGH",
      "description": "The advertisement does not clearly disclose sponsorship or paid promotion."
    },
    {
      "category": "Misleading Claim",
      "severity": "MEDIUM",
      "description": "The advertisement includes a performance claim without sufficient supporting context."
    }
  ]
}
```

---

## Local Setup

### 1. Clone the repository

```bash
git clone https://github.com/SaiPriyankaKakarla/AdAudit-AI.git
cd AdAudit-AI
```

### 2. Create a virtual environment

```bash
uv venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
uv sync
```

### 4. Configure environment variables

Create a `.env` file using `.env.example` as a template.

```bash
cp .env.example .env
```

Add your own Azure and LangSmith credentials locally.

Required environment variables include:

```env
AZURE_OPENAI_ENDPOINT=
AZURE_OPENAI_API_KEY=
AZURE_OPENAI_CHAT_DEPLOYMENT=
AZURE_OPENAI_EMBEDDING_DEPLOYMENT=
AZURE_OPENAI_API_VERSION=

AZURE_SEARCH_ENDPOINT=
AZURE_SEARCH_KEY=
AZURE_SEARCH_INDEX_NAME=

AZURE_VI_NAME=
AZURE_VI_ACCOUNT_ID=
AZURE_VI_LOCATION=
AZURE_SUBSCRIPTION_ID=
AZURE_RESOURCE_GROUP=

APPLICATIONINSIGHTS_CONNECTION_STRING=

LANGCHAIN_TRACING_V2=
LANGCHAIN_ENDPOINT=
LANGCHAIN_API_KEY=
LANGCHAIN_PROJECT=
```

---

## Index Compliance Documents

Before running audits, index the compliance documents into Azure AI Search:

```bash
uv run python3 backend/scripts/index_documents.py
```

This script:

1. Loads compliance PDF documents
2. Splits documents into chunks
3. Creates embeddings using Azure OpenAI
4. Uploads embedded chunks to Azure AI Search

---

## Run the Project

### Run as a script

```bash
uv run python3 main.py
```

### Run as an API

```bash
uv run uvicorn backend.src.api.server:app --reload
```

Then open:

```text
http://localhost:8000/docs
```

---

## Sample Workflow

```text
Input:
YouTube advertisement URL

Processing:
Download video
Extract transcript and OCR
Retrieve relevant policy rules
Run compliance audit using GPT 4o
Generate structured report

Output:
PASS or FAIL compliance report with issue descriptions
```

---

## Why This Project Matters

This project demonstrates how modern AI applications can move beyond simple chatbot interfaces.

AdAudit AI shows how LLMs can be integrated into a real workflow with:

- External data ingestion
- Multimodal content extraction
- Retrieval augmented generation
- Workflow orchestration
- API serving
- Cloud AI services
- Observability
- Structured outputs

These are key skills for AI Engineer, Applied AI Engineer, GenAI Engineer, and LLMOps roles.

---

## Engineering Highlights

- Designed a modular AI pipeline with separate API, graph, service, and indexing layers
- Used LangGraph to manage multi step workflow execution
- Integrated Azure AI Video Indexer for transcript and OCR based video understanding
- Built a RAG pipeline with Azure AI Search and Azure OpenAI embeddings
- Used GPT 4o for structured compliance reasoning
- Exposed the system through FastAPI with typed request and response models
- Added LangSmith tracing for LLM workflow debugging
- Added Azure Monitor telemetry for production style backend observability
- Protected secrets using `.env.example` and `.gitignore`

---

## Security Notice

This repository does not include real API keys, connection strings, or cloud credentials.

Use `.env.example` as a reference and configure secrets locally.

The real `.env` file is intentionally excluded from GitHub.

---

## Future Improvements

- Add frontend dashboard for video audit submissions
- Add authentication for API access
- Store audit history in a database
- Add async background processing for long video audits
- Add confidence scores for compliance findings
- Add human review workflow for flagged violations
- Add support for multiple compliance policy sets
- Add automated evaluation for LLM output quality
- Deploy API to Azure Container Apps

---

## Project Status

This project is a working portfolio implementation built to demonstrate end to end AI engineering and LLMOps concepts.

It is designed for learning, experimentation, and showcasing production style AI system design.

---

## Author

Sai Priyanka Kakarla

Project focus:

- AI Engineering
- LLMOps
- RAG Systems
- Multimodal AI
- FastAPI
- Azure AI Services
- LangGraph Workflows
