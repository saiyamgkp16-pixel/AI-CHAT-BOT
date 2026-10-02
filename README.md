# Multimodal AI Chatbot (MVP)

A beginner-friendly, unified Multimodal Personal AI Chatbot MVP built with **React**, **FastAPI**, **Python**, and **PostgreSQL**.

The application combines **Text, Image Vision, Voice Audio (STT & TTS), Document RAG (PDF, DOCX, TXT), Web Search, and Natural Language Document Creation/Updating** into a seamless, modern conversational interface.

---

## 🚀 Key Features

### 1. Multimodal Input & Output
- **Text**: Natural conversational intelligence with contextual conversation memory.
- **Vision / Images**: Upload images (JPG, PNG, WebP) and discuss diagrams, charts, or visual information.
- **Documents & RAG**: Upload PDF, DOCX, TXT, or Markdown files. Text is automatically cleaned, chunked, and embedded into vector storage for grounded retrieval.
- **Voice / Audio Input (STT)**: Live microphone speech-to-text powered by browser-native Web Speech API or backend `/api/stt` endpoint.
- **Voice / Audio Output (TTS)**: Instant Text-to-Speech audio generation via backend `/api/tts` with gTTS streaming and browser speech synthesis.

### 2. Live Web Search & Grounding
- Seamlessly retrieves real-time facts and news using zero-key DuckDuckGo search or Tavily when current information is required.
- Cites source titles, URLs, and snippets underneath answers.

### 3. Document Creation, Updating & Version History
- Create and edit study documents using natural language instructions (e.g., *"Create a study note on DBMS normalization"*, *"Add a section about 3NF"*).
- The backend creates an immutable snapshot in `document_versions` on every modification, allowing you to review prior revisions.

### 4. Personal Emotional Assistance & Boundaries
- Warm, compassionate conversational support for study stress, motivation, and everyday reflections.
- **Safety Boundary**: Clearly identifies as an AI companion, avoids clinical diagnoses, and advises professional human support during crises.

### 5. Security & Prompt Injection Defense
- Treats uploaded documents, retrieved chunks, and web search results as **untrusted data**.
- Strict prompt boundaries isolate system instructions from external text to prevent prompt injection overrides.
- Filename sanitization, file extension checking, and file size limits prevent directory traversal.

---

## 🏗️ Architecture

```
                 USER
                   │
          ┌────────┼────────┐
          │        │        │
        TEXT     IMAGE    AUDIO
          │        │        │
          │      VISION    STT
          │        │        │
          └────────┼────────┘
                   │
             MULTIMODAL LLM
                   │
       ┌───────────┼───────────┐
       │           │           │
      RAG      WEB SEARCH    MEMORY
       │           │           │
   PostgreSQL   Search API  PostgreSQL
   + pgvector
       │           │           │
       └───────────┼───────────┘
                   │
                RESPONSE
                /      \
             TEXT      TTS (Audio)
```

---

## 📂 Project Structure

