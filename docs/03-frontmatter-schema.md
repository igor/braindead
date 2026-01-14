# Frontmatter Schema

**Learn about YAML frontmatter:** [Obsidian Properties](https://help.obsidian.md/Editing+and+formatting/Properties) | [YAML Syntax](https://yaml.org/spec/1.2.2/)

**This document:** Shows OUR frontmatter schema for this architecture, not a YAML tutorial.

---

YAML frontmatter adds machine-readable metadata to markdown files. When combined with PARA organization, it creates rich semantic context for both humans and AI systems.

## Why Frontmatter Matters

**For humans:**
- Quick metadata at a glance
- Consistent classification
- Easy filtering and search

**For AI (Claude Code + RAG):**
- Semantic context without reading full content
- Structured queries ("find all client deliverables from 2025")
- Better retrieval relevance

**For tools:**
- Obsidian dataview queries
- RAG indexing metadata
- Automated workflows

---

## Required Fields

These fields are required for files in `1. Projects/`, `2. Areas/`, `3. Resources/`, and `4. Archive/`:

### type

**Purpose:** Classifies the document's function

**Options:**
- `research` - Primary or secondary research
- `concept` - Conceptual frameworks, mental models
- `deliverable` - Client-facing or final outputs
- `reference` - Reference material, documentation
- `transcript` - Meeting or interview transcripts
- `protocol` - Session logs, daily protocols
- `index` - Index or navigation documents
- `operations` - Operating procedures, instructions
- `guide` - How-to documentation

**How to choose:**
- Ask: "What is this document's primary purpose?"
- When in doubt, `reference` is a safe default
- Can be refined later as patterns emerge

### scope

**Purpose:** Indicates who this is for

**Options:**
- `client` - Client-facing or client work
- `internal` - Internal work, personal, or company

**Why it matters:**
- Separates billable vs. internal work
- Privacy and sharing considerations
- Different standards and audiences

### created / updated

**Format:** `YYYY-MM-DD`

**Purpose:**
- Track document lifecycle
- Find recent vs. stale information
- RAG can weight recent content higher

**Best practice:**
- Set `created` when file is first written
- Update `updated` every time you make significant changes
- Automation can handle this, but manual is fine

---

## Contextual Fields

At least one of these must be present:

### project

**Purpose:** Links document to a specific project

**Format:** String matching the project folder name

**Example:**
```yaml
project: "Client_Acme_BrandStrategy"
```

**When to use:** Files in `1. Projects/` folders

**Inference:** Can be automatically inferred from file path

### area

**Purpose:** Links document to an ongoing area of responsibility

**Format:** String matching the area folder name

**Example:**
```yaml
area: "Studio_OS"
```

**When to use:** Files in `2. Areas/` folders

**Can coexist with project:** Yes! A project can serve multiple areas.

Example:
```yaml
project: "Client_Acme_BrandStrategy"
area: "Professional_Development"  # Learning from this project
```

---

## Optional Fields

Add these when relevant:

### deliverable_type

**Purpose:** Specifies the kind of deliverable

**When to use:** When `type: deliverable`

**Options:**
- `framework` - Strategic framework
- `workshop` - Workshop design or materials
- `analysis` - Analysis report
- `presentation` - Board deck, stakeholder presentation
- `text` - Written content (articles, copy, etc.)
- `guide` - Process guide, documentation

**Example:**
```yaml
type: deliverable
deliverable_type: presentation
```

### language

**Purpose:** Document language (for multilingual work)

**Options:**
- `en` - English
- `de` - German
- `es` - Spanish
- etc. (ISO 639-1 codes)

**When to use:**
- Multilingual client work
- Helps with search and RAG filtering

### tags

**Purpose:** Topical keywords for cross-cutting themes

**Format:** Array of lowercase strings

**Examples:**
```yaml
tags: [brand-strategy, qualitative-research, consumer-insights]
tags: [ai, automation, process-improvement]
```

**Best practices:**
- Use lowercase with hyphens
- Keep tags broad (5-15 per project)
- Tags complement PARA structure, don't replace it
- Useful for cross-project themes

---

## Complete Examples

### Client Project Research Document

```yaml
---
type: research
scope: client
project: "Client_Acme_BrandStrategy"
created: 2026-01-10
updated: 2026-01-14
tags: [consumer-research, qualitative, interviews]
---
```

**Path:** `1. Projects/Client_Acme_BrandStrategy/03_Research_Insights/Consumer_Interviews_Summary.md`

### Area Operations Document

```yaml
---
type: operations
scope: internal
area: "Studio_OS"
created: 2025-12-01
updated: 2026-01-14
tags: [process, documentation, workflow]
---
```

**Path:** `2. Areas/Studio_OS/Project_Kickoff_Process.md`

### Client Deliverable

```yaml
---
type: deliverable
deliverable_type: presentation
scope: client
project: "Client_Acme_BrandStrategy"
language: en
created: 2026-01-14
updated: 2026-01-14
tags: [board-presentation, strategy, recommendations]
---
```

**Path:** `1. Projects/Client_Acme_BrandStrategy/01_Strategic_Foundation/Board_Presentation_Final.md`

### Resource Reference Material

```yaml
---
type: reference
scope: internal
created: 2024-06-15
updated: 2026-01-10
tags: [methodology, facilitation, workshop]
---
```

**Path:** `3. Resources/Methodologies/Facilitation/Workshop_Design_Patterns.md`

### Daily Protocol

```yaml
---
type: protocol
project: "Client_Acme_BrandStrategy"
date: 2026-01-14
tags: [protocol, claude-session]
---
```

**Path:** `protocols/2026-01-14.md`

---

## Automation and Inference

### Path-based Inference

Claude Code can infer fields from file path:

```
1. Projects/Client_Acme_BrandStrategy/03_Research_Insights/Document.md
              ↓
project: "Client_Acme_BrandStrategy"
```

```
2. Areas/Studio_OS/00_context/Document.md
          ↓
area: "Studio_OS"
```

### Type Inference (Partial)

Some types can be inferred from path:

- `protocols/` → `type: protocol`
- `INSTRUCTIONS.md` → `type: operations`
- `README.md` → `type: reference` or `type: index`

**But still ask:** User preferences vary. Always confirm `type` with user when creating new files.

---

## Validation

### Required Field Checklist

Before saving a file in PARA folders:

- [ ] `type` is specified and valid
- [ ] `scope` is specified (`client` or `internal`)
- [ ] `created` is set to today's date
- [ ] `updated` is set to today's date
- [ ] At least one of `project` or `area` is set (or can be inferred from path)

### Optional Validation Script

See [scripts/validate-frontmatter.py](../scripts/README.md) for automated validation (optional).

---

## RAG Integration

### How RAG Uses Frontmatter

When documents are indexed in RAG collections, frontmatter becomes metadata:

**Query:** "Find all client deliverables from Q4 2025"

**RAG filters by:**
- `scope: client`
- `type: deliverable`
- `created` or `updated` in date range

**Result:** More precise retrieval than keyword search alone.

### Collection-Specific Frontmatter

Some RAG collections may have additional metadata requirements. Consult your `rag-collections.yaml` for collection-specific needs.

---

## Common Questions

**Q: Do I need frontmatter in every markdown file?**
A: Only in `1. Projects/`, `2. Areas/`, `3. Resources/`, and `4. Archive/`. Files in `0. Inbox/`, `protocols/`, or `Daily/` can omit it or use minimal frontmatter.

**Q: What if I forget to add frontmatter?**
A: Add it when you next edit the file. Not retroactive—focus on new files.

**Q: Can I add custom fields?**
A: Yes! The schema is extensible. Just be consistent. Common additions: `status`, `priority`, `client_name`, `phase`.

**Q: How do I handle multi-project documents?**
A: Pick the primary project. If truly spanning multiple projects, consider:
  - Duplicate in each project (acceptable for different audiences)
  - Keep in one project, reference from others
  - Move to Resources if it's truly generic

**Q: Should I tag everything?**
A: No. Tags are for cross-cutting themes. Most documents don't need tags. PARA structure + `type`/`scope` is often sufficient.

---

## Next Steps

- Review [PARA Organization](02-para-organization.md) to understand where files live
- Use templates that include frontmatter examples
- Set up [Claude Code configuration](04-claude-configuration.md) to automatically prompt for frontmatter
- See [yaml-frontmatter.md template](../templates/vault/.claude/agent_docs/yaml-frontmatter.md) for the rules Claude Code follows

---

**Template reference:** [templates/vault/.claude/agent_docs/yaml-frontmatter.md](../templates/vault/.claude/agent_docs/yaml-frontmatter.md)
