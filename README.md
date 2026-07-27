# Michael Onojah — AI Engineering Portfolio

Self-taught AI/ML backend engineer. I build production-shaped AI systems, not just models, but the infrastructure around them: auth, rate limiting, async job queues, observability, and deployment.

**Live capstone:** https://ai-platform-capstone.onrender.com
**GitHub:** [github.com/HonourJAH](https://github.com/HonourJAH)

This portfolio highlights 3 projects that best represent how I think about building AI systems end to end. A full list of 12 projects covering classification, feature stores, model registries, streaming, and more — is available across my [GitHub repositories](https://github.com/HonourJAH?tab=repositories).

---

## 1. [Production AI Platform](https://github.com/HonourJAH/ai-platform-capstone) — Capstone

A unified, production-shaped inference gateway that combines text classification, image classification, RAG, and LLM chat behind a single authenticated, rate-limited, observable API.

**Live:** https://ai-platform-capstone.onrender.com

**What it demonstrates:**
- API-key auth (SHA-256 hashed, one-time-reveal), Redis token-bucket rate limiting via an atomic Lua script
- Sync vs. async serving architecture — cheap inference (text, RAG, chat) served inline; CPU-heavy image classification dispatched to a Celery job queue with polling
- Prometheus + Grafana observability, auto-provisioned via Docker Compose
- A cloud-vs-local backend toggle (Ollama/local Qdrant/local embeddings for full local dev; Groq/Qdrant Cloud/Cohere for a memory-lean free-tier deployment) — same codebase, two real environments
- CI that builds the actual Docker image and health-checks it against real Postgres and Redis, not just running unit tests in isolation

**The debugging is the actual story here.** The README documents every real bug hit and fixed along the way: a Celery worker that silently deadlocked forever the first time it touched a loaded PyTorch model (forking a process after PyTorch's threading locks were held), a race condition where a job could start processing before its own database row existed, an adapter that generated its own job ID instead of the one already committed — causing jobs to "succeed" in logs while the client polled a permanently stuck record and hitting Render's 512MB free-tier ceiling with two ML models loaded in-process, solved by moving embeddings and generation to cloud APIs rather than compromising the design.

**Stack:** FastAPI, Celery, Redis, PostgreSQL, Qdrant, Prometheus, Grafana, Docker, PyTorch, scikit-learn, Groq, Cohere

---

## 2. [AI Agent API](https://github.com/HonourJAH/ai-agent-api)

A tool-using AI agent exposed as an API. The agent can reason about a request, decide which tool to invoke, execute it, and incorporate the result into its response, all streamed back to the client in real time.

**What it demonstrates:**
- Real tool use: a calculator, a sandboxed code executor, and live web search (via Tavily), invoked by the LLM's own reasoning rather than hardcoded routing
- Server-Sent Events (SSE) streaming, so responses arrive token-by-token instead of waiting for the full generation
- Session memory backed by Redis, so multi-turn conversations retain context across requests
- Debugging real agent failure modes — e.g. diagnosing and fixing a missing `get_current_datetime` tool that was causing the model to hallucinate dates, and fixing a sandboxed code executor that failed on `datetime` calls because `time` wasn't in its import allowlist

**Stack:** FastAPI, Ollama, Redis, Tavily API, Server-Sent Events

---

## 3. [RAG API](https://github.com/HonourJAH/rag-api)

A Retrieval-Augmented Generation service: upload documents, have them chunked and embedded, and ask questions answered strictly from the retrieved context — with the model explicitly instructed to say when it doesn't know, rather than hallucinate.

**What it demonstrates:**
- The full RAG pipeline built from scratch: chunking, embedding (`sentence-transformers`), vector storage and similarity search (Qdrant), and grounded generation (Ollama)
- Document lifecycle management — list, filter-by-document search, and delete, not just a one-way ingest-and-forget pipeline
- Prompt design that explicitly constrains the model to the retrieved context, reducing hallucination by instruction rather than hoping the model behaves

**Stack:** FastAPI, Qdrant, sentence-transformers, Ollama, PyMuPDF

---

## About Me

B.Eng. Electrical & Electronics Engineering, Federal University of Technology Akure. Self-taught in AI/ML backend engineering — portfolio-driven rather than employment-history-driven, with a focus on the infrastructure layer around ML models: serving, observability, and reliability under real constraints.

Looking for AI Engineering / MLOps roles at companies integrating AI into production software, or building tooling to monitor and serve ML models reliably.

**Contact:** [LinkedIn](https://www.linkedin.com/in/friday-onojah-9a5698219/) · [GitHub](https://github.com/HonourJAH)
