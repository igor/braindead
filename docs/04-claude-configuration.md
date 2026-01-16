# Claude Configuration

**Learn about Claude Code configuration:** [Official CLAUDE.md Guide](https://docs.anthropic.com/claude/docs/claude-code-configuration)

**This document:** Shows configuration PATTERNS for this architecture, not a complete Claude Code tutorial.

---

Claude Code can read your preferences and workflows from `CLAUDE.md` files. This creates an operating manual that both you and Claude can reference.

## The Two-Tier System

### Global Configuration (`~/.claude/CLAUDE.md`)

**Purpose:** Your default preferences across all projects

**Location:** `~/.claude/CLAUDE.md` (in your home directory)

**Contains:**
- General workflow preferences
- Your professional context
- Infrastructure details
- Quality standards
- Research approaches
- Strategic work references

**When Claude reads it:** Every Claude Code session, regardless of project

### Project Configuration (`{project}/.claude/CLAUDE.md`)

**Purpose:** Project-specific overrides and additions

**Location:** `{vault_path}/.claude/CLAUDE.md` or `{project_path}/.claude/CLAUDE.md`

**Contains:**
- Vault-specific organization
- Project-specific workflows
- RAG system operations
- Custom skills for this project
- References to detailed agent_docs

**When Claude reads it:** When working in that project/vault directory

### How They Work Together

Claude reads **both** files in this order:

1. Global config (`~/.claude/CLAUDE.md`)
2. Project config (`{project}/.claude/CLAUDE.md`)

Project config **adds to and overrides** global config.

**Example:**

*Global CLAUDE.md:*
```markdown
## Documentation

- **style**: comprehensive with rationale
```

*Project CLAUDE.md:*
```markdown
## Documentation

- **style**: quick notes only (rapid prototyping project)
```

Claude uses the project override: quick notes for this project, comprehensive everywhere else.

---

## Three-Tier Pattern with Delegation (Advanced)

The two-tier system works well initially, but as your setup grows, you may find yourself duplicating operational details across multiple vaults or projects. The three-tier pattern solves this through **delegation**.

### When You Need This

**The two-tier system breaks down when you have:**

- Multiple vaults sharing the same infrastructure (RAG server, remote machines)
- Operational procedures duplicated across vault configs
- Infrastructure changes requiring updates to multiple CLAUDE.md files
- A mix of vault-specific context and shared operations

**Signals you're ready for three-tier:**

- You copy-paste RAG operations between vault CLAUDE.md files
- You maintain the same infrastructure details in multiple places
- You have 2+ vaults/projects with overlapping infrastructure
- You want a single source of truth for operations

### The Delegation Pattern

Instead of duplicating operational details, vaults **declare what they have** and **delegate how it works** to the global config.

**Structure:**

```
~/.claude/
  └── CLAUDE.md              # Operations & infrastructure (how)
  └── agent_docs/            # Global workflows

{vault_1}/.claude/
  └── CLAUDE.md              # Context & delegation (what + pointer)
  └── agent_docs/            # Vault-specific docs

{vault_2}/.claude/
  └── CLAUDE.md              # Context & delegation (what + pointer)
  └── agent_docs/            # Vault-specific docs
```

**Information flow:**

1. **Global** defines **how operations work** (procedures, infrastructure, commands)
2. **Vault** defines **what it provides** (collections, paths, structure) + **delegates** operations to global
3. **Project** (optional) provides **overrides** for specific needs

### Example: RAG System Delegation

**Global CLAUDE.md** (`~/.claude/CLAUDE.md`):

```markdown
### RAG System Operations

**SINGLE SOURCE OF TRUTH**: `brain dead/.claude/agent_docs/rag-collections.yaml`

**Infrastructure:**
- Host: Mac Mini at igor-dom.nord, port 8000
- Ollama version: 0.13.5
- Embedding model: nomic-embed-text
- RAG path: /Volumes/Storage/RAG

**BEFORE any RAG operation** (re-index, create collection, query troubleshooting):
1. READ `rag-collections.yaml` for current collection definitions
2. Check collection exists, get correct ingest script name, recommended n_results

**AFTER any collection change** (create, update, delete, re-index):
1. UPDATE `rag-collections.yaml` with new chunk counts, dates, or collection entries
2. This keeps the registry accurate for future operations

**Service check**: `pgrep -f mcp_server.py` before operations

**Quick reference**:
```bash
# Health check
curl -H 'X-API-Key: {YOUR_API_KEY}' http://{RAG_HOST}:{RAG_PORT}/health

# Re-index (see rag-collections.yaml for collection-specific commands)
ssh {RAG_HOST} 'cd /Volumes/Storage/RAG && source venv/bin/activate && python scripts/ingest_vault.py'
```

**Detailed documentation**: See `brain dead/.claude/agent_docs/rag-operations.md`
```

**Vault CLAUDE.md** (`{vault}/.claude/CLAUDE.md`):

```markdown
## RAG Collections Available

This vault provides access to the following RAG collections (see global CLAUDE.md for RAG operations):

- **braindead** - Full Brain Dead vault (primary collection)
- **readwise** - Readwise highlights and annotations
- **2025_portfolio** - Portfolio work and case studies
- **whatsapp** - WhatsApp conversation history (via whatsapp skill)

**Collection registry**: [.claude/agent_docs/rag-collections.yaml](.claude/agent_docs/rag-collections.yaml)

## Vault-Specific Workflows

For detailed vault operations, see:

- **YAML frontmatter requirements**: [agent_docs/yaml-frontmatter.md](.claude/agent_docs/yaml-frontmatter.md)
- **RAG metadata filtering**: [2. Areas/RAG/metadata_filtering_usage.md](2.%20Areas/RAG/metadata_filtering_usage.md)
```

**Key delegation phrase:** `"see global CLAUDE.md for RAG operations"`

### What Goes Where

**Global CLAUDE.md:**
- ✅ Infrastructure details (hosts, ports, paths)
- ✅ Operational procedures (how to re-index, query, maintain)
- ✅ Cross-cutting workflows (file operations, documentation style)
- ✅ Professional context and quality standards
- ✅ References to global `agent_docs/` and `strategic_docs/`

**Vault CLAUDE.md:**
- ✅ Vault structure (PARA paths, organization)
- ✅ Available collections (what this vault provides)
- ✅ Collection registry location (local `rag-collections.yaml`)
- ✅ References to vault-specific `agent_docs/`
- ✅ Delegation statements (`"see global CLAUDE.md for X"`)

**Project CLAUDE.md (optional):**
- ✅ Project-specific overrides
- ✅ Temporary exceptions to global rules
- ✅ Client-specific constraints

### Benefits of Delegation

**Single source of truth:**
- Infrastructure changes update once (global)
- No duplicate operational procedures
- Consistency across all vaults

**Clear separation of concerns:**
- Operations live globally (how it works)
- Context lives locally (what it has)
- Overrides live at project level (exceptions)

**Maintenance efficiency:**
- Update RAG host once, affects all vaults
- Change embedding model once, everywhere knows
- Add workflow preference once, available everywhere

**Scales naturally:**
- Add new vaults without duplicating infrastructure
- Each vault declares only what makes it unique
- Global config grows slowly (operations change rarely)

### Migration Path

**From two-tier to three-tier:**

1. **Identify duplication** - What's repeated across vault configs?
2. **Extract operations** - Move procedures to global CLAUDE.md
3. **Add delegation** - Replace duplicated text with "see global CLAUDE.md"
4. **Keep context local** - Leave vault-specific details (paths, collections) in vault config
5. **Test** - Ask Claude about RAG operations from different vaults

**Example migration:**

*Before (duplicated):*
- `vault_1/.claude/CLAUDE.md` - 200 lines including RAG operations
- `vault_2/.claude/CLAUDE.md` - 200 lines including RAG operations
- Changes require updating both files

*After (delegated):*
- `~/.claude/CLAUDE.md` - +100 lines for RAG operations (once)
- `vault_1/.claude/CLAUDE.md` - 50 lines (context + delegation)
- `vault_2/.claude/CLAUDE.md` - 50 lines (context + delegation)
- Changes update global file only

### When to Use

**Stick with two-tier if:**
- You work primarily in one vault
- Your setup is simple (local RAG, no remote infrastructure)
- You don't mind some duplication
- You're just getting started

**Upgrade to three-tier when:**
- You have 2+ vaults sharing infrastructure
- You're copy-pasting operational details between configs
- Infrastructure changes affect multiple vaults
- You want clear separation: operations (global) vs. context (vault)
- Your system has matured beyond experimental stage

**This pattern scales.** It's not required for everyone, but it solves real problems as complexity grows. Start simple, evolve when you feel the pain.

---

## What to Put in CLAUDE.md

### Workflow Preferences

How you want Claude to approach tasks:

```markdown
## General Workflow Preferences

- **complex tasks**: use EnterPlanMode before executing
- **file operations**: always use mkdir -p, ask before deleting
- **multi-step workflows**: create TodoWrite task list first
- **documentation**: comprehensive with rationale (not quick notes)
```

**Why this matters:** You stop repeating yourself. Claude remembers your patterns.

### Work Context

Who you are and what quality means:

```markdown
## Work Context

I'm a {YOUR_ROLE} specializing in {YOUR_DOMAIN}.

My work focuses on:
- {WORK_TYPE_1} (e.g., strategic documents, technical designs, research reports)
- {WORK_TYPE_2} (e.g., client deliverables, internal documentation)
- {WORK_APPROACH} (e.g., research-driven, evidence-based, framework-oriented)

**Communication style:**
- {STYLE_PREFERENCE_1} (e.g., use frameworks to structure thinking)
- {STYLE_PREFERENCE_2} (e.g., support claims with evidence)
- {STYLE_PREFERENCE_3} (e.g., consider trade-offs explicitly)
```

<!-- Example adaptations:
- Software engineer: "technical architect specializing in distributed systems"
- Researcher: "academic researcher specializing in cognitive science"
- Product manager: "product strategist specializing in B2B SaaS"
- Designer: "UX designer specializing in accessibility and user research"
-->

**Why this matters:** Claude understands your domain and adjusts tone, depth, and rigor accordingly.

### Infrastructure

Your technical setup:

```markdown
## Infrastructure

*(Optional - only if using remote RAG server)*

**RAG Server** ({RAG_HOST}):
- Remote machine for RAG infrastructure
- Hosts: Ollama (embeddings), ChromaDB (storage)
- Connection: `ssh {RAG_HOST}`
- Port: {RAG_PORT} (default: 8000)

*Most users run RAG locally and can omit this section.*

**Vault**: ~/Documents/vault
**Organization**: PARA
```

**Why this matters:** Claude knows how to connect to your systems and where files live.

### Detailed Workflows

Reference external documentation for complex procedures:

```markdown
## Detailed Workflows

For complex procedures, see:

- **YAML frontmatter**: See `.claude/agent_docs/yaml-frontmatter.md`
- **RAG operations**: See `.claude/agent_docs/rag-operations.md`
- **New project setup**: See `.claude/commands/new-project.md`
```

**Why this matters:** Keeps CLAUDE.md focused. Complex procedures live in dedicated files.

---

## Creating Your First CLAUDE.md

### Step 1: Choose Your Level

**Start with global if:**
- You use Claude Code across multiple projects
- You want consistent behavior everywhere
- You have general preferences (commit style, testing approach, etc.)

**Start with project/vault if:**
- You primarily work in one vault
- You want to experiment before going global
- Your project has unique needs

**Do both if:**
- You have both general preferences and project-specific needs (recommended for mature setups)

### Step 2: Create the File

**For global:**
```bash
mkdir -p ~/.claude
touch ~/.claude/CLAUDE.md
```

**For project/vault:**
```bash
mkdir -p {vault_path}/.claude
touch {vault_path}/.claude/CLAUDE.md
```

### Step 3: Start with Your Context

Begin by documenting:

- **Who you are professionally** - your role, domain, expertise
- **What quality means in your work** - standards, expectations, output types
- **How you prefer to work** - workflow patterns, tool preferences
- **Your infrastructure** - if relevant (RAG setup, remote servers, etc.)

Don't overthink it. Start with a few bullet points. You'll expand as patterns emerge.

### Step 4: Document Your Patterns

As you work with Claude Code, notice when you repeat instructions:

- "No, format commit messages like X"
- "Always run tests before committing"
- "Use this research methodology, not that one"

**Add these to CLAUDE.md.**

### Step 5: Evolve Over Time

Your CLAUDE.md should grow with your practice:

- Add preferences as you discover them
- Refine descriptions as patterns clarify
- Remove what doesn't serve you

**It's a living document.**

---

## Agent Docs Pattern

For complex, multi-step procedures, create separate files in `.claude/agent_docs/`:

### Example: YAML Frontmatter Rules

**File:** `.claude/agent_docs/yaml-frontmatter.md`

**Contains:**
- Required fields and options
- When to ask user vs. infer
- Template structure
- Validation rules

**Referenced from CLAUDE.md:**
```markdown
## Detailed Workflows

- **YAML frontmatter**: See `.claude/agent_docs/yaml-frontmatter.md`
```

**Why separate:** Keeps CLAUDE.md scannable. Claude loads agent_docs when needed.

### Common Agent Docs

- `yaml-frontmatter.md` - Metadata rules
- `rag-operations.md` - RAG infrastructure docs
- `rag-collections.yaml` - Collection registry (single source of truth)
- `things3-management.md` - Task management rules
- `{custom-workflow}.md` - Your domain-specific procedures

---

## Strategic Docs Pattern

For domain-specific methodologies and frameworks, create files in `~/.claude/strategic_docs/`:

### Example: Research Methodology

**File:** `~/.claude/strategic_docs/research_methodology.md`

**Contains:**
- Research processes (primary, secondary)
- Validation approaches
- Evidence standards
- Quality criteria

**Referenced from CLAUDE.md:**
```markdown
## Strategic Work References

- **Research methodology**: `~/.claude/strategic_docs/research_methodology.md`
```

**Why separate:** Your methodologies are reusable across projects. Keep them global.

### Common Strategic Docs

- `research_methodology.md`
- `board_presentation_standards.md`
- `brand_strategy_frameworks.md`
- `foresight_planning_approach.md`

**How to create these:** Document the methodologies you actually use. Not aspirational frameworks - the actual processes you follow and trust.

---

## Settings and Environment

### settings.json

**Location:** `~/.claude/settings.json`

**Purpose:** Claude Code configuration (permissions, MCP servers, etc.)

**Documentation:** See [Claude Code Settings Documentation](https://docs.anthropic.com/claude/docs/claude-code-configuration#settings)

**Sensitive values:** Use `settings.local.json` (not tracked in git)

### .env

**Location:** `~/.claude/.env`

**Purpose:** Environment variables (API keys, paths, etc.)

**Important:** Never commit `.env` to git. Add it to `.gitignore`.

---

## Testing Your Configuration

### Verify Claude Reads It

Start a Claude Code session in your project:

```
You: "What do you know about my setup?"
Claude: [Should reference your CLAUDE.md preferences]
```

### Test a Preference

Add a preference to CLAUDE.md:

```markdown
- **commit messages**: use conventional commits format (feat:, fix:, docs:)
```

Then ask Claude to create a commit. It should follow that format without being reminded.

### Check Agent Docs

Reference an agent doc in CLAUDE.md, then ask Claude to perform that workflow. It should read and follow the agent doc.

---

## Common Patterns

### Preference: EnterPlanMode for Complex Tasks

```markdown
- **complex tasks**: use EnterPlanMode with AskUserQuestion before execution
```

**Effect:** Claude will propose a plan and ask clarifying questions instead of jumping into implementation.

### Preference: TodoWrite for Multi-Step Work

```markdown
- **multi-step workflows**: create TodoWrite task list BEFORE starting
```

**Effect:** Claude creates a visible task list so you can track progress.

### Preference: RAG-First Research

```markdown
- **research approach**: consult vault RAG first, then external sources to fill gaps
```

**Effect:** Claude checks your knowledge base before searching externally.

### Domain Context: Board-Level Quality

```markdown
**Output quality:**
- Board-level strategic documents
- Client-ready deliverables
- Research-driven, not speculative
```

**Effect:** Claude adjusts depth, rigor, and polish to match board presentation standards.

---

## Maintenance

### Weekly

- Notice repeated corrections → add to CLAUDE.md
- Review diary entries for emerging patterns

### Monthly

- Review CLAUDE.md for outdated preferences
- Consolidate similar preferences
- Move complex rules to agent_docs

### Quarterly

- Review strategic_docs for relevance
- Update methodology documentation
- Archive deprecated approaches

---

## Privacy Considerations

### What's Safe to Include

- Workflow preferences
- Quality standards
- Tool configurations
- Folder structures
- Generic methodologies

### What to Exclude

- Client names (use placeholders)
- Project-specific details in global config
- API keys (use .env instead)
- Proprietary methodologies
- Confidential processes

### Sharing Your Config

If open-sourcing your setup:
1. Replace all personal details with `{PLACEHOLDERS}`
2. Remove client references
3. Sanitize infrastructure details
4. Keep methodologies generic

---

## Next Steps

1. Create your CLAUDE.md file (global or project-specific)
2. Start with 3-5 key preferences - your most repeated instructions
3. Work with Claude Code and notice what you keep explaining
4. Add those patterns to your CLAUDE.md
5. Expand as your practice evolves
6. Create [custom skills](05-custom-skills.md) for common workflows (optional)
7. Set up [RAG infrastructure](06-rag-infrastructure.md) for semantic search (optional)

**Remember:** Your CLAUDE.md should reflect your actual patterns, not ideal ones. Document what you do, not what you think you should do.
