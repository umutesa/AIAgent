# umutesacodeX - AI Personal Assistant Chatbot

## Overview

umutesacodeX is an intelligent chatbot system that creates a personalized AI assistant for Umutesa, featuring multiple conversation modes, vector-based knowledge retrieval, audio processing capabilities, and a modern web interface. The system combines n8n workflow automation with OpenAI's language models and Pinecone vector storage.

## Detailed System Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                               CLIENT LAYER                                      │
├─────────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐                 │
│  │   Web Browser   │  │  Mobile WebView │  │   API Client    │                 │
│  │   (HTML/JS/CSS) │  │   (Responsive)  │  │   (Third-party) │                 │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘                 │
└─────────────────────────────────────────────────────────────────────────────────┘
                                    │
                         ┌──────────▼──────────┐
                         │     HTTPS/WSS       │
                         │   Load Balancer     │
                         └──────────┬──────────┘
┌─────────────────────────────────────────────────────────────────────────────────┐
│                            PRESENTATION LAYER                                   │
├─────────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐                 │
│  │  Audio Engine   │  │  UI Controller  │  │ State Manager   │                 │
│  │  ┌───────────┐  │  │  ┌───────────┐  │  │  ┌───────────┐  │                 │
│  │  │    STT    │  │  │  │  Message  │  │  │  │  Session  │  │                 │
│  │  │ (Web API) │  │  │  │ Renderer  │  │  │  │ Storage   │  │                 │
│  │  └───────────┘  │  │  └───────────┘  │  │  └───────────┘  │                 │
│  │  ┌───────────┐  │  │  ┌───────────┐  │  │  ┌───────────┐  │                 │
│  │  │    TTS    │  │  │  │  Mode     │  │  │  │   Cache   │  │                 │
│  │  │(Synthesis)│  │  │  │ Selector  │  │  │  │ Manager   │  │                 │
│  │  └───────────┘  │  │  └───────────┘  │  │  └───────────┘  │                 │
│  │  ┌───────────┐  │  │  ┌───────────┐  │  │  ┌───────────┐  │                 │
│  │  │   Audio   │  │  │  │ Markdown  │  │  │  │   Error   │  │                 │
│  │  │  Player   │  │  │  │  Parser   │  │  │  │ Handler   │  │                 │
│  │  └───────────┘  │  │  └───────────┘  │  │  └───────────┘  │                 │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘                 │
└─────────────────────────────────────────────────────────────────────────────────┘
                                    │
                         ┌──────────▼──────────┐
                         │    REST API Call    │
                         │  POST /webhook/chat │
                         └──────────┬──────────┘
┌─────────────────────────────────────────────────────────────────────────────────┐
│                           API GATEWAY LAYER                                     │
├─────────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐                 │
│  │  n8n Webhook    │  │  Rate Limiter   │  │   CORS Policy   │                 │
│  │    Endpoint     │  │   (100/hour)    │  │    Handler      │                 │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘                 │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐                 │
│  │  Input Validator│  │  Auth Manager   │  │  Error Router   │                 │
│  │   (JSON Schema) │  │   (API Keys)    │  │   (Fallbacks)   │                 │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘                 │
└─────────────────────────────────────────────────────────────────────────────────┘
                                    │
                         ┌──────────▼──────────┐
                         │  Workflow Engine    │
                         │    (n8n Core)       │
                         └──────────┬──────────┘
┌─────────────────────────────────────────────────────────────────────────────────┐
│                         BUSINESS LOGIC LAYER                                    │
├─────────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐                 │
│  │  Mode Router    │  │  Prompt Engine  │  │ Context Manager │                 │
│  │                 │  │                 │  │                 │                 │
│  │ ┌─────────────┐ │  │ ┌─────────────┐ │  │ ┌─────────────┐ │                 │
│  │ │  Switch     │ │  │ │ Dynamic     │ │  │ │  Session    │ │                 │
│  │ │  Logic      │ │  │ │ Template    │ │  │ │  Tracking   │ │                 │
│  │ └─────────────┘ │  │ │ Generation  │ │  │ └─────────────┘ │                 │
│  │ ┌─────────────┐ │  │ └─────────────┘ │  │ ┌─────────────┐ │                 │
│  │ │ Conditional │ │  │ ┌─────────────┐ │  │ │ Conversation│ │                 │
│  │ │ Branching   │ │  │ │ Persona     │ │  │ │   History   │ │                 │
│  │ └─────────────┘ │  │ │ Injection   │ │  │ └─────────────┘ │                 │
│  └─────────────────┘  │ └─────────────┘ │  └─────────────────┘                 │
│                       └─────────────────┘                                      │
└─────────────────────────────────────────────────────────────────────────────────┘
                                    │
                         ┌──────────▼──────────┐
                         │    AI Orchestra     │
                         └──────────┬──────────┘
