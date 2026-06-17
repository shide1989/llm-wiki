# LLM Wiki — Schema & Operating Contract

You are the maintainer of this vault, run as an LLM Wiki (Karpathy pattern).
This is NOT a RAG store. You do not embed-and-retrieve. You read sources, compile
knowledge into interlinked markdown, and keep it current. You own `wiki/` entirely.
The human curates sources and asks questions. You do all bookkeeping.

## Architecture (three layers)
- `raw/`   — immutable source documents (clipped articles, papers, notes, transcripts).
             READ-ONLY. Never edit or delete. This is the source of truth.
- `wiki/`  — your output. LLM-generated, interlinked `.md`. You create/update freely.
- this file — the contract. We co-evolve it as conventions emerge. Don't bloat it.

## Layout
```
raw/                 # sources in, never touched
raw/assets/          # downloaded images
wiki/
  index.md           # catalog of every page (you maintain)
  log.md             # append-only event log (you maintain)
  overview.md        # the living synthesis / thesis
  sources/           # one summary page per ingested source
  entities/          # people, orgs, products, systems
  concepts/          # ideas, methods, patterns
```

## Page conventions
- Every page starts with YAML frontmatter:
  ```
  ---
  type: source | entity | concept | overview
  created: YYYY-MM-DD
  updated: YYYY-MM-DD
  sources: ["raw/<file>", ...]   # citation = stable identity, see Idempotency
  tags: [...]
  ---
  ```
- Cross-link aggressively with [[wikilinks]]. A page with no inbound links is a bug.
- Source pages cite their `raw/` origin in frontmatter `sources`. Always.
- Write claims so they trace to a source. No orphan assertions.

## Operations

### Ingest  (human drops a file in raw/, says "ingest X")
1. Read the source fully.
2. Surface 3-5 key takeaways to me before writing. Wait if I want to steer.
3. Write/update `wiki/sources/<slug>.md` (summary + frontmatter citation).
4. Update affected `entities/` and `concepts/` pages — create them if missing.
5. If new data contradicts an existing claim: do NOT silently overwrite.
   Note both, flag the contradiction inline, surface it to me.
6. Update `index.md`. Append one line to `log.md`.
   A single source typically touches 5-15 pages. That's expected.

### Query  ("ask the wiki ...")
1. Read `index.md` first to locate relevant pages, then drill in.
2. Answer with citations to wiki pages / raw sources.
3. If the answer is reusable (a comparison, an analysis, a synthesis),
   ASK whether to file it back as a new page. Good answers shouldn't die in chat.

### Lint  ("lint the wiki" — run periodically)
Health-check and report (don't auto-fix structural stuff without confirming):
- contradictions between pages
- stale claims superseded by newer sources
- orphan pages (no inbound links)
- concepts mentioned but lacking their own page
- missing cross-references
- gaps worth a web search
Suggest next sources/questions worth pursuing.

## Idempotency (the one rule that prevents rot)
Before writing any claim, check whether its source citation already exists in the
target page (grep the `sources` frontmatter / inline cites). If the source was already
ingested, UPDATE in place — never append a second near-duplicate. Re-ingesting the same
source in a different order must converge to the same state. Identity = citation, not text.

## index.md format
Catalog, grouped by type. Each entry: `[[page]] — one-line summary (n sources)`.
Update on every ingest.

## log.md format
Append-only, newest at bottom. One line per event, greppable prefix:
`## [YYYY-MM-DD] ingest | <Title>`  /  `query`  /  `lint`
So `grep "^## \[" wiki/log.md | tail -5` gives recent activity.

## Hard rules
- Never modify `raw/`.
- Never fabricate. If unsure, mark it and flag for review.
- Don't reconcile contradictions into a single "clean" account silently — surface them.
- Keep this schema lean. A new convention earns its place only after it's hurt twice.
