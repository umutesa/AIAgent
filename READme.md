# umutesacodeX - AI Personal Chatbot

## Overview

umutesacodeX is an intelligent chatbot system that represent Umutesa, featuring multiple conversation modes, vector-based knowledge retrieval, audio processing capabilities, and a modern web interface. The system combines n8n workflow automation with OpenAI's language models and Pinecone vector storage.

## Detailed System Architecture

### Audio Processing Architecture

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                           AUDIO PROCESSING PIPELINE                             │
└─────────────────────────────────────────────────────────────────────────────────┘

INPUT AUDIO FLOW:
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   User      │───▶│ Microphone  │───▶│   Browser   │───▶│   Speech    │
│   Speech    │    │   Capture   │    │   Web API   │    │Recognition  │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
                                                                  │
                   ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
                   │ Text Input  │◀───│ Transcript  │◀───│   Engine    │
                   │  Component  │    │ Processing  │    │ (WebKit/Web)│
                   └─────────────┘    └─────────────┘    └─────────────┘

OUTPUT AUDIO FLOW:
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│ AI Response │───▶│   Text      │───▶│  Browser    │───▶│   Audio     │
│    Text     │    │ Sanitizer   │    │  Synthesis  │    │   Output    │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
                                              │
                   ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
                   │ User Audio  │◀───│   Voice     │◀───│  Speech     │
                   │ Experience  │    │ Controls    │    │ Synthesis   │
                   └─────────────┘    └─────────────┘    └─────────────┘

PRONUNCIATION SYSTEM:
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Static    │───▶│   Audio     │───▶│  HTML5      │───▶│  Speaker    │
│   Files     │    │   Buffer    │    │   Player    │    │   Output    │
│(umutesa.mp3)│    │             │    │             │    │             │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
```

### Data Flow Architecture

```
REQUEST FLOW:
Client ──┬──▶ STT Processing ──┬──▶ Input Validation ──▶ Mode Routing
         │                    │
         └──▶ Text Input ──────┘

MODE PROCESSING:
Mode Router ──▶ Prompt Template ──▶ Context Injection ──▶ AI Agent

KNOWLEDGE RETRIEVAL:
User Query ──▶ Embedding Generation ──▶ Vector Search ──▶ Context Retrieval
                      │                       │                  │
                      ▼                       ▼                  ▼
              OpenAI Embeddings         Pinecone Index     Top-K Results

AI PROCESSING:
Context + Query ──▶ LLM Processing ──▶ Response Generation ──▶ Output Formatting
     │                    │                     │                    │
     ▼                    ▼                     ▼                    ▼
Memory Buffer      GPT-4o-mini Model    Token Management    Markdown/JSON

RESPONSE FLOW:
Formatted Response ──┬──▶ Text Display ──▶ Markdown Rendering
                     │
                     └──▶ TTS Processing ──▶ Audio Playback
```



## Overall System Architecture

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Web Frontend  │───▶│   n8n Webhook    │───▶│  Mode Processor │
│   (HTML/JS)     │    │   (REST API)     │    │   (Switch)      │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                                         │
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│  Google Drive   │───▶│  Document Store  │    │   AI Agent      │
│   (Knowledge)   │    │   (Processing)   │    │ (GPT-4o-mini)   │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                │                        │
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│ Pinecone Vector │◀───│   Embeddings     │    │  Memory Buffer  │
│     Store       │    │   (OpenAI)       │    │  (50 messages)  │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                │                        │
                       ┌──────────────────┐    ┌─────────────────┐
                       │ Vector Retrieval │◀───│ Response Format │
                       │     Tool         │    │   & Output      │
                       └──────────────────┘    └─────────────────┘
```

## Core Components

### 1. Frontend Interface (`index.html`)
- **Modern Chat UI**: Clean, responsive design with message bubbles
- **Mode Selection**: 7 different conversation modes
- **Voice Features**: Speech-to-text input and text-to-speech output
- **Markdown Support**: Rich text rendering with tables, images, code blocks
- **Real-time Status**: Connection and processing status indicators

