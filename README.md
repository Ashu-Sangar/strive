# Stride

AI-powered ML learning path generator. Takes your background and goal as input, builds a personalized week-by-week curriculum with curated resources via RAG, and adapts the plan based on your progress.

---

## The Problem

Most people trying to learn ML don't know where to start. They get overwhelmed by the number of resources, waste time on the wrong ones, and have no structure to follow. Stride solves this by generating a personalized, adaptive learning plan backed by a curated library of vetted resources.

## How It Works

1. **Intake** — Answer a short form: your background, math comfort level, goal, and hours per week available
2. **Path generation** — AI builds a phased, week-by-week curriculum with real, specific resources for each topic
3. **Progress tracking** — Mark resources complete and rate your confidence (got it / shaky / lost)
4. **Adaptation** — The plan updates based on your progress, inserting remediation resources where needed and compressing phases you're flying through

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js + TypeScript |
| Backend | FastAPI (Python) |
| LLM | Anthropic API (claude-sonnet-4-20250514) |
| Embeddings | OpenAI text-embedding-3-small |
| Database + Vector Store | Supabase + pgvector |
| Auth | Supabase Auth |
| Frontend Deployment | Vercel |
| Backend Deployment | Railway |

## Project Structure

```
stride/
├── frontend/          # Next.js app
│   ├── app/
│   │   ├── page.tsx           # Landing page
│   │   ├── intake/            # Intake form flow
│   │   ├── dashboard/         # Learning path dashboard
│   │   └── api/               # Next.js API routes
│   ├── components/
│   └── lib/
│
├── backend/           # FastAPI app
│   ├── main.py
│   ├── routes/
│   │   ├── generate.py        # Path generation endpoint
│   │   └── adapt.py           # Adaptation/re-generation endpoint
│   ├── services/
│   │   ├── agent.py           # Anthropic API logic
│   │   └── retrieval.py       # pgvector RAG retrieval
│   └── scripts/
│       └── ingest.py          # Resource dataset ingestion script
│
└── data/
    └── resources.json         # Curated ML resource dataset
```

## Development Plan

### Phase 1 — Setup & Scaffolding
- Init Next.js project (App Router + TypeScript)
- Init FastAPI backend
- Configure Supabase project, enable pgvector extension
- Set up environment variables
- Deploy frontend to Vercel, backend to Railway

### Phase 2 — Resource Dataset & RAG Pipeline
- Curate 150–300 ML resources with metadata (title, url, type, topics, difficulty, estimated_hours)
- Write `ingest.py` to embed each resource via OpenAI and upsert into Supabase
- Build retrieval function in FastAPI — embed a topic query, run cosine similarity against pgvector, return top-k resources

### Phase 3 — Intake Form & Path Generation
- Build multi-step intake form in Next.js
- FastAPI `/generate` endpoint: takes intake answers → calls Anthropic API to structure curriculum → retrieves resources from pgvector per topic → returns streamed plan
- Save generated plan to Supabase under user account

### Phase 4 — Dashboard & Progress Tracking
- Build roadmap dashboard: phases, weeks, resource cards with checkboxes
- Confidence rating per resource (got it / shaky / lost)
- Track completion state in Supabase `user_progress` table
- Per-phase and overall progress indicators

### Phase 5 — Adaptive Re-generation
- On "shaky/lost" rating: retrieve and insert remediation resources for that topic
- On phase completion ahead of schedule: compress next phase via re-generation
- Pass full user progress state on every agent call
- "Regenerate plan" button for goal/time changes

## Getting Started

### Prerequisites
- Node.js 18+
- Python 3.11+
- Supabase account
- Anthropic API key
- OpenAI API key (embeddings only)

### Environment Variables

**Frontend (`frontend/.env.local`)**
```
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
NEXT_PUBLIC_API_URL=
```

**Backend (`backend/.env`)**
```
ANTHROPIC_API_KEY=
OPENAI_API_KEY=
SUPABASE_URL=
SUPABASE_SERVICE_KEY=
```

### Running Locally

```bash
# Frontend
cd frontend
npm install
npm run dev

# Backend
cd backend
pip install -r requirements.txt
uvicorn main:app --reload
```

## Roadmap

- [ ] Phase 1 — Setup & scaffolding
- [ ] Phase 2 — Resource dataset & RAG pipeline
- [ ] Phase 3 — Intake form & path generation
- [ ] Phase 4 — Dashboard & progress tracking
- [ ] Phase 5 — Adaptive re-generation
- [ ] v2 — Add quiz/assessment layer per topic
- [ ] v2 — Expand beyond ML to additional skill tracks
- [ ] v2 — Retrieval experimentation (compare strategies, measure outcomes)
