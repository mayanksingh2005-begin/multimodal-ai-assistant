# SRDT Multimodal AI Chatbot

A full-stack multimodal chatbot MVP with a Streamlit interface and FastAPI backend. It supports conversational AI, image understanding, document-based retrieval (RAG), speech output, image generation/upscaling, web-search grounding, and provider fallback.

## Technology

- **Frontend:** Streamlit
- **Backend:** FastAPI + Uvicorn
- **Persistence:** SQLAlchemy; SQLite for local development, PostgreSQL/pgvector option
- **AI providers:** Google Gemini, xAI Grok, optional Groq; deterministic mock provider for offline demos/tests
- **Document processing:** PDF, DOCX, TXT/Markdown parsing and chunking
- **Multimodal services:** vision, text-to-speech, image generation and image enhancement

## Project layout

```text
SRDT-MM-Chatbot-main/
├── backend/
│   ├── app/
│   │   ├── api/          # Chat, models, documents, multimodal endpoints
│   │   ├── core/         # Environment-based configuration
│   │   ├── db/           # SQLAlchemy sessions and vector adapter
│   │   ├── models/       # Database models
│   │   ├── schemas/      # Request/response schemas
│   │   ├── services/     # Providers, RAG, search, audio, vision, images
│   │   └── utils/
│   ├── tests/
│   └── requirements.txt
├── frontend/             # Streamlit UI and API client
├── scripts/              # Database initialization SQL and test harness
├── .env.example
├── PROJECT_REPORT.pdf
├── PROJECT_REPORT.md
└── automation_test_report.json
```

## Requirements

- Python 3.10 or newer (Python 3.13 was used in the supplied verification notes).
- Internet connection for cloud providers, web search, and online image generation.
- API keys are optional for local mock-mode demonstrations.

## Setup (Windows PowerShell)

Run commands from the project root:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r backend/requirements.txt -r frontend/requirements.txt
Copy-Item .env.example .env
```

Edit `.env` and add the provider keys you intend to use. Leave keys blank to use the mock provider for supported flows. Do not commit `.env` or share API keys.

## Run

Open two terminals, with the virtual environment activated in both.

**Terminal 1 — API:**

```powershell
python -m uvicorn backend.app.main:app --host 127.0.0.1 --port 8000 --reload
```

**Terminal 2 — UI:**

```powershell
python -m streamlit run frontend/app.py
```

Open `http://localhost:8501`. API documentation is at `http://127.0.0.1:8000/docs`; health status is at `http://127.0.0.1:8000/health`.

## Configuration

Important variables in `.env.example`:

| Variable | Purpose |
|---|---|
| `GEMINI_API_KEY` | Google Gemini access |
| `GROK_API_KEY` | xAI access |
| `GROQ_API_KEY` | Groq-hosted model access |
| `DEFAULT_MODEL` | Preferred provider/model or `auto` |
| `DATABASE_URL` | SQLite default or PostgreSQL connection string |
| `CHUNK_SIZE`, `CHUNK_OVERLAP` | RAG document chunking |
| `TOP_K_RETRIEVAL` | Number of relevant chunks to retrieve |
| `BACKEND_API_URL` | Backend URL used by Streamlit |

## Tests and verification

Install dependencies first, then run:

```powershell
python -m pytest -q
```

The repository includes unit/integration tests under `backend/tests/` and a recorded functional run in `automation_test_report.json`. See `PROJECT_REPORT.pdf` and `PROJECT_REPORT.md` for the detailed scope, recorded results, limitations, and the independent verification attempt performed while preparing this package.

## Important notes

- The recorded automation report contains 16 checks: 14 marked `WORKING` and 2 marked `DEGRADED`. The degraded checks are DuckDuckGo web search (zero results/rate limiting during that run) and the xAI Grok cloud call (HTTP 403/quota or balance issue). This does not mean every environment will reproduce those results.
- A separate pytest attempt during package preparation could not collect tests because the current execution environment did not have `duckduckgo_search` installed. Python source compilation succeeded. This is reported transparently; it is not presented as a passing test run.
- Image enlargement/sharpening uses conventional image-processing operations. It can improve apparent clarity, but it is not equivalent to a learned super-resolution model and cannot guarantee recovery of real missing details.
- Emotional-support features are not medical care, diagnosis, or crisis counselling. For urgent help in India, contact local emergency services (112) or Tele-MANAS (14416).
- Treat uploaded documents and API keys as private. Configure access controls and production-grade secrets management before deployment.
