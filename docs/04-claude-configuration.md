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

**Template:** [templates/claude-global/CLAUDE.md.template](../templates/claude-global/CLAUDE.md.template)

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

**Template:** [templates/vault/.claude/CLAUDE.md.template](../templates/vault/.claude/CLAUDE.md.template)

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

### Step 2: Copy the Template

**For global:**
```bash
mkdir -p ~/.claude
cp templates/claude-global/CLAUDE.md.template ~/.claude/CLAUDE.md
```

**For project/vault:**
```bash
mkdir -p {vault_path}/.claude
cp templates/vault/.claude/CLAUDE.md.template {vault_path}/.claude/CLAUDE.md
```

### Step 3: Replace Placeholders

Open the file and replace all `{PLACEHOLDERS}`:

- `{YOUR_ROLE}` → Your professional role (e.g., "Software architect", "Research analyst")
- `{YOUR_DOMAIN}` → Your specialization (e.g., "Distributed systems", "User research")
- `{RAG_HOST}` → Your RAG server hostname (e.g., "localhost", "192.168.1.100") if using remote RAG
- `{VAULT_PATH}` → Absolute path to your vault (e.g., "~/Documents/my-vault")
- etc.

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

**Templates available:** [templates/claude-global/strategic_docs/](../templates/claude-global/strategic_docs/)

---

## Settings and Environment

### settings.json

**Location:** `~/.claude/settings.json`

**Purpose:** Claude Code configuration (permissions, MCP servers, etc.)

**Template:** [templates/claude-global/settings.json.template](../templates/claude-global/settings.json.template)

**Sensitive values:** Use `settings.local.json` (not tracked in git)

### .env

**Location:** `~/.claude/.env`

**Purpose:** Environment variables (API keys, paths, etc.)

**Template:** [templates/claude-global/.env.example](../templates/claude-global/.env.example)

**Important:** Never commit `.env` to git. It's in `.gitignore`.

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

1. Copy the appropriate template ([global](../templates/claude-global/CLAUDE.md.template) or [vault](../templates/vault/.claude/CLAUDE.md.template))
2. Replace placeholders with your actual preferences
3. Start with 3-5 key preferences
4. Expand as patterns emerge
5. Create [custom skills](05-custom-skills.md) for common workflows
6. Set up [RAG infrastructure](06-rag-infrastructure.md) for semantic search (optional)

---

**Templates:**
- [Global CLAUDE.md template](../templates/claude-global/CLAUDE.md.template)
- [Vault CLAUDE.md template](../templates/vault/.claude/CLAUDE.md.template)
- [Strategic docs templates](../templates/claude-global/strategic_docs/)
- [Agent docs examples](../templates/vault/.claude/agent_docs/)
