# Architecture — Groww Mutual Fund FAQ Chatbot (RAG Prototype)

**Role:** Snr Architect | **Scope:** Restricted to PRD corpus (5 HDFC fund pages on Groww.in only)

## High-Level Flow
```
[5 Groww Fund URLs]
      ↓ (Phase 1: Data Loading)
Clean text corpus
      ↓ (Phase 2: Chunking)
Text chunks
      ↓ (Phase 3: Embedding)
Chunk vectors
      ↓ (Phase 4: Vector Store)
FAISS/Chroma index
      ↓ (Phase 5: Retrieval Logic)  ← Query guardrails (refuse advice/PII/performance)
Top-k chunks
      ↓
LLM → answer ≤3 sentences + one citation link + Last updated
      ↓
(Phase 6: Retrieval Testing) — offline eval set → iterate
```

---

## Phase 1 — Data Loading
- Input: the 5 sandboxed Groww URLs (PRD §3).
- Fetch with requests/httpx + parse with BeautifulSoup (or Crawl4AI).
- Extract: fund name, category, expense ratio, ELSS lock-in, AUM, fund manager, benchmark, last-updated date.
- Normalize whitespace; store raw + cleaned text per URL with metadata (url, source, fetched_at).

## Phase 2 — Chunking
- Strategy: recursive character splitter, ~300–500 tokens/chunk, 50-token overlap.
- Keep section headings in chunk metadata (e.g., "expense_ratio", "lock_in") for better retrieval.
- One chunk never mixes two funds → preserves citation accuracy.

## Phase 3 — Embedding
- Model: OpenAI text-embedding-3-small (or local sentence-transformers all-MiniLM-L6-v2).
- Batch embed all chunks; persist vectors with metadata.

## Phase 4 — Vector Store
- Local prototype: FAISS (flat index) or ChromaDB with metadata filters.
- Store per-chunk: vector, text, source URL, section, fetched date.
- Rebuild index on re-ingestion (full refresh; no incremental updates needed at this scale).

## Phase 5 — Retrieval Logic
- Query understanding: classify intent first — `factual` vs `opinionated/portfolio/performance` vs `contains_PII`.
  - Non-factual → refusal template + official factsheet link.
  - PII → reject and do not log.
- Factual → embed query → top-k (k=3–5) cosine retrieval → optional metadata filter → LLM prompt:
  "Answer in ≤3 sentences using only these chunks. End with one citation URL and 'Last updated: <date>'."
- Fallback: if no chunk passes similarity threshold, say "I don't have that fact in the sources."

## Phase 6 — Retrieval Testing
- Golden set: ~20 Q&A pairs (expense ratio, lock-in, manager, category, benchmark per fund).
- Metrics: hit@k (is the correct chunk retrieved), citation accuracy, refusal accuracy on negative queries.
- Manual spot-check groundedness; iterate on chunk size / k / threshold.

## Guardrails Summary (from PRD)
- Public sources only, no PII, no performance claims/comparisons, citations mandatory.
