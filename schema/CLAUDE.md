# OMNISCIENT Knowledge Base — LLM Wiki Schema

This is the schema file for the OMNISCIENT knowledge base. The LLM uses this
to understand how the wiki is structured, what conventions to follow, and what
workflows to run when ingesting sources, answering questions, or maintaining
the wiki.

## Architecture

Three layers:
1. **raw/sources/** — immutable source documents (PDFs, articles, research papers, code analysis)
2. **wiki/** — LLM-generated markdown pages (summaries, entity pages, concept pages, architecture docs)
3. **schema/CLAUDE.md** — this file (rules, conventions, workflows)

## Conventions

- Every wiki page has YAML frontmatter: `title`, `type` (entity|concept|source|architecture|decision), `date`, `tags`
- Cross-references use `[[wikilink]]` syntax
- `index.md` is the content catalog — updated on every ingest
- `log.md` is the chronological operation record — append-only

## Workflows

### Ingest (when a new source or change is added to OMNISCIENT)
1. Read the source/change
2. Write a summary page in wiki/
3. Update relevant entity/concept pages
4. Update index.md
5. Append to log.md

### Query (when asked about the codebase)
1. Read index.md to find relevant pages
2. Read those pages
3. Synthesize an answer with citations to wiki pages

### Lint (periodic health check)
1. Check for contradictions between pages
2. Find orphan pages with no inbound links
3. Note stale claims superseded by newer changes
4. Suggest new sources to ingest

## Page Types

- **architecture/** — system architecture, module maps, dependency graphs
- **entities/** — individual modules, components, API routes
- **concepts/** — algorithms, strategies, design patterns used
- **decisions/** — architectural decision records (ADRs)
- **sources/** — summaries of external research (papers, guides)
