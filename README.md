---
title: Knowledge Universe API
emoji: 🌌
colorFrom: purple
colorTo: green
sdk: docker
pinned: false
---

# Knowledge Universe API

> **The Knowledge Layer for AI That Stays Current.**  
> One API. 16 crawlers. 62 format types. Every result scored for freshness, decay, velocity, and conflict.  
> Drop into any RAG pipeline. Your keys. Our intelligence.

[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.109.0-009688.svg)](https://fastapi.tiangolo.com/)
[![Redis](https://img.shields.io/badge/Redis-Cache-DC382D.svg)](https://redis.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Live API](https://img.shields.io/badge/Live%20API-HuggingFace-FF9D00.svg)](https://vlsiddarth-knowledge-universe.hf.space)
[![22 Tests Passing](https://img.shields.io/badge/tests-22%20PASS%200%20FAIL-brightgreen.svg)](#testing)

## ⚡️ Enterprise API in Action

**1. Sequence-Drift Prevention (Velocity & Conflict Detection)**
Instead of just returning links, Knowledge Universe cross-encodes payloads to catch contradictions before they poison your LLM context window.

```json
{
  "knowledge_velocity": {
    "velocity_label": "hypersonic",
    "median_age_days": 14,
    "recommended_refresh_days": 7,
    "warning": "This domain is evolving rapidly. Cache for max 1 week."
  },
  "conflict_detection": {
    "conflicts_found": 1,
    "conflict_pairs": [
      {
        "source_a": "github:langchain-ai/langchain",
        "source_b": "stackoverflow:79123456",
        "conflict_type": "version_discrepancy"
      }
    ]
  }
}
```
**2. Deterministic Temporal Decay**

Every vector payload is scored using mathematical decay algorithms tuned to its specific source platform.

## The Problem

Your RAG pipeline answered with confidence. The source was 18 months old. Nothing broke. Nothing warned you.

```json
"decay_scores": {
  "arxiv:2401.01234": {
    "decay_score": 0.12,
    "label": "fresh",
    "age_days": 45
  },
  "github:old-repo/v1": {
    "decay_score": 0.88,
    "label": "decayed",
    "age_days": 412
  }
}
```
```
LLM + vector store:    "The answer is X"  (source: Stack Overflow, 2021)
Knowledge Universe:    decay_score=0.72, label="stale"  ← caught before it reaches your LLM
```

Every retrieval API today — Tavily, Exa, SerpAPI — returns results confidently. None of them tell you:
- When a source is **stale**
- When two sources **contradict each other**
- When a topic is **evolving so fast** your cache is already wrong
- When an academic paper has been **retracted**
- How a paper's **intellectual lineage** connects to newer work

Knowledge Universe is the first retrieval API built around **time-awareness** and **knowledge integrity**.

---

## What This Does

### 🔴 Knowledge Decay Score
Every result carries a decay score from `0.0` (fresh) to `1.0` (fully decayed), computed from publication date, source type, and half-life constants tuned per platform:

```
decay = 1 - 0.5 ^ (age_days / half_life)
```

```
0.00 – 0.25  →  fresh    ✅  safe to use
0.25 – 0.50  →  aging    ⚠️  use with awareness
0.50 – 0.75  →  stale    🔶  verify before using
0.75 – 1.00  →  decayed  ❌  find a newer source
```

### 📡 Knowledge Velocity Score *(Industry-first)*
Not just how old a source is — but how **fast the topic itself is evolving**. If 78% of LangChain results are from the last 30 days, that's a signal the domain is moving at hypersonic speed.

```json
"knowledge_velocity": {
  "velocity_label": "hypersonic",
  "median_age_days": 14,
  "pct_from_last_90_days": 0.78,
  "recommended_refresh_days": 7,
  "warning": "This domain is evolving extremely rapidly. Re-query weekly."
}
```

### ⚡ Source Conflict Detection *(Industry-first)*
When two sources in the same response contradict each other — one says "use method A", another says "deprecated, use method B" — KU flags it automatically.

```json
"conflict_detection": {
  "conflicts_found": 1,
  "conflict_pairs": [{
    "source_a": "stackoverflow:79123456",
    "source_b": "github:langchain-ai/langchain",
    "conflict_type": "version_discrepancy",
    "description": "SO answer references v0.1 API; GitHub README shows v0.3 API",
    "confidence": 0.82
  }]
}
```

### 🎯 Coverage Confidence Score *(Industry-first)*
The only retrieval API that tells you when it didn't find what you were looking for:

```json
"coverage_intelligence": {
  "confidence": 0.71,
  "confidence_label": "high",
  "coverage_warning": false,
  "suggested_queries": [],
  "difficulty_coherence": 0.85
}
```

### 🔬 Retraction Status *(Medical/Legal AI compliance)*
Every academic result from arXiv, CrossRef, and SemanticScholar is checked against the Retraction Watch database. Retracted papers are zeroed out before they reach your LLM.

```json
"retraction_status": {
  "retracted": false,
  "checked_at": "2026-04-24T...",
  "check_source": "retraction_watch"
}
```

### 🕸️ Citation Graph & Intellectual Lineage *(Research-grade)*
Every academic result includes its citation network — papers it cites, how many times it's been cited, and the most recent papers that build on it.

```json
"related_sources": {
  "cites": ["crossref:10.48550/arXiv.1706.03762"],
  "cited_by_count": 47,
  "successor_papers": ["crossref:10.xxx"]
}
```

---

## Live API

| | |
|---|---|
| **Base URL** | `https://vlsiddarth-knowledge-universe.hf.space` |
| **Health** | `https://vlsiddarth-knowledge-universe.hf.space/health` |
| **Swagger UI** | `https://vlsiddarth-knowledge-universe.hf.space/api-docs` |
| **Sign up** | `POST /v1/signup?email=you@email.com` |

---

## Quick Start

### 1. Get a free API key (no credit card)

```bash
curl -X POST "https://vlsiddarth-knowledge-universe.hf.space/v1/signup?email=you@email.com"
```

```json
{
  "status": "created",
  "api_key": "ku_test_abc123...",
  "tier": "free",
  "calls_limit": 500,
  "message": "Save your API key — it won't be shown again."
}
```

### 2. Discover sources with decay + velocity + conflict scores

```bash
curl -X POST https://vlsiddarth-knowledge-universe.hf.space/v1/discover \
  -H "X-API-Key: ku_test_abc123..." \
  -H "Content-Type: application/json" \
  -d '{
    "topic": "transformer architecture",
    "difficulty": 3,
    "formats": ["pdf", "github", "html"],
    "max_results": 5
  }'
```

### 3. Python — full response with all intelligence signals

```python
import requests

resp = requests.post(
    "https://vlsiddarth-knowledge-universe.hf.space/v1/discover",
    headers={"X-API-Key": "ku_test_abc123..."},
    json={
        "topic": "RAG retrieval augmented generation",
        "difficulty": 3,
        "formats": ["pdf", "github", "html"]
    }
).json()

# Decay scores on every source
for sid, decay in resp["decay_scores"].items():
    print(f"{decay['label']:8} score={decay['decay_score']:.2f}  {sid}")

# Coverage confidence
cov = resp["coverage_intelligence"]
if cov["coverage_warning"]:
    print("Low confidence. Try:", cov["suggested_queries"])

# Knowledge velocity — how fast is this domain evolving?
vel = resp["knowledge_velocity"]
print(f"Domain velocity: {vel['velocity_label']} — refresh in {vel['recommended_refresh_days']} days")

# Conflict detection
conflicts = resp["conflict_detection"]
if conflicts["conflicts_found"] > 0:
    for c in conflicts["conflict_pairs"]:
        print(f"CONFLICT: {c['conflict_type']} — {c['description']}")
```

### 4. LangChain integration

```python
from knowledge_universe import KUClient  # pip install knowledge-universe

ku = KUClient(api_key="ku_test_...")
results = ku.discover("transformer architecture", difficulty=3)

# Direct LangChain injection
docs = results.to_langchain()  # List[Document] with decay_score in metadata

# Filter stale sources before they reach your LLM
safe_docs = [d for d in docs if d.metadata["decay_score"] < 0.5]
```

---

## Complete Response Schema

```json
{
  "query": "transformer architecture",
  "total_found": 8,
  "cache_hit": false,
  "processing_time_ms": 3467.4,
  "credits_used": 1,
  "credits_remaining": 499,

  "decay_scores": {
    "arxiv:1706.03762": {
      "decay_score": 0.847,
      "freshness": 0.153,
      "label": "stale",
      "age_days": 2736,
      "penalty_multiplier": 0.268,
      "decay_velocity": 0.000633,
      "days_until_stale": 0
    }
  },

  "knowledge_velocity": {
    "velocity_label": "fast",
    "velocity_score": 0.72,
    "median_age_days": 45,
    "pct_from_last_90_days": 0.44,
    "recommended_refresh_days": 21,
    "warning": "Active development. Re-query every 2-3 weeks."
  },

  "conflict_detection": {
    "conflicts_found": 0,
    "conflict_pairs": [],
    "checked_sources": 8,
    "warning": null
  },

  "coverage_intelligence": {
    "confidence": 0.71,
    "confidence_label": "high",
    "coverage_warning": false,
    "warning_message": null,
    "suggested_queries": [],
    "top_result_similarities": [
      { "title": "Attention Is All You Need", "similarity": 0.823 }
    ],
    "difficulty_coherence": 0.85
  },

  "sources": [
    {
      "id": "arxiv:1706.03762",
      "title": "Attention Is All You Need",
      "url": "https://arxiv.org/abs/1706.03762",
      "source_platform": "arxiv",
      "quality_score": 8.47,
      "difficulty": 4,
      "decay_report": { "label": "stale", "decay_score": 0.847 },
      "retraction_status": { "retracted": false, "check_source": "retraction_watch" },
      "related_sources": {
        "cites": ["crossref:10.xxx"],
        "cited_by_count": 89234,
        "successor_papers": ["crossref:10.yyy"]
      }
    }
  ]
}
```

---

## Performance

| Metric | Value | vs. Competition |
|--------|-------|-----------------|
| Cold query latency | **~3.5s** | Faster than Tavily (5.3s) |
| Cache hit latency | **~220ms** | Excellent round-trip |
| Sources crawled | **16 crawlers** | arXiv, GitHub, YouTube, SO, Kaggle, CrossRef... |
| Format types supported | **62** | Most comprehensive in category |
| Test suite | **22 PASS / 0 FAIL** | Production verified |
| Decay scoring | **✅** | Tavily ✗ · Exa ✗ · SerpAPI ✗ |
| Knowledge velocity | **✅** | Industry-first |
| Conflict detection | **✅** | Industry-first |
| Retraction checking | **✅** | Industry-first |
| Citation graph | **✅** | Industry-first |
| Confidence score | **✅** | Industry-unique |
| Difficulty-aware ranking | **✅** | Industry-unique |

---

## API Reference

### All Endpoints

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `POST` | `/v1/signup` | No | Get free API key |
| `GET` | `/v1/usage` | Yes | Monthly quota and reset |
| `POST` | `/v1/discover` | Yes | Multi-platform discovery + all intelligence signals |
| `POST` | `/v1/knowledge` | Yes | Enterprise KnowledgeObjects + embeddings |
| `POST` | `/v1/context-optimizer` | Yes | Rank your existing docs for LLM context packing |
| `POST` | `/v1/knowledge-audit` | Yes | Health report for your knowledge base |
| `POST` | `/v1/batch-discover` | Yes | Up to 10 topics in parallel |
| `POST` | `/v1/batch-discover-stream` | Yes | Streaming batch via Server-Sent Events |
| `POST` | `/v1/path` | Yes | Learning path from beginner → expert |
| `POST` | `/v1/diff` | Yes | New vs established sources (field velocity) |
| `GET` | `/v1/coverage` | Yes | Topic coverage health report |
| `POST` | `/v1/compare` | Yes | Two topics head-to-head |
| `GET` | `/v1/decay-table` | Yes | Decay projection schedule (+30d, +90d, +365d) |
| `POST` | `/v1/freshness-alert` | Yes | Register webhook for decay threshold alerts |
| `POST` | `/v1/half-life-overrides` | Yes | Custom decay half-lives per platform (Growth+) |
| `GET` | `/v1/trace` | Yes | Trace citation lineage of any DOI |
| `GET` | `/v1/formats` | No | All 62 supported format types |
| `GET` | `/v1/crawlers` | No | Active crawlers and status |
| `GET` | `/v1/cache/stats` | Yes | Redis hit rate and memory |
| `GET` | `/v1/key-rotation-status` | Yes | API key rotation health |
| `GET` | `/health` | No | Liveness check + Redis status |
| `GET` | `/api-docs` | No | Interactive Swagger UI |

### `/v1/discover` Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `topic` | string | ✅ | Search query (2–200 chars) |
| `difficulty` | int 1–5 | ✅ | 1 = beginner, 5 = research-level |
| `formats` | string[] | — | Default: `["pdf","video","github","jupyter"]` |
| `max_results` | int 1–50 | — | Default: `10` |
| `output` | string | — | `json` \| `embeddings` |
| `min_freshness` | float 0–1 | — | Filter sources below this freshness score |

### Pricing

| Plan | Price | Calls/month | Key Features |
|------|-------|-------------|--------------|
| Free | $0 | 500 | All intelligence signals, 16 crawlers |
| Starter | $29/mo | 5,000 | Priority response, YouTube + Kaggle |
| Growth | $79/mo | 20,000 | Half-life customization, batch endpoints |
| Pro | $199/mo | 75,000 | SLA guarantee, dedicated support |
| Enterprise | Custom | Unlimited | On-prem, custom crawlers, audit reports |

---

## Knowledge Decay Engine

Half-lives tuned per platform based on how fast knowledge becomes outdated:

| Platform | Half-life | Why |
|----------|-----------|-----|
| HuggingFace | 120 days | ML moves fastest — models superseded constantly |
| GitHub | 180 days | Code goes stale as dependencies update |
| YouTube | 270 days | Tutorials date quickly with library releases |
| Stack Overflow | 365 days | Answers age with framework versions |
| Kaggle | 365 days | Competition context becomes irrelevant |
| arXiv | 3 years | Research papers have long shelf life |
| MIT OCW | 3 years | Academic courses revised on cycles |
| Wikipedia | 4 years | Actively maintained, slow to decay |
| Open Library | 5 years | Books revised infrequently |

> **Enterprise:** Growth tier customers can override half-lives per platform via `/v1/half-life-overrides`. A fintech team knows their GitHub dependencies break every 30 days.

---

## Sources — 16 Active Crawlers

| Source | API Key | Formats | Notes |
|--------|---------|---------|-------|
| arXiv | None | pdf, latex, html | + Retraction Watch check |
| Wikipedia | None | html, knowledge_graph | |
| Stack Overflow | None | html | |
| HuggingFace Hub | None | dataset, jupyter | |
| Open Library | None | epub, pdf, html | |
| MIT OCW | None | html, pdf, video, problem_set | |
| Podcast Index | None | podcast, audio, transcript | |
| PapersWithCode | None | pdf, html | HuggingFace Papers API |
| CrossRef | None | pdf, html | 150M+ academic works |
| Documentation | None | html | LangChain, FastAPI, PyTorch, 30+ frameworks |
| Distill + Observable | None | html, simulation | |
| GitHub REST | Optional | github, jupyter, markdown | |
| YouTube Data v3 | Optional | video, transcript | |
| Kaggle | Optional | kaggle, jupyter, dataset | |
| Universal Media | Optional | 3d_model, audio, simulation | Sketchfab, Freesound, Wolfram |

All 62 supported formats: `GET /v1/formats`

---

## Architecture

```
YOUR APP
   │
   ▼  POST /v1/discover  (X-API-Key header)
┌──────────────────────────────────────────────────────────────┐
│                   KNOWLEDGE UNIVERSE API                      │
│                                                              │
│  Auth + Quota Check (Redis)                                  │
│    ↓                                                         │
│  Cache Check (Redis) ──── HIT ──────────→ ~220ms return     │
│    ↓ MISS                                                    │
│  Parallel Crawl (16 crawlers, per-crawler timeouts)          │
│    ├── arXiv           (25s)  No key needed                  │
│    ├── Wikipedia        (5s)  No key needed                  │
│    ├── StackOverflow    (6s)  No key needed                  │
│    ├── HuggingFace      (8s)  No key needed                  │
│    ├── OpenLibrary      (5s)  No key needed                  │
│    ├── MIT OCW          (5s)  No key needed                  │
│    ├── PapersWithCode   (6s)  No key needed                  │
│    ├── CrossRef         (6s)  No key needed                  │
│    ├── Documentation    (3s)  No key needed                  │
│    ├── Distill+Observ   (5s)  No key needed                  │
│    ├── GitHub REST      (8s)  Optional token                 │
│    ├── YouTube          (8s)  Optional key                   │
│    ├── Kaggle           (6s)  Optional credentials           │
│    ├── UniversalMedia   (5s)  Optional keys                  │
│    ├── CommonCrawl      (2s)  Fast-fail                      │
│    └── GHArchive        (2s)  Fast-fail                      │
│    ↓                                                         │
│  Normalize → Semantic Pre-filter → Quality Score             │
│    ↓                                                         │
│  MinHash LSH Deduplication                                   │
│    ↓                                                         │
│  Knowledge Decay Engine  ←── Core IP                         │
│    ↓                                                         │
│  Retraction Watch Check  (academic sources)                  │
│    ↓                                                         │
│  CrossRef Citation Graph (academic sources)                  │
│    ↓                                                         │
│  Platform-aware LLM Reranker (all-MiniLM-L6-v2)              │
│    ↓                                                         │
│  Coverage Confidence + Difficulty Coherence Score            │
│    ↓                                                         │
│  Knowledge Velocity Score                                    │
│    ↓                                                         │
│  Source Conflict Detection                                   │
│    ↓                                                         │
│  Cache result (Redis, 4h TTL)                                │
│    ↓                                                         │
│  Return: Sources + Decay + Velocity + Conflicts + Citations  │
└──────────────────────────────────────────────────────────────┘
```

---

## Competitive Comparison

| Feature | Knowledge Universe | Tavily | Exa | SerpAPI | Perplexity API |
|---------|:-----------------:|:------:|:---:|:-------:|:--------------:|
| Cold latency | **3.5s** | 5.3s | 1.5s | 3.5s | 2s |
| Knowledge decay score | ✅ | ✗ | ✗ | ✗ | ✗ |
| Knowledge velocity | ✅ | ✗ | ✗ | ✗ | ✗ |
| Conflict detection | ✅ | ✗ | ✗ | ✗ | ✗ |
| Retraction checking | ✅ | ✗ | ✗ | ✗ | ✗ |
| Citation graph | ✅ | ✗ | ✗ | ✗ | ✗ |
| Coverage confidence | ✅ | ✗ | ✗ | ✗ | ✗ |
| Difficulty-aware ranking | ✅ | ✗ | ✗ | ✗ | ✗ |
| Publication dates | ✅ | ✗ | ✅ | ✅ | ✗ |
| Embeddings output | ✅ | ✗ | ✗ | ✗ | ✗ |
| Format filtering (62 types) | ✅ | ✗ | ✗ | ✗ | ✗ |
| Batch parallel queries | ✅ | ✗ | ✗ | ✗ | ✗ |
| Streaming SSE | ✅ | ✗ | ✗ | ✗ | ✗ |
| Learning path endpoint | ✅ | ✗ | ✗ | ✗ | ✗ |
| Context optimizer | ✅ | ✗ | ✗ | ✗ | ✗ |
| Knowledge audit | ✅ | ✗ | ✗ | ✗ | ✗ |
| LangChain/LlamaIndex ready | ✅ | ✅ | ✗ | ✗ | ✗ |
| No API key needed (bulk) | ✅ | ✗ | ✗ | ✗ | ✗ |

---

## Testing

Production-verified test suite. Run it yourself:

```powershell
$API_KEY = "your_key_here"
$BASE = "https://vlsiddarth-knowledge-universe.hf.space"
.\run_all_tests_v3.ps1
```

```
KNOWLEDGE UNIVERSE -- FULL TEST SUITE v3
==========================================
  PASS  Health check
  PASS  Formats endpoint (62 formats)
  PASS  Crawlers endpoint (16 crawlers)
  PASS  Usage endpoint
  PASS  Discover -- basic (Live Crawl)
  PASS  Discover -- decay scores on every source
  PASS  Discover -- coverage intelligence present
  PASS  Cache hit -- second call <250ms
  PASS  Learning path -- slim response
  PASS  Diff -- new vs established
  PASS  Coverage -- topic health
  PASS  Compare -- two topics
  PASS  Decay table -- projections
  PASS  Freshness alert -- register
  PASS  F1 Context optimizer -- semantic ranking
  PASS  F2 Knowledge audit -- arXiv date extraction
  PASS  F3 YouTube social proof
  PASS  F4 Domain-aware suggestions
  PASS  F6 Batch discover -- parallel
  PASS  F7 Half-life override -- 403 free tier
  PASS  F8 Streaming batch SSE
  PASS  F9 Citation trace
==========================================
RESULTS: 22 PASS  0 FAIL
```

---

## Run Locally (5 Minutes)

```bash
git clone https://github.com/VLSiddarth/Knowledge-Universe.git
cd Knowledge-Universe
python3 -m venv venv && source venv/bin/activate
pip install -r requirements.txt

# Start Redis
docker run -d --name ku-redis -p 6379:6379 redis:7-alpine

# Configure
cp .env.example .env
# Edit .env: set DEMO_MODE=false, add optional API keys

# Start
uvicorn src.api.main:app --reload --host 0.0.0.0 --port 8000
```

Visit `http://localhost:8000/health` → `{"status":"healthy","redis":"connected"}`

### Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `REDIS_HOST` | ✅ | Redis hostname |
| `REDIS_PORT` | ✅ | Redis port (default: 6379) |
| `REDIS_PASSWORD` | ✅ | Redis password |
| `SECRET_KEY` | ✅ | 64-char random string |
| `DEMO_MODE` | ✅ | Set `false` for live crawling |
| `GITHUB_TOKEN` | Recommended | Unlocks GitHub crawler |
| `YOUTUBE_API_KEY` | Recommended | Unlocks YouTube crawler |
| `KAGGLE_USERNAME` | Optional | Unlocks Kaggle crawler |
| `KAGGLE_KEY` | Optional | Unlocks Kaggle crawler |
| `SKETCHFAB_API_KEY` | Optional | Unlocks 3D model search |
| `FREESOUND_API_KEY` | Optional | Unlocks audio search |

Generate SECRET_KEY:
```bash
python -c "import secrets; print(secrets.token_hex(32))"
```

---

## Deploy to Production

**Short version:**
1. Fork this repo
2. Create HuggingFace Space → Docker → connect repo
3. Create free Redis at [upstash.com](https://upstash.com)
4. Add secrets in Space Settings
5. Push to main — auto-deploys

See [DEPLOYMENT.md](DEPLOYMENT.md) for full guide.

---

## Troubleshooting

**Redis not connecting:**
```bash
redis-cli ping  # Should return PONG
```

**First request slow (30–60s):**
Normal on cold start — `all-MiniLM-L6-v2` embedding model initializes once. After that, cache hits return in ~220ms.

**0 results returned:**
Never use `formats=["video"]` alone or `min_freshness > 0.7`. Use:
```json
{ "formats": ["pdf", "html", "github"], "max_results": 5 }
```

**YouTube/GitHub/Kaggle returning 0:**
Add API keys to HuggingFace Space secrets or your `.env` file.

**422 Validation error:**
Run `GET /v1/formats` to see all 62 valid format values.

---

## Contributing

Pull requests welcome. See [CONTRIBUTING.md](.github/CONTRIBUTING.md).

By contributing, you agree to grant Knowledge Universe a non-exclusive, irrevocable, royalty-free license to use your contributions.

---

## License

MIT License — see [LICENSE](LICENSE).

---

**Built with open source. No scrapers. No copyrighted content. Your keys, our intelligence.**

*Knowledge Universe — because your RAG pipeline deserves to know how old, how fast, and how conflicted its sources are.*
