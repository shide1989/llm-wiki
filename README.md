# LLM Wiki

A git-backed knowledge template,based on the Karpathy LLM-Wiki pattern.
Not a RAG: an agent ingests sources into interlinked markdown files and keeps it current.

- `raw/` — immutable sources you drop in
- `wiki/` — agent-owned, interlinked markdown
- `CLAUDE.md` — the operating contract the agent follows

Open this folder as an Obsidian vault for the graph view. Run an agent
(Claude Code / Codex) from this directory to ingest, query, and lint.

Use this repository as a template for your knowledge vaults !
