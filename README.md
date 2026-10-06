# Groww Mutual Fund FAQ Chatbot (RAG Prototype)

See `docs/PRD_RAG.md` and `docs/architecture.md`.

## Folder structure
- `docs/` – PRD, architecture, problem statement
- `data/raw/` – fetched page text/HTML
- `data/chunks/` – chunked text output
- `data/embeddings/` – saved embedding vectors
- `data/vectordb/` – FAISS/Chroma index
- `code/` – pipeline code (load, chunk, embed, store, retrieve, test)
- `tests/` – retrieval evaluation scripts