┌─────────────────────────────────────────────────────────────────────────────────┐
│                           AI PROCESSING LAYER                                   │
├─────────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐                 │
│  │ Memory Manager  │  │  Vector Search  │  │  LLM Interface  │                 │
│  │                 │  │     Engine      │  │                 │                 │
│  │ ┌─────────────┐ │  │ ┌─────────────┐ │  │ ┌─────────────┐ │                 │
│  │ │   Buffer    │ │  │ │  Semantic   │ │  │ │   OpenAI    │ │                 │
│  │ │  Window     │ │  │ │   Query     │ │  │ │ GPT-4o-mini │ │                 │
│  │ │ (50 msgs)   │ │  │ │ Processing  │ │  │ └─────────────┘ │                 │
│  │ └─────────────┘ │  │ └─────────────┘ │  │ ┌─────────────┐ │                 │
│  │ ┌─────────────┐ │  │ ┌─────────────┐ │  │ │   Token     │ │                 │
│  │ │ Conversation│ │  │ │ Similarity  │ │  │ │ Management  │ │                 │
│  │ │ Threading   │ │  │ │  Scoring    │ │  │ └─────────────┘ │                 │
│  │ └─────────────┘ │  │ └─────────────┘ │  │ ┌─────────────┐ │                 │
│  │ ┌─────────────┐ │  │ ┌─────────────┐ │  │ │  Response   │ │                 │
│  │ │   Context   │ │  │ │   Top-K     │ │  │ │ Streaming   │ │                 │
│  │ │   Cleanup   │ │  │ │ Retrieval   │ │  │ └─────────────┘ │                 │
│  │ └─────────────┘ │  │ │   (K=5)     │ │  └─────────────────┘                 │
│  └─────────────────┘  │ └─────────────┘ │                                      │
│                       └─────────────────┘                                      │
└─────────────────────────────────────────────────────────────────────────────────┘
                                    │
                         ┌──────────▼──────────┐
                         │   Data Pipeline     │
                         └──────────┬──────────┘
┌─────────────────────────────────────────────────────────────────────────────────┐
│                            DATA LAYER                                           │
├─────────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐                 │
│  │ Knowledge Base  │  │  Vector Store   │  │  File System    │                 │
│  │                 │  │                 │  │                 │                 │
│  │ ┌─────────────┐ │  │ ┌─────────────┐ │  │ ┌─────────────┐ │                 │
│  │ │ Google      │ │  │ │  Pinecone   │ │  │ │   Audio     │ │                 │
│  │ │ Drive API   │ │  │ │   Index     │ │  │ │   Assets    │ │                 │
│  │ └─────────────┘ │  │ │ (1536-dim)  │ │  │ └─────────────┘ │                 │
│  │ ┌─────────────┐ │  │ └─────────────┘ │  │ ┌─────────────┐ │                 │
│  │ │ Document    │ │  │ ┌─────────────┐ │  │ │   Static    │ │                 │
│  │ │ Processing  │ │  │ │ Embedding   │ │  │ │   Files     │ │                 │
│  │ │   Pipeline  │ │  │ │   Cache     │ │  │ └─────────────┘ │                 │
│  │ └─────────────┘ │  │ └─────────────┘ │  │ ┌─────────────┐ │                 │
│  │ ┌─────────────┐ │  │ ┌─────────────┐ │  │ │   Session   │ │                 │
│  │ │   Text      │ │  │ │ Namespace   │ │  │ │   Storage   │ │                 │
│  │ │ Splitting   │ │  │ │ Management  │ │  │ └─────────────┘ │                 │
│  │ └─────────────┘ │  │ └─────────────┘ │  └─────────────────┘                 │
│  └─────────────────┘  └─────────────────┘                                      │
└─────────────────────────────────────────────────────────────────────────────────┘
                                    │
                         ┌──────────▼──────────┐
                         │  External Services  │
                         └─────────────────────┘
┌─────────────────────────────────────────────────────────────────────────────────┐
│                       EXTERNAL SERVICES LAYER                                   │
├─────────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐                 │
│  │    OpenAI       │  │    Pinecone     │  │  Google Drive   │                 │
│  │                 │  │                 │  │                 │                 │
│  │ ┌─────────────┐ │  │ ┌─────────────┐ │  │ ┌─────────────┐ │                 │
│  │ │   GPT-4o    │ │  │ │   Vector    │ │  │ │ OAuth2 Auth │ │                 │
│  │ │   Model     │ │  │ │  Database   │ │  │ └─────────────┘ │                 │
│  │ └─────────────┘ │  │ └─────────────┘ │  │ ┌─────────────┐ │                 │
│  │ ┌─────────────┐ │  │ ┌─────────────┐ │  │ │   File      │ │                 │
│  │ │ Embeddings  │ │  │ │   Index     │ │  │ │   API       │ │                 │
│  │ │   Model     │ │  │ │ Management  │ │  │ └─────────────┘ │                 │
│  │ └─────────────┘ │  │ └─────────────┘ │  │ ┌─────────────┐ │                 │
│  │ ┌─────────────┐ │  │ ┌─────────────┐ │  │ │ Folder      │ │                 │
│  │ │   API       │ │  │ │   Search    │ │  │ │ Monitoring  │ │                 │
│  │ │ Management  │ │  │ │   Engine    │ │  │ └─────────────┘ │                 │
│  │ └─────────────┘ │  │ └─────────────┘ │  └─────────────────┘                 │
│  └─────────────────┘  └─────────────────┘                                      │
└─────────────────────────────────────────────────────────────────────────────────┘
```

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

# umutesacodeX - AI Personal Assistant Chatbot

## Overview

umutesacodeX is an intelligent chatbot system that creates a personalized AI assistant for Umutesa, featuring multiple conversation modes, vector-based knowledge retrieval, and a modern web interface. The system combines n8n workflow automation with OpenAI's language models and Pinecone vector storage.

## System Architecture

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

### API Integration

```javascript
// Direct API usage
const response = await fetch('https://your-webhook-url/chat', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
        message: "Tell me about your robotics experience",
        mode: "interview",
        sessionId: "unique-session-id"
    })
});

