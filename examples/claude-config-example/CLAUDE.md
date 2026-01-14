# Claude Code Instructions

## General Workflow Preferences

- **complex tasks**: use EnterPlanMode with AskUserQuestion to capture preferences before execution
- **file writes**: use `mkdir -p` before writing to ensure directory exists
- **multi-step workflows**: create TodoWrite task list BEFORE starting (for transparency and progress tracking)
- **documentation**: comprehensive structured docs with rationale, not quick notes - include decisions, alternatives, trade-offs
- **codebase exploration**: use Task tool with Explore agent for non-trivial discovery

## Infrastructure

*(This example uses remote RAG setup - most users can omit this section)*

**Local Machine**:

- Primary work machine
- Claude Code runs here
- Vault location: `~/Documents/vault`

**RAG Server** (optional - remote setup):

- Remote machine at `{RAG_HOST}:8000`
- Hosts: Ollama (embeddings), ChromaDB (storage)
- Connection: `ssh {RAG_HOST}`
- See `vault/.claude/agent_docs/rag-operations.md` for details

*Note: Most users run RAG locally. This remote setup is optional for advanced use cases.*

## Work Context

I'm an independent consultant working at the intersection of strategy and research. I work directly with clients on multi-month engagements.

My work combines:

- Strategic analysis and positioning
- Qualitative research and synthesis
- Stakeholder workshops and facilitation
- Executive-level presentations

*(This is a generic example - adapt to your actual role and domain)*

### Quality & Communication Expectations

Output quality:

- Executive-level strategic documents
- Client-ready deliverables
- Research-driven, not speculative
- Evidence-based recommendations

Communication style:

- Use frameworks to structure thinking (not just prose)
- Be specific and actionable (not vague or generic)
- Support claims with research/evidence
- Consider trade-offs explicitly

## Client Work & Communication

- **communication editing**: implement corrections directly when requested (not review-first)
- **tone**: professional but warm, authentic

## Research & Knowledge Work

- **RAG-first research**: consult vault knowledge first (ChromaDB), then external sources to fill gaps
- **research before implementation**: use external research for benchmarks/patterns before proposing solutions
- **avoid prescriptive recommendations**: provide frameworks and principles - let client decide via prototyping
- **RAG queries**: use multiple parallel queries (3+) for complex, multi-dimensional topics
- **RAG results**: use 15-20 results for exploratory research, 5-10 for focused queries

## Knowledge Vault

**Organization**: PARA (Projects/Areas/Resources/Archive)
**Location**: ~/Documents/vault
**Client communications**: Archive in `4. Archive/Clients/[Year]/[Client]/`

### RAG System Operations

**SINGLE SOURCE OF TRUTH**: `vault/.claude/agent_docs/rag-collections.yaml`

**BEFORE any RAG operation** (re-index, create collection, query troubleshooting):

1. READ `rag-collections.yaml` for current collection definitions
2. Check collection exists, get correct ingest script name, recommended n_results

**AFTER any collection change** (create, update, delete, re-index):

1. UPDATE `rag-collections.yaml` with new chunk counts, dates, or collection entries
2. This keeps the registry accurate for future operations

**Infrastructure**: RAG server at `{RAG_HOST}:8000` (or localhost), Ollama 0.13.5
**Service check**: `pgrep -f mcp_server.py` before operations (or `ssh {RAG_HOST} 'pgrep -f mcp_server.py'` for remote)

## Detailed Workflows

For complex, multi-step procedures, refer to topic-specific files:

- **YAML frontmatter requirements**: See `vault/.claude/agent_docs/yaml-frontmatter.md`
- **RAG operations details**: See `vault/.claude/agent_docs/rag-operations.md`
- **Project setup**: See `vault/.claude/commands/new-project.md`

## Strategic Work References

For domain-specific methodologies and standards:

- **Research methodology**: `~/.claude/strategic_docs/research_methodology.md` - Research processes, validation, evidence standards
- **Board presentations**: `~/.claude/strategic_docs/board_presentation_standards.md` - Structure, content standards, communication principles
- **Brand strategy**: `~/.claude/strategic_docs/brand_strategy_frameworks.md` - Core frameworks, selection criteria, examples

---

## Adapting This Example

This configuration shows patterns for:

- Knowledge-intensive consulting work
- Client project management
- Research synthesis workflows
- Strategic documentation

### Adapt These Patterns to Your Domain

**Software development:** Replace "client projects" with "product features", "strategic docs" with "technical specs", "research synthesis" with "requirements analysis"

**Academic research:** Replace "client" with "study/grant", focus on publication workflows, literature review synthesis, experiment documentation

**Product management:** Emphasize roadmap planning, user research, feature specs, stakeholder alignment, metrics tracking

**Design work:** Focus on design systems, user research synthesis, design documentation, portfolio management, critique frameworks
