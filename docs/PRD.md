# PRD — Groww Mutual Fund FAQ Assistant (RAG Prototype)

**Author:** Samarth Singh Baghel | **Status:** Draft v1 | **Scope:** Prototype / Hobby Project

---

## 1. Problem Statement
Investors browsing Groww mutual fund pages (AMC: HDFC) have factual questions — expense ratio, ELSS lock-in, fund category, launch date — but answers are scattered across page sections. A retrieval-based Q&A assistant can answer these instantly, with citations back to the official source, without giving investment advice.

## 2. Goal
Build a working prototype RAG chatbot that answers **factual questions only** about 5 HDFC fund pages on Groww.in, shows **one clear citation link** per answer, and **refuses opinionated / portfolio questions**. This is a learning/hobby prototype to test LLM + RAG feasibility, not a production feature.

## 3. Corpus & Scope (In / Out)

**In scope — only these 5 public pages:**
| # | Fund | Category | URL |
|---|---|---|---|
| 1 | HDFC Large Cap Fund Direct Growth | Large-cap | https://groww.in/mutual-funds/hdfc-large-cap-fund-direct-growth |
| 2 | HDFC Flexi Cap Fund Direct Growth | Flexi-cap (legacy URL: hdfc-equity-fund-direct-growth) | https://groww.in/mutual-funds/hdfc-equity-fund-direct-growth |
| 3 | HDFC ELSS Tax Saver Fund Direct Plan Growth | ELSS | https://groww.in/mutual-funds/hdfc-elss-tax-saver-fund-direct-plan-growth |
| 4 | HDFC Small Cap Fund Direct Growth | Small-cap | https://groww.in/mutual-funds/hdfc-smal1-cap-fund-direct-growth |
| 5 | HDFC Balanced Advantage Fund Direct Growth | Hybrid | https://groww.in/mutual-funds/hdfc-balanced-advantage-fund-direct-growth |

**Out of scope:** Screenshots of the app back-end, third-party blogs/reviews, any page beyond the 5 links above, personal financial data, performance/returns computation.

## 4. Users & Use Cases
- **Investor researching HDFC funds on Groww** — asks factual questions.
- Not for: "Should I invest?", "Best fund for me?", portfolio allocation, returns comparison.

### Example supported queries
- "What is the expense ratio of HDFC Large Cap Fund?"
- "What is the ELSS lock-in period?"
- "Who is the fund manager of HDFC Small Cap?"

### Example refused queries
- "Should I invest in this fund?" → refusal + pointer to official data
- "Compare returns of Large Cap vs Flexi Cap" → refused (no performance claims; link to official factsheet)

## 5. Functional Requirements
| ID | Requirement |
|---|---|
| F1 | Ingest & chunk the 5 URLs; build embeddings + vector index |
| F2 | Answer factual queries in ≤3 sentences with one citation link |
| F3 | Refuse opinionated / portfolio / performance-comparison queries with a standard message |
| F4 | Reject PII input (PAN, Aadhaar, account no., OTP, email) with a warning, don't store it |
| F5 | Show "Last updated: <source date>" in each answer |
| F6 | Tiny UI: welcome line, 3 example questions, disclaimer note "Facts-only. No investment advice." |

## 6. Non-Functional / Constraints
- Public sources only; no scraping of private data; no PII storage.
- No performance claims; no computed/compared returns — always link to the official factsheet.
- Answers short (≤3 sentences), cite one source link, include "Last updated."
- Prototype-grade latency (<5s) is acceptable.

## 7. Success Metrics (Prototype)
- Answers grounded in retrieved chunks (manual spot-check of 20 queries, target 90%+ correct)
- 100% of answers include exactly one citation link and last-updated stamp
- 100% of opinionated queries refused
- 0 PII logged/stored

## 8. Proposed Approach (Technical)
1. Scrape/parse the 5 pages → clean text (fund name, category, expense ratio, lock-in, AUM, manager, benchmark).
2. Chunk (~300–500 tokens), embed, store in a vector DB (e.g., FAISS / Chroma).
3. Query flow: embed question → retrieve top-k chunks → LLM answer with citation + last-updated; guardrail prompt to refuse advice/performance questions.
4. Simple UI (Streamlit or Gradio).

## 9. Open Questions
- Does the legacy Flexi Cap URL redirect correctly to current content? (note in spec)
- Refresh cadence for re-scraping source pages?
- Refusal copy & tone — finalize with stakeholders?

## 10. Milestones
| Wk | Deliverable |
|---|---|
| 1 | Corpus scrape + chunking + vector index |
| 2 | RAG pipeline + guardrails (refusal, citation, last-updated) |
| 3 | UI + evaluation on 20-query test set + README |