const data = await response.json();
console.log(data.output);
```

### Webhook Request Format

```json
POST /webhook/chat
{
    "message": "Your question here",
    "mode": "default|interview|storytelling|story_images|fast_facts|humble_brag|skills",
    "sessionId": "unique-session-identifier"
}
```

### Response Format

```json
{
    "output": "AI response text with markdown formatting",
    "sessionId": "session-identifier",
    "mode": "selected-mode",
    "timestamp": "2025-01-01T12:00:00.000Z"
}
```

## Configuration Options

### Workflow Parameters

```javascript
// Memory settings
contextWindowLength: 50  // Number of messages to remember

// Vector search
topK: 5  // Number of relevant documents to retrieve

// Embedding settings
embeddingBatchSize: 1536  // OpenAI embedding dimensions
```

### Frontend Customization

```css
/* Theme colors in index.html */
:root {
    --primary-color: #2563eb;
    --background-color: rgba(255, 255, 255, 0.685);
    --border-color: #e0e0e0;
}
```

## Monitoring & Maintenance

### Health Checks

1. **n8n Workflow Status**
   - Monitor execution logs
   - Check webhook endpoint availability
   - Verify API key validity

2. **Vector Store Health**
   ```python
   # Check Pinecone index stats
   index = pinecone.Index("indexspace")
   stats = index.describe_index_stats()
   print(f"Vector count: {stats['total_vector_count']}")
   ```

3. **Document Sync**
   - Monitor Google Drive folder changes
   - Run document processing workflow regularly
   - Verify embedding generation

### Performance Optimization

1. **Caching Strategy**
   ```javascript
   // Implement response caching for common queries
   const cache = new Map();
   if (cache.has(messageHash)) {
       return cache.get(messageHash);
   }
   ```

2. **Rate Limiting**
   ```javascript
   // Add request throttling
   const rateLimiter = {
       requests: 0,
       resetTime: Date.now() + 60000
   };
   ```

## Security Considerations

### API Security
- Use HTTPS for all communications
- Implement proper CORS headers
- Validate input parameters
- Rate limit webhook endpoints

### Data Privacy
- Encrypt sensitive documents
- Implement session expiration
- Log minimal personal information
- Comply with data protection regulations

## Troubleshooting

### Common Issues

1. **Webhook Not Responding**
   ```bash
   # Check n8n workflow status
   # Verify webhook URL configuration
   # Test with curl:
   curl -X POST https://your-webhook/chat \
        -H "Content-Type: application/json" \
        -d '{"message":"test","mode":"default","sessionId":"test"}'
   ```

2. **Vector Search Failures**
   ```python
   # Verify Pinecone connection
   import pinecone
   pinecone.init(api_key="your-key")
   index = pinecone.Index("indexspace")
   print(index.describe_index_stats())
   ```

3. **Frontend Issues**
   ```javascript
   // Check browser console for errors
   // Verify CORS configuration
   // Test with simple fetch request
   ```

### Debug Mode

Enable debug logging in n8n workflow:
```javascript
// Add debug nodes to capture:
- Input validation results
- API response status
- Vector search results
- Final output formatting
```

## Deployment Options

### Cloud Deployment

1. **n8n Cloud**
   - Use hosted n8n service
   - Automatic scaling and backups
   - Built-in credential management

2. **Frontend Hosting**
   ```bash
   # Netlify deployment
   npm install -g netlify-cli
   netlify deploy --dir=. --prod
   
   # Vercel deployment
   npm install -g vercel
   vercel --prod
   ```

### Self-Hosted Option

```docker
# Docker Compose setup
version: '3.8'
services:
  n8n:
    image: n8nio/n8n
    ports:
      - "5678:5678"
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=password
    volumes:
      - n8n_data:/home/node/.n8n
      
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./index.html:/usr/share/nginx/html/index.html
```

## Contributing

### Development Workflow

1. **Fork Repository**
2. **Create Feature Branch**
3. **Test Changes Locally**
4. **Submit Pull Request**

### Code Standards

- Use meaningful variable names
- Add comments for complex logic
- Follow existing formatting patterns
- Test all conversation modes

## License

This project is licensed under the MIT License. See LICENSE file for details.

## Support

For issues and questions:
- Create GitHub issue for bugs
- Check n8n documentation for workflow questions
- Review OpenAI API documentation for model issues

---

**Version**: 1.0.0  
**Last Updated**: September 2025  
**Maintainer**: Umutesa Munyurangabo
