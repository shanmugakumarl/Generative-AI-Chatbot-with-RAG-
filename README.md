# 🤖 Generative AI Chatbot with RAG

> A domain-specific Q&A chatbot powered by LangChain, LangGraph, ChromaDB,
> and GPT-4o-mini. Supports document ingestion, semantic retrieval, and
> stateful multi-turn conversations.

---

## 🚀 Live Demo

Run locally → `http://localhost:8001/docs`

---

## 📌 Features

- 📚 **Document Ingestion** — Ingest PDF, TXT, or plain text into ChromaDB vector store
- 🔍 **Semantic Retrieval** — MMR-based retrieval (Maximal Marginal Relevance) for diverse, relevant chunks
- 🧠 **Smart Prompting** — Few-shot + Chain-of-Thought prompting to reduce hallucinations
- 🔄 **Multi-turn Memory** — Stateful conversations using LangGraph with per-session memory
- ⚡ **Streaming** — Token-by-token streaming responses
- 🌐 **REST API** — All features exposed as clean FastAPI JSON endpoints

---

## 🛠️ Tech Stack

| Layer | Tools |
|---|---|
| Language | Python 3.11 |
| API | FastAPI + Uvicorn |
| LLM | GPT-4o-mini (OpenAI) |
| Embeddings | text-embedding-3-small (OpenAI) |
| Vector Store | ChromaDB (local persistence) |
| Retrieval | LangChain MMR Retriever |
| Orchestration | LangGraph (stateful agent) |
| Prompting | Few-shot + Chain-of-Thought |
| Deployment | Docker, docker-compose, AWS EC2 |

---

## 📂 Project Structure
```
rag_chatbot_project/
├── rag_chatbot.py       ← Single-file full project (run this)
├── requirements.txt     ← All dependencies
├── .env                 ← Add your OpenAI API key here
├── HOW_TO_RUN.txt       ← Step-by-step guide
├── Dockerfile           ← Container build
├── docker-compose.yml   ← One-command deployment
└── chroma_db/           ← Auto-created: stores vector embeddings
```

---

## ⚙️ Installation & Run
```bash
# 1. Clone the repository
git clone https://github.com/YOUR_USERNAME/rag-chatbot.git
cd rag_chatbot_project

# 2. Install dependencies
pip install -r requirements.txt

# 3. Add your OpenAI API key
# Open .env file and replace:
OPENAI_API_KEY=sk-your-key-here

# 4. Run
python rag_chatbot.py

# 5. Open interactive docs
# http://localhost:8001/docs
```

> 🔑 Get your free OpenAI API key at → https://platform.openai.com/api-keys

---

## 🔗 API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| GET | `/` | Health check + OpenAI key status |
| POST | `/ingest/text` | Add text to knowledge base |
| POST | `/ingest/file` | Upload PDF / TXT / MD file |
| POST | `/chat` | Single-turn Q&A |
| POST | `/chat/stream` | Streaming token-by-token response |
| POST | `/conversation` | Multi-turn stateful chat (LangGraph) |
| GET | `/history/{session_id}` | Get conversation history |
| DELETE | `/session/{session_id}` | Clear session memory |

---

## 📊 How to Use — Step by Step

### Step 1 — Ingest your knowledge
```bash
curl -X POST http://localhost:8001/ingest/text \
  -H "Content-Type: application/json" \
  -d '{
    "text": "LangChain is a framework for building LLM applications. RAG combines retrieval with generation for accurate answers.",
    "source": "my_notes",
    "collection": "default"
  }'
```

### Step 2 — Ask a question
```bash
curl -X POST http://localhost:8001/chat \
  -H "Content-Type: application/json" \
  -d '{
    "question": "What is LangChain?",
    "collection": "default"
  }'
```

### Step 3 — Multi-turn conversation
```bash
# First message
curl -X POST http://localhost:8001/conversation \
  -H "Content-Type: application/json" \
  -d '{
    "session_id": "user_001",
    "message": "What is RAG?",
    "collection": "default"
  }'

# Follow-up (same session_id keeps memory)
curl -X POST http://localhost:8001/conversation \
  -H "Content-Type: application/json" \
  -d '{
    "session_id": "user_001",
    "message": "How does it reduce hallucinations?",
    "collection": "default"
  }'
```

---

## 📊 Sample Response
```json
{
  "answer": "Step-by-step: LangChain is an open-source Python framework that simplifies building LLM-powered applications. It provides abstractions for chains, memory, agents, and tool use, enabling developers to compose complex AI workflows. [1]",
  "sources": [
    {
      "content": "LangChain is a framework for building LLM applications...",
      "source": "my_notes",
      "page": ""
    }
  ]
}
```

---

## 🏗️ Architecture
```
User Message
     │
     ▼
┌─────────────────────────────────────────┐
│           LangGraph State Machine        │
│                                         │
│  ┌──────────┐  ┌───────────────────┐   │
│  │ Retrieve  │─▶│ Generate Answer   │   │
│  │ ChromaDB  │  │ GPT-4o-mini       │   │
│  │ MMR k=4   │  │ Few-shot + CoT    │   │
│  └──────────┘  └───────────────────┘   │
│  Memory: MemorySaver (per session_id)   │
└─────────────────────────────────────────┘
     │
     ▼
FastAPI Response (answer + sources + session_id)
```

---

## 🐳 Docker Deployment
```bash
# Add your key to .env first, then:
docker-compose up --build

# API available at http://localhost:8001
```

---

## ☁️ AWS EC2 Deployment
```bash
# On EC2 (Ubuntu 22.04)
sudo apt update && sudo apt install -y docker.io docker-compose

git clone https://github.com/YOUR_USERNAME/rag-chatbot.git
cd rag_chatbot_project

echo "OPENAI_API_KEY=sk-your-key" > .env

sudo docker-compose up -d --build

# Open port 8001 in EC2 Security Group
# Access: http://<EC2-PUBLIC-IP>:8001/docs
```

---

## ⚠️ Common Errors & Fixes

| Error | Fix |
|---|---|
| `OPENAI_API_KEY not found` | Add your key to the `.env` file |
| `ModuleNotFoundError` | Run `pip install -r requirements.txt` |
| `python-multipart` error | Run `pip install python-multipart` |
| `Port 8001 already in use` | Change port in last line of `rag_chatbot.py` |
| Empty answers | Make sure you ingested documents first via `/ingest/text` |

---

## 👤 Author

**Shanmugakumar L**
B.Tech — Artificial Intelligence & Data Science
Jansons Institute of Technology, Coimbatore

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://linkedin.com/in/YOUR_PROFILE)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-black)](https://github.com/YOUR_USERNAME)

---

## 📜 License

MIT License — free to use, modify, and distribute.
