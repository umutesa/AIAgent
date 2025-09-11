# umutesacodeX - AI Personal Chatbot

## Overview

umutesacodeX is an intelligent chatbot system that represent Umutesa, featuring multiple conversation modes, vector-based knowledge retrieval, audio processing capabilities, and a modern web interface. The system combines n8n workflow automation with OpenAI's language models and Pinecone vector storage.

## Features

### **1. Multi-Modal Conversation Handling**
- Supports **seven distinct interaction styles** for seamless and intuitive communication.
- Enables dynamic switching between interaction modes based on user preferences or context.

### **2. Dynamic Knowledge Base**
- Built with a **RAG (Retrieval-Augmented Generation)**-based context-aware knowledge base.
- Ensures accurate, up-to-date, and contextually relevant responses.

### **3. Advanced Speech Integration**
- Fully integrated **speech-to-text** and **text-to-speech** capabilities.
- Provides natural and expressive audio interactions for enhanced accessibility.

### **4. Responsive Web Interface**
- Modern and accessible front-end design built using **HTML, CSS, and JavaScript**.
- Fully responsive layout optimized for both desktop and mobile devices.

### **5. Multi-Input and Multi-Output**
- **Input Options**:
  - Text-based input.
  - Audio-based input (speech recognition).
- **Output Options**:
  - **Text**: Clear and concise responses.
  - **Graphs**: Visual data representation.
  - **Tables**: Tabular data for structured outputs.
  - **Images**: Rich media support for context-specific visuals.
  - **Audio**: Spoken responses with text-to-speech.
  - **References**: Proper citations alongside generated content.

### **6. Cultural Sensitivity**
- **Pronunciation Features**:
  - Handles proper name pronunciation using advanced phonetic algorithms.
  - Includes localized pronunciation support for diverse cultural contexts.

![AiAgent Demo](images/demo.gif)

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
Client ──┬──▶ STT Processing (Speech to text )  ──┬──▶ Input Validation ──▶ Mode Routing
         │                    │
         └──▶ Text Input ──────┘

MODE PROCESSING:
Mode Router ──▶ Prompt Template ──▶ Context Processing ──▶ AI Agent

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
- **Modern Chat UI**: Clean, responsive design 
- **Mode Selection**: 7 different conversation modes
- **Voice Features**: Speech-to-text input and text-to-speech output
- **Markdown Support**: Rich text rendering with tables, images, and Audio
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
- **Node.js** (version 14.x or higher)
- **npm** (comes with Node.js)
- **http-server** (install instructions below)

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
   download n8n-workflows/umutesaCodeX.json script from the repositiory
   
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
   folderId: "FoderID"
   ```

### Step 2: Frontend Deployment

### 1. Clone the Repository
Clone this repository to your local machine:
```bash
git clone https://github.com/umutesa/<repository-name>.git
cd <repository-name>
```

---

### 2. Install Dependencies
Download all required dependencies using `npm`:
```bash
npm install
```

---

### 3. Set Up the `.env` File
Create a `.env` file in the root directory of the project:
```bash
touch .env
```

Add your AI key and other environment variables to the `.env` file:
```env
AI_KEY=your-ai-key-here
OTHER_ENV_VAR=your-value-here
```

**Update Webhook URL in index.html : Ensure it is copied from the webhook in the n8n workflow**
   ```javascript
   // In index.html, update:
   const WEBHOOK_URL = 'https://umutesa.app.n8n.cloud/webhook/chat'; 
   ```

---


### 4. Configure `.gitignore`
Ensure that sensitive files like `.env` and other unnecessary files are excluded from version control. Add the following to the `.gitignore` file:
```
# Environment variables
.env

# Node modules
node_modules/

# Logs
*.log
```

---

### 5. Start the HTTP Server
Install `http-server` globally if you don’t already have it:
```bash
npm install -g http-server
```

Run the project using `http-server`:
```bash
http-server
```

By default, the server will be available at: (once deploy )
```
http://localhost:8080
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
