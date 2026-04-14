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
> One API. 13+ official sources. Every result scored for freshness and decay.  
> Drop into any RAG pipeline. Your keys. Our intelligence.

[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.109.0-009688.svg)](https://fastapi.tiangolo.com/)
[![Redis](https://img.shields.io/badge/Redis-Cache-DC382D.svg)](https://redis.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Live API](https://img.shields.io/badge/Live%20API-HuggingFace-FF9D00.svg)](https://vlsiddarth-knowledge-universe.hf.space)

---

## 📺 See It In Action

**Cold Start vs. Cache Hit (3.5s → 14ms):**

<video src="https://github.com/user-attachments/assets/fdeb9439-fa18-4697-abd1-3fc6bb236e31" controls="controls" muted="muted" width="100%"></video>

**Full 90-Second Walkthrough — 13 Crawlers, Decay Scores, Coverage Confidence:**

<video src="https://github.com/user-attachments/assets/faf44193-95c9-4ba5-86d9-dee75bb49a3c" controls="controls" muted="muted" width="100%"></video>

`0:15` Deep parallel search across 13+ sources &nbsp;|&nbsp; `0:30` 14ms cache hits &nbsp;|&nbsp; `1:15` LLM reranking &nbsp;|&nbsp; `1:25` LangChain/LlamaIndex-ready output

---

## The Problem

Your RAG pipeline answered with confidence. The source was 18 months old. Nothing broke. Nothing warned you.

```
LLM + vector store:    "The answer is X"  (source: Stack Overflow, 2021)
Knowledge Universe:    decay_score=0.72, label="stale"  ← caught before it reaches your LLM
```

Every retrieval API today — Tavily, Exa, SerpAPI — returns results confidently. None of them tell you when those results are stale, mismatched to your query, or sourced from domains that age fast. Knowledge Universe is the first retrieval API built around **time-awareness**.

---

## What This Does

### Knowledge Decay Score
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

### Coverage Confidence Score
The only retrieval API that tells you when it didn't find what you were looking for:

```json
"coverage_intelligence": {
  "confidence": 0.71,
  "confidence_label": "high",
  "coverage_warning": false,
  "suggested_queries": []
}
```

When confidence is low, it suggests better search terms — automatically.

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

### 2. Discover sources with decay scores

```bash
curl -X POST https://vlsiddarth-knowledge-universe.hf.space/v1/discover \
  -H "X-API-Key: ku_test_abc123..." \
  -H "Content-Type: application/json" \
  -d '{
    "topic": "transformer architecture",
    "difficulty": 3,
    "formats": ["pdf", "github", "stackoverflow"],
    "max_results": 5
  }'
```

### 3. Python — read decay scores

```python
import requests

resp = requests.post(
    "https://vlsiddarth-knowledge-universe.hf.space/v1/discover",
    headers={"X-API-Key": "ku_test_abc123..."},
    json={
        "topic": "RAG retrieval augmented generation",
        "difficulty": 3,
        "formats": ["pdf", "github"]
    }
).json()

# Every result has a decay score
for sid, decay in resp["decay_scores"].items():
    print(f"{decay['label']:8} score={decay['decay_score']:.2f}  {sid}")

# Coverage confidence — did KU find good results?
cov = resp["coverage_intelligence"]
if cov["coverage_warning"]:
    print("Low confidence. Try:", cov["suggested_queries"])
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

## Response Schema

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
      "penalty_multiplier": 0.268
    }
  },

  "coverage_intelligence": {
    "confidence": 0.71,
    "confidence_label": "high",
    "coverage_warning": false,
    "warning_message": null,
    "suggested_queries": [],
    "top_result_similarities": [
      { "title": "Attention Is All You Need", "similarity": 0.823 }
    ]
  },

  "sources": [ ... ]
}
```

---

## Performance

| Metric | Value | vs. Competition |
|--------|-------|-----------------|
| Cold query latency | **~3.5s** | Faster than Tavily (5.3s) |
| Cache hit latency | **14ms** | Near SerpAPI (3.5s cold) |
| Sources crawled | **13 platforms** | arXiv, GitHub, YouTube, SO, Kaggle... |
| Gap analysis score | **2.52/3** | 5/5 test queries passing |
| Decay scoring | **✅ Yes** | Tavily ✗ · Exa ✗ · SerpAPI ✗ |
| Confidence score | **✅ Yes** | Industry-unique |
| Difficulty-aware ranking | **✅ Yes** | Industry-unique |

---

## Architecture

```
YOUR APP
   │
   ▼  POST /v1/discover  (X-API-Key header)
┌──────────────────────────────────────────────────────────┐
│                  KNOWLEDGE UNIVERSE API                   │
│                                                          │
│  Auth + Quota Check (Redis)                              │
│    ↓                                                     │
│  Cache Check (Redis) ──── HIT ──────────→ 14ms return   │
│    ↓ MISS                                                │
│  Parallel Crawl (13 crawlers, per-crawler timeouts)      │
│    ├── arXiv           (12s)  No key needed              │
│    ├── Wikipedia        (5s)  No key needed              │
│    ├── StackOverflow    (6s)  No key needed              │
│    ├── HuggingFace      (8s)  No key needed              │
│    ├── OpenLibrary      (5s)  No key needed              │
│    ├── MIT OCW          (5s)  No key needed              │
│    ├── Podcast Index    (5s)  No key needed              │
│    ├── GitHub REST      (8s)  Optional token             │
│    ├── YouTube          (8s)  Optional key               │
│    ├── Kaggle           (6s)  Optional credentials       │
│    ├── CommonCrawl      (2s)  Fast-fail                  │
│    ├── GHArchive        (2s)  Fast-fail                  │
│    └── LibGen           (4s)  Supplementary              │
│    ↓                                                     │
│  Normalize → Quality Score → Deduplicate (MinHash LSH)  │
│    ↓                                                     │
│  Knowledge Decay Engine  ←── Core IP                    │
│    ↓                                                     │
│  Platform-aware LLM Reranker (all-MiniLM-L6-v2)        │
│    ↓                                                     │
│  Coverage Confidence Score (shared embeddings, ~10ms)   │
│    ↓                                                     │
│  Cache result (Redis, 4h TTL)                           │
│    ↓                                                     │
│  Return: Sources + Decay Scores + Coverage Intelligence  │
└──────────────────────────────────────────────────────────┘
```

---

## API Reference

### Endpoints

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `POST` | `/v1/signup` | No | Get free API key |
| `GET` | `/v1/usage` | Yes | Monthly quota and reset |
| `POST` | `/v1/discover` | Yes | Multi-platform discovery + decay scores |
| `POST` | `/v1/knowledge` | Yes | Enterprise KnowledgeObjects + embeddings |
| `GET` | `/v1/formats` | No | All 54 supported source formats |
| `GET` | `/v1/crawlers` | No | Active crawlers and status |
| `GET` | `/v1/cache/stats` | Yes | Redis hit rate and memory |
| `GET` | `/health` | No | Liveness check + Redis status |
| `GET` | `/api-docs` | No | Interactive Swagger UI |

### `/v1/discover` Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `topic` | string | ✅ | Search query (2–200 chars) |
| `difficulty` | int 1–5 | ✅ | 1 = beginner, 5 = research-level |
| `formats` | string[] | — | Default: `["pdf","video","github","jupyter"]` |
| `max_results` | int 1–50 | — | Default: `10` |
| `output` | string | — | `json` \| `embeddings` (default: `json`) |
| `min_freshness` | float 0–1 | — | Filter sources below this freshness score |

### Pricing

| Plan | Price | Calls/month |
|------|-------|-------------|
| Free | $0 | 500 |
| Starter | $29/mo | 5,000 |
| Growth | $79/mo | 20,000 |
| Pro | $199/mo | 75,000 |
| Enterprise | Custom | Unlimited |

---

## Knowledge Decay Engine

Half-lives are tuned per platform based on how fast knowledge becomes outdated:

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

---

## Sources

| Source | API Key | Formats |
|--------|---------|---------|
| arXiv | None | pdf, latex, html |
| Wikipedia | None | html, knowledge_graph |
| Stack Overflow | None | html |
| HuggingFace Hub | None | dataset, jupyter |
| Open Library | None | epub, pdf, html |
| MIT OCW | None | html, pdf, video, problem_set |
| Podcast Index | None | podcast, audio, transcript |
| GitHub REST | Optional | github, jupyter, markdown |
| YouTube Data v3 | Optional | video, video_playlist, transcript |
| Kaggle | Optional | kaggle, jupyter, dataset |

All 54 supported formats: `GET /v1/formats`

---

## Competitive Comparison

| Feature | Knowledge Universe | Tavily | Exa | SerpAPI |
|---------|:-----------------:|:------:|:---:|:-------:|
| Cold latency | **3.5s** | 5.3s | 1.5s | 3.5s |
| Knowledge decay score | ✅ | ✗ | ✗ | ✗ |
| Coverage confidence | ✅ | ✗ | ✗ | ✗ |
| Difficulty-aware ranking | ✅ | ✗ | ✗ | ✗ |
| Publication dates | ✅ | ✗ | ✅ | ✅ |
| Embeddings output | ✅ | ✗ | ✗ | ✗ |
| Format filtering | ✅ | ✗ | ✗ | ✗ |
| LangChain/LlamaIndex ready | ✅ | ✅ | ✗ | ✗ |
| No API key needed (bulk) | ✅ | ✗ | ✗ | ✗ |

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

Then visit `http://localhost:8000/health` — should return `{"status":"healthy","redis":"connected"}`.

### Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `REDIS_HOST` | ✅ | Redis hostname (Upstash or local) |
| `REDIS_PORT` | ✅ | Redis port (default: 6379) |
| `REDIS_PASSWORD` | ✅ | Redis password |
| `SECRET_KEY` | ✅ | 64-char random string |
| `DEMO_MODE` | ✅ | Set to `false` for live crawling |
| `GITHUB_TOKEN` | Recommended | Unlocks GitHub crawler |
| `YOUTUBE_API_KEY` | Recommended | Unlocks YouTube crawler |
| `KAGGLE_USERNAME` | Optional | Unlocks Kaggle crawler |
| `KAGGLE_KEY` | Optional | Unlocks Kaggle crawler |

Generate SECRET_KEY:
```bash
python -c "import secrets; print(secrets.token_hex(32))"
```

---

## Deploy to Production

See [DEPLOYMENT.md](DEPLOYMENT.md) for the full guide (HuggingFace Spaces + Upstash Redis).

**Short version:**
1. Fork this repo
2. Create a HuggingFace Space → Docker runtime → connect repo
3. Create free Redis at [upstash.com](https://upstash.com)
4. Add environment variables in Space Settings → Secrets
5. Push to main — auto-deploys

---

## Troubleshooting

**Redis not connecting:**
```bash
redis-cli ping  # Should return PONG
```
Check `REDIS_HOST`, `REDIS_PORT`, and `REDIS_PASSWORD` are all set correctly.

**First request is slow (30–60s on cold start):**  
Normal. The `all-MiniLM-L6-v2` embedding model loads on first use. After that, cache hits return in ~14ms.

**GitHub / YouTube / Kaggle returning 0 results:**  
These crawlers return empty silently without credentials. Add keys to `.env`:
```bash
GITHUB_TOKEN=ghp_...
YOUTUBE_API_KEY=AIzaSy_...
KAGGLE_USERNAME=...
KAGGLE_KEY=...
```

**422 Validation error on `/v1/discover`:**  
Check `formats` values are valid. Run `GET /v1/formats` to see all 54 accepted values.

---

## Contributing

Pull requests welcome. See [CONTRIBUTING.md](.github/CONTRIBUTING.md) for guidelines.

By contributing, you agree to grant Knowledge Universe a non-exclusive, irrevocable, royalty-free license to use your contributions.

---

## License

MIT License — see [LICENSE](LICENSE).

---

**Built with open source. No scrapers. No copyrighted data. Your keys, our intelligence.**

*Knowledge Universe — because your RAG pipeline deserves to know how old its sources are.*
