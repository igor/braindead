# PARA Organization

This architecture uses [Tiago Forte's PARA method](https://fortelabs.com/blog/para/) for folder organization.

**Learn PARA:** Read [Tiago Forte's complete guide](https://fortelabs.com/blog/para/) to understand the full methodology.

**This document:** Shows how PARA creates context for Claude Code and RAG, not how to do PARA itself.

---

## Why PARA for AI Systems

PARA isn't just folder organization—it creates semantic context that AI can understand.

### For Humans

- **Actionability:** Projects come first—what needs attention now is immediately visible
- **Clarity:** Clear distinctions prevent "where does this go?" paralysis
- **Flexibility:** Easily move items between categories as work evolves

### For AI (The Integration Layer)

**Semantic context without explanation:**

- `1. Projects/Client_Acme/` → AI knows this is active client work
- `3. Resources/Methodologies/` → AI knows these are reference materials
- `4. Archive/Clients/2024/` → AI knows this is historical, completed work

**Metadata flow:**

- PARA structure + YAML frontmatter = rich context for RAG indexing
- Folder location provides first layer of meaning
- Frontmatter adds machine-readable details
- Together: semantic search that understands context

**Natural queries map to structure:**

- "What client projects are active?" → `1. Projects/Client_*`
- "Find research about X" → `3. Resources/` + semantic search
- "What did we do for Client Y?" → `4. Archive/Clients/*/Y_*`

**RAG benefits:**

- Query scope: "Search only active projects" vs "Search archive"
- Time-based filtering: Archive organized by year
- Context preservation: File path tells story even years later

---

## Folder Naming Conventions

These naming patterns create consistency that both humans and AI can rely on.

### Projects

**Pattern:** `{Scope}_{Client/Topic}_{ProjectName}`

**Scope prefixes:**

- `Client_` - Client work
- `Internal_` - Internal work

**Examples:**

- `Client_Acme_BrandStrategy`
- `Client_VentureCo_UserResearch`
- `Internal_ProcessDocumentation`
- `Internal_RAGSystem`

**Why this matters:** Claude can filter by scope, identify client vs internal work, understand project naming from path alone.

### Areas

**Pattern:** `{AreaName}` (no prefix needed)

**Examples:**

- `Studio_OS`
- `Professional_Development`
- `Infrastructure`

**Why this matters:** Areas are ongoing responsibilities—no date markers, no completion states.

### Resources

**Pattern:** Organized by theme/topic

**Examples:**

- `Methodologies/Facilitation/Gamestorming`
- `Frameworks/Strategy`
- `Industry_Research/AI_Strategy`
- `Tools/Software_Inventory`

**Why this matters:** Thematic organization makes RAG queries more effective. "Find facilitation methods" knows where to look.

### Archive

**Pattern:** `{SourceCategory}/{Year}/{ItemName}`

**Examples:**

- `Clients/2024/Acme_BrandStrategy`
- `Clients/2025/VentureCo_Research`
- `Internal/2024/OldRAGSystem`

**Why this matters:** Year-based archiving enables time-scoped queries. "What did we do in 2024?" or "Find that client from 2023."

---

## PARA + Frontmatter Integration

PARA provides folder structure. YAML frontmatter adds machine-readable metadata. Together they create rich semantic context.

**Example file:**

```yaml
---
type: research
scope: client
project: "Client_Acme_BrandStrategy"
created: 2026-01-14
updated: 2026-01-14
tags: [brand-strategy, consumer-research, qualitative]
---
```

**Path:**
```
1. Projects/Client_Acme_BrandStrategy/03_Research_Insights/Consumer_Research.md
```

**What AI reads:**

1. **From folder path:**
   - `1. Projects/` → Active work, not archived
   - `Client_Acme_BrandStrategy/` → Client project, specific client
   - `03_Research_Insights/` → Research phase, not strategy or deliverable

2. **From frontmatter:**
   - `type: research` → Research document, not deliverable
   - `scope: client` → Confidential, client-specific
   - `created: 2026-01-14` → Recent, current context
   - `tags: [brand-strategy, ...]` → Semantic classification

3. **Combined context:**
   - Active client research from January 2026
   - Part of brand strategy project for Acme
   - Qualitative consumer research findings
   - Not yet synthesized into deliverable

**Result:** RAG can find this with queries like:

- "Recent consumer research findings"
- "Acme brand strategy research"
- "Active qualitative studies"

Without PARA + frontmatter, it's just "Consumer_Research.md" somewhere in your files.

See [Frontmatter Schema](03-frontmatter-schema.md) for complete field definitions.

---

## Project Structure Template

Our specific pattern for project folders:

```text
1. Projects/Client_Acme_BrandStrategy/
├── 00_Project_Management/
│   ├── README.md               # Project overview
│   └── INSTRUCTIONS.md         # Operating context for Claude
├── 01_Strategic_Foundation/
│   ├── Stakeholder_Presentations/
│   └── Client_Materials/
├── 02_Research_Frameworks/
├── 03_Research_Insights/
├── 04_Strategy_and_Planning/
├── 05_Data_and_Exports/
├── 06_Transcripts/
└── ARCHIVE/
```

**Why numbered folders:**

- Enforces project phase sequence
- Makes chronological flow obvious to AI
- Prevents "where does this go?" ambiguity

**Why INSTRUCTIONS.md:**

- Gives Claude project-specific context
- Documents stakeholders, constraints, preferences
- Makes project self-documenting

**Build your own structure** based on what your projects actually need.

---

## Integration with RAG

PARA structure makes RAG queries more effective:

**Without PARA:**

```text
Query: "brand strategy research"
Returns: 847 files with those words
Problem: No way to scope by active vs archived, client vs internal
```

**With PARA:**

```text
Query: "brand strategy research" + scope: "1. Projects/Client_*"
Returns: 12 active client project files
Result: Relevant, scoped, actionable
```

**RAG collection strategies:**

1. **Single vault collection** - Index everything, filter by path
2. **Separate collections** - Projects vs Archive vs Resources
3. **Client-specific collections** - Per-client archives for confidentiality

See [RAG Infrastructure](06-rag-infrastructure.md) for collection design patterns.

---

## Common Questions

### Should I have subprojects?

**No.** Keep Projects flat. If something is big enough to be separate, make it a separate project.

**Why:** Flat structure is easier for AI to query and humans to navigate.

### What if a project touches multiple areas?

**The project lives in Projects.** Reference areas via frontmatter or notes.

**Example:** `Client_Acme_BrandStrategy` might support both "Business Development" and "Professional Development" areas. Document this in frontmatter, don't duplicate the project.

### When does an area become a project?

**When it needs focused, time-bound work with a defined outcome.**

**Example:** "Area: Studio Operations" spawns "Project: Document Client Onboarding Process."

### Should I separate client and internal at top level?

**No.** Keep all Projects together. Use the `Client_`/`Internal_` prefix and `scope` frontmatter field to distinguish.

**Why:** Prevents duplication of folder structures. Single `1. Projects/` folder is simpler.

### How do I handle resources used across many projects?

**Keep them in `3. Resources/`, reference from projects as needed.**

**For frequently-used methodologies:** Consider RAG indexing with custom skills.

**Example:** [Gamestorming](https://gamestorming.com/) facilitation methods in `3. Resources/Methodologies/Gamestorming/` → indexed as RAG collection → queryable via `/gamestorming` skill.

See [Custom Skills](05-custom-skills.md) for methodology indexing patterns.

---

## Progressive Adoption

**Don't migrate everything at once.** Start with new work, move old notes only when you need them.

**Pattern:**

```text
New work → Use PARA structure from day one
Old notes → Migrate when accessed
Never → Batch migrate everything upfront
```

**Why this works:** You get value immediately (new work organized) without massive upfront cost (organizing years of notes).

See [Integration Patterns](07-workflows.md#pattern-7-on-demand-migration) for on-demand migration workflow.

---

## Next Steps

1. **Learn PARA methodology:** Read [Tiago Forte's guide](https://fortelabs.com/blog/para/)
2. **Create your structure:** Build folders based on your actual project needs
3. **Add frontmatter:** See [Frontmatter Schema](03-frontmatter-schema.md) for machine-readable metadata
4. **Configure Claude:** Set up [Claude Code configuration](04-claude-configuration.md) to maintain structure
5. **Optional: Add RAG:** [RAG Infrastructure](06-rag-infrastructure.md) for semantic search across your vault

---

**Remember:** This document shows how PARA integrates with Claude Code and RAG. For PARA methodology itself, see [Tiago Forte's complete guide](https://fortelabs.com/blog/para/).
