# LLM Wiki

A git-backed personal knowledge base on the Karpathy LLM-Wiki pattern.
Not RAG: an agent compiles sources into interlinked markdown and keeps it current.

- `raw/`  — immutable sources you drop in
- `wiki/` — agent-owned, interlinked markdown
- `CLAUDE.md` — the operating contract the agent follows

Open this folder as an Obsidian vault for the graph view. Run an agent
(Claude Code / Codex) from this directory to ingest, query, and lint.