### 2. n8n Workflow Engine (`umutesacodeX.json`)
- **Webhook Endpoint**: REST API for chat interactions
- **Mode Routing**: Dynamic prompt generation based on selected mode
- **Document Processing**: Automated knowledge base updates
- **AI Integration**: OpenAI GPT-4o-mini with memory and vector search

### 3. Knowledge Management System
- **Google Drive Integration**: Automatic document synchronization
- **Vector Storage**: Pinecone for semantic search capabilities
- **Document Processing**: Recursive text splitting and embedding generation

## Conversation Modes

| Mode | Description | Use Case |
|------|-------------|----------|
| **Default** | Balanced, friendly responses | General conversation |
| **Interview** | Professional, concise answers | Job interview preparation |
| **Storytelling** | Narrative, reflective responses | Personal experiences |
| **Story Images** | Visual storytelling with images | Enhanced narratives |
| **Fast Facts** | Quick, bullet-point format | Rapid information delivery |
| **Humble Brag** | Confident self-promotion | Showcasing achievements |
| **Skills** | Professional analysis tables | Technical skill assessment |

## Prerequisites

### Required Services
- **n8n Cloud Account** or self-hosted n8n instance
- **OpenAI API Key** (GPT-4o-mini access)
- **Pinecone Account** with vector index
- **Google Drive API** access
- **Web Server** for hosting frontend

### API Keys Needed
```
OPENAI_API_KEY=your_openai_key
PINECONE_API_KEY=your_pinecone_key
GOOGLE_DRIVE_CREDENTIALS=your_google_credentials
```

## Installation & Setup

### Step 1: n8n Workflow Setup

1. **Import Workflow**
   ```bash
   # In n8n interface
   1. Go to Workflows
   2. Click "Import from file"
   3. Upload umutesacodeX.json
   ```

2. **Configure Credentials**
   ```bash
   # Set up in n8n Credentials:
   - OpenAI API (for GPT-4o-mini and embeddings)
   - Pinecone API (for vector storage)
   - Google Drive OAuth2 (for document access)
   ```

3. **Create Pinecone Index**
   ```python
   # Pinecone setup
   import pinecone
   
   pinecone.init(api_key="your-key")
   pinecone.create_index(
       name="indexspace",
       dimension=1536,
       metric="cosine"
   )
   ```

4. **Set Google Drive Folder**
   ```bash
   # Update folder ID in workflow:
   folderId: "1NtYJfbKmZCoAi4bfwIMvoGnLK4DqUOZ6"
   ```

### Step 2: Frontend Deployment

1. **Update Webhook URL**
   ```javascript
   // In index.html, update:
   const WEBHOOK_URL = 'https://your-n8n-instance.com/webhook/chat';
   ```

2. **Deploy to Web Server**
   ```bash
   # Option 1: Simple HTTP server
   python -m http.server 8000
   
   # Option 2: Deploy to hosting service
   # Upload index.html to Netlify, Vercel, or similar
   ```

3. **Configure Audio Assets** (Optional)
   ```bash
   # Create voice/ directory
   mkdir voice
   # Add umutesa.mp3 pronunciation file
   ```

### Step 3: Knowledge Base Setup

1. **Prepare Documents**
   ```bash
   # Upload documents to Google Drive folder:
   - Resume/CV files
   - Project descriptions
   - Technical documentation
   - Personal information
   ```

2. **Initial Processing**
   ```bash
   # In n8n workflow:
   1. Click "Execute workflow" trigger
   2. Documents will be processed and vectorized
   3. Verify Pinecone index population
   ```

## Usage

### Basic Chat Interaction

1. **Select Mode**: Choose appropriate conversation style
2. **Type/Speak**: Enter question or use voice input
3. **Get Response**: AI responds based on knowledge and mode
4. **Continue**: Maintain conversation context automatically


---

**Version**: 1.0.0  
**Last Updated**: September 2025  
**Maintainer**: Umutesa Munyurangabo