```
d:/PROJECTS/AI CHAT BOT/
├── backend/
│   ├── app/
│   │   ├── api/
│   │   │   ├── audio.py        # /api/tts & /api/stt
│   │   │   ├── chat.py         # /api/chat & /api/conversations
│   │   │   ├── documents.py    # /api/documents CRUD & versions
│   │   │   ├── files.py        # /api/files/upload & parsing
│   │   │   └── search.py       # /api/search & /api/rag/search
│   │   ├── services/
│   │   │   ├── document_service.py # Document management & actions
│   │   │   ├── llm_service.py      # Provider-agnostic LLM caller
│   │   │   ├── rag_service.py      # Embedding & similarity search
│   │   │   ├── search_service.py   # DuckDuckGo & Tavily search
│   │   │   ├── stt_service.py      # Speech-to-Text
│   │   │   ├── tts_service.py      # Text-to-Speech
│   │   │   └── vision_service.py   # Image validation & processing
│   │   ├── models/
│   │   │   └── models.py       # SQLAlchemy ORM models
│   │   ├── schemas/
│   │   │   └── schemas.py      # Pydantic v2 schemas
│   │   ├── database/
│   │   │   └── session.py      # PostgreSQL & SQLite fallback engine
│   │   ├── utils/
│   │   │   ├── document_parser.py # PDF/DOCX/TXT text extraction
│   │   │   └── security.py        # Sanitization & safe prompt builder
│   │   ├── config.py           # Pydantic Settings
│   │   └── main.py             # FastAPI app entry point
│   ├── tests/
│   │   └── test_api.py         # 100% automated test suite
│   ├── requirements.txt
│   └── .env.example
│
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── ChatInput.jsx    # Input capsule with media buttons
│   │   │   ├── ChatMessage.jsx  # Bubbles with TTS, citations & badges
│   │   │   ├── DocumentModal.jsx # Document editor & version history
│   │   │   ├── QuickPrompts.jsx # Starter cards for instant actions
│   │   │   └── Sidebar.jsx      # Conversation & document navigation
│   │   ├── services/
│   │   │   └── api.js           # API client
│   │   ├── App.jsx              # Main application view
│   │   ├── index.css            # Dark glassmorphic design system
│   │   └── main.jsx
│   ├── package.json
│   └── vite.config.js
│
├── .gitignore
├── .env.example
└── README.md
```

---

## 🛠️ Local Setup Guide

### Prerequisites
- **Python 3.10+** (Tested on Python 3.13)
- **Node.js 18+** (Tested on Node.js 24)
- **PostgreSQL** (Optional: application seamlessly uses local SQLite fallback if PostgreSQL is not active)

---

### 1. Backend Setup

```bash
# Navigate to backend directory
cd backend

# Create Python virtual environment
python -m venv venv

# Activate virtual environment
# Windows:
venv\Scripts\activate
# Linux/macOS:
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# (Optional) Copy environment file and configure API keys
cp .env.example .env
```

#### Run Backend Server:
```bash
uvicorn app.main:app --host 127.0.0.1 --port 8000 --reload
```

- **API Documentation**: Open [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs) in your browser.
- **Health Check**: Open [http://127.0.0.1:8000/](http://127.0.0.1:8000/).

---

### 2. Frontend Setup

Open a new terminal window:

```bash
# Navigate to frontend directory
cd frontend

# Install node packages
npm install

# Start Vite development server
npm run dev
```

- Open [http://127.0.0.1:5173/](http://127.0.0.1:5173/) in your browser.

---

### 3. Run Automated Tests

To run the complete backend test suite:

```bash
cd backend
venv\Scripts\activate
pytest tests/test_api.py -v
```

All 8 endpoint tests will execute:
- `test_health_check`
- `test_chat_text_flow`
- `test_emotional_assistance_query`
- `test_document_creation_and_versions`
- `test_file_upload_and_rag`
- `test_web_search_endpoint`
- `test_tts_audio_endpoint`
- `test_security_prompt_injection_safety`

---

## 🔐 Environment Variables (`.env`)

| Variable | Description | Default |
|---|---|---|
| `DATABASE_URL` | PostgreSQL connection string | `postgresql://postgres:postgres@localhost:5432/multimodal_chatbot` |
| `LLM_PROVIDER` | AI provider (`gemini` or `openai` or `demo`) | `gemini` |
| `LLM_API_KEY` | Google Gemini or OpenAI API Key | `your_api_key_here` |
| `SEARCH_API_KEY` | Optional Tavily/SerpApi key (DuckDuckGo works with 0 keys) | Empty |
| `STT_API_KEY` | Optional Whisper STT key (Web Speech works in browser) | Empty |
| `TTS_API_KEY` | Optional TTS key (gTTS works built-in) | Empty |

---

## 📄 License
MIT License. Built for educational and MVP showcase purposes.
