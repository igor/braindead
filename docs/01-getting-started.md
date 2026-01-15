# Getting Started

This guide helps you pick your entry point. You don't need to adopt everything at once—start with what solves your immediate problem.

## Choose Your Starting Point

### Level 1: Document One Process

**Tools needed:** Any text editor
**Best for:** Understanding if explicit protocols help you

**What you'll do:**
1. Pick ONE thing you do repeatedly (research process, writing workflow, client kickoff, analysis method)
2. Write it down in plain English markdown
3. Don't worry about AI or tools—just articulate your implicit method
4. Save it somewhere you'll actually reference it

**Why this matters:**
- Makes your expertise transferable (to your future self, to colleagues, eventually to AI)
- Reveals gaps in your thinking ("Wait, why *do* I do it that way?")
- Creates a baseline you can improve
- Shows whether explicitness helps or just adds overhead

**Example:** If you're a researcher, document your literature review process. If you're a consultant, document your project kickoff workflow. If you're a writer, document your editing process.

**Success metric:** You reference this document the next time you do the task, and it actually helps.

---

### Level 2: Add Structure

**Tools needed:** Any markdown tool (Obsidian recommended)
**Best for:** People with lots of unstructured notes

**What you'll do:**
1. Adopt PARA organization (Projects / Areas / Resources / Archive)
2. Move existing notes into this structure
3. Add simple YAML frontmatter to files (date, type, status)
4. Create a few templates for common file types

**Why this matters:**
- When everything has a place, you can find it (human benefit)
- When everything has metadata, AI can navigate it (machine benefit)
- Structure creates relationships between ideas automatically
- You stop asking "Where did I save that thing?"

**Quick setup:**

```text
your-vault/
├── 0. Inbox/           # Quick capture
├── 1. Projects/        # Time-bound work
├── 2. Areas/           # Ongoing domains
├── 3. Resources/       # Reference materials
└── 4. Archive/         # Completed work
```

Minimal frontmatter:

```yaml
---
created: 2026-01-14
updated: 2026-01-14
type: research
---
```

**Read next:** [PARA Organization](02-para-organization.md) for full details

**Success metric:** You can find any note within 30 seconds, and you know where new notes should go.

---

### Level 3: Make It Executable

**Tools needed:** Claude Code, markdown vault
**Prerequisites:** Levels 1 & 2 (or equivalent)
**Best for:** People who work extensively with AI

**What you'll do:**
1. Create a \`CLAUDE.md\` operating manual (global or project-specific)
2. Document your preferences, workflows, decision rules
3. Add workflow documentation for recurring tasks
4. Set up custom skills for common operations

**Why this matters:**
- Claude Code reads your CLAUDE.md and respects your preferences
- You stop repeating yourself ("No, use YAML not JSON. No, format it like X.")
- AI suggestions become actually useful (they follow your patterns)
- Your operating manual helps you even without AI (explicit beats implicit)

**Create your CLAUDE.md:**

```markdown
# Claude Code Instructions

## General Workflow Preferences

- **Complex tasks**: Use EnterPlanMode before executing
- **File writes**: Always use mkdir -p first
- **Documentation**: Comprehensive with rationale, not quick notes

## Work Context

[Your role, domain, quality expectations]

## Research & Knowledge Work

[Your research patterns, RAG preferences, source standards]
```

**Read next:** [Claude Configuration](04-claude-configuration.md) for full structure

**Success metric:** Claude Code follows your patterns without being reminded, and you catch yourself referencing your own CLAUDE.md for guidance.

---

### Level 4: Add Intelligence

**Tools needed:** Claude Code, RAG infrastructure (local or remote)
**Prerequisites:** Levels 1, 2 & 3
**Best for:** People with extensive knowledge bases (1000+ files)

**What you'll do:**
1. Set up RAG infrastructure (Ollama + ChromaDB + MCP)
2. Create collection registry (\`rag-collections.yaml\`)
3. Index your vault with semantic chunking
4. Build custom skills for collection-specific queries

**Why this matters:**
- Semantic retrieval across thousands of files in seconds
- AI finds relevant context you'd forgotten existed
- Relationships between ideas emerge automatically
- Your notes become active infrastructure, not passive archives

**Real example:** I had the [Gamestorming](https://gamestorming.com/) book (facilitation methods by Dave Gray, Sunni Brown, and James Macanufo) I'd used for years—trusted methodologies I knew well. I indexed it as a RAG collection with a `/gamestorming` skill. Now instead of flipping through pages looking for the right facilitation method, I just ask: "What's a good prioritization technique for distributed teams?" Same pattern works for any methodology you trust ([Thinking Tools](https://untools.co/), design patterns, research frameworks).

**RAG setup overview:**

1. **Infrastructure**: ChromaDB server (localhost or remote)
2. **Registry**: \`rag-collections.yaml\` as single source of truth
3. **Collections**: One per domain (vault, readwise, projects, etc.)
4. **Skills**: Custom \`/query-collection\` commands

**Read next:** [RAG Infrastructure](06-rag-infrastructure.md) for full setup

**Success metric:** You query your vault conversationally and get relevant results you wouldn't have found with keyword search.

---

## Learn More

**Core Concepts:**

- [PARA Method](https://fortelabs.com/blog/para/) - Tiago Forte's organization system
- [Claude Code](https://docs.anthropic.com/claude/docs/claude-code) - Anthropic's documentation
- [CLAUDE.md Configuration](https://docs.anthropic.com/claude/docs/claude-code-configuration) - Operating manual setup
- [Claude Code Skills](https://docs.anthropic.com/claude/docs/claude-code-skills) - Building custom commands
- [Obsidian](https://obsidian.md/) - Markdown knowledge base tool
- [YAML Frontmatter](https://help.obsidian.md/Editing+and+formatting/Properties) - Metadata in markdown

**Methodologies:**

- [Gamestorming](https://gamestorming.com/) - Facilitation methods by Gray, Brown, Macanufo
- [Thinking Tools](https://untools.co/) - Mental models and frameworks by Adam Amran

This repository shows how to INTEGRATE these concepts, not how to use them individually.

---

## Decision Tree: Which Level Do I Need?

### Start with Level 1 if:
- ❓ You're not sure if explicit protocols help
- 🎯 You want quick wins without tool investment
- 📝 You work mostly with human collaborators (not AI)

### Start with Level 2 if:
- 🗂️ You have lots of notes but can't find anything
- 🔍 You waste time searching for information you know you wrote down
- 🧩 Your work involves connecting ideas across projects

### Start with Level 3 if:
- 🤖 You already use Claude Code extensively
- 🔁 You repeat the same instructions to AI multiple times
- 📋 You have established workflows you want AI to follow

### Start with Level 4 if:
- 📚 You have 1000+ markdown files
- 🔗 You need to find connections across years of work
- 🚀 You're committed to RAG as infrastructure

**Most people should start with Level 1 or 2.**

---

## Prerequisites

### For Everyone (Levels 1-2)
- Text editor or markdown app (Obsidian recommended)
- Willingness to articulate implicit processes
- About 100-500 notes (if you have them)

### For Claude Code Users (Level 3)
- [Claude Code](https://claude.ai/code) installed
- macOS, Linux, or Windows
- Basic terminal comfort
- Structured vault (from Level 2)

### For RAG Infrastructure (Level 4)
- Level 3 setup complete
- Python 3.10+
- [Ollama](https://ollama.ai/) for embeddings
- ChromaDB for vector storage
- MCP server (included with Claude Code)
- 1000+ markdown files to make it worthwhile

---

## Common Pitfalls

### "I'll set up the perfect system first"
**Don't.** Start with Level 1. Document one process. See if it helps. If it does, add structure. If it doesn't, you saved yourself hours of setup.

### "I need to migrate all my notes"
**Don't.** Start with new notes in the structure. Migrate old notes only when you need them. Perfection is the enemy of progress.

### "I should replicate Igor's exact system"
**Don't.** This is a reference architecture, not a prescription. Adapt the patterns to your domain. Your field, your workflows, your constraints.

### "RAG will solve my organization problems"
**It won't.** RAG amplifies good structure. If your notes are chaotic, RAG will give you chaotic results faster. Do Levels 1-2 first.

### "I'll document everything at once"
**Don't.** Document on-demand. When you catch yourself doing something repeatedly, write it down. Don't pre-document hypothetical workflows.

---

## What Success Looks Like

### Level 1 Success:
- You have one documented process
- You reference it when doing the task
- It actually helps (even slightly)
- You understand the value of explicit protocols

### Level 2 Success:
- Every file has a clear home
- You can find any note in under 30 seconds
- New notes go into the right folder automatically
- Metadata helps you filter and navigate

### Level 3 Success:
- Claude Code respects your preferences
- You stop repeating instructions
- AI suggestions follow your patterns
- Your CLAUDE.md evolves as you learn

### Level 4 Success:
- You query your vault conversationally
- Relevant results surface from across years of work
- Connections emerge that you wouldn't have made manually
- Your knowledge base becomes active infrastructure

---

## Next Steps

**Pick your level:**
- [Level 1](#level-1-document-one-process): Just start documenting
- [Level 2](#level-2-add-structure): Adopt PARA + frontmatter → [Full Guide](02-para-organization.md)
- [Level 3](#level-3-make-it-executable): Create CLAUDE.md → [Full Guide](04-claude-configuration.md)
- [Level 4](#level-4-add-intelligence): Set up RAG → [Full Guide](06-rag-infrastructure.md)

**Or explore:**
- [Philosophy](00-philosophy.md) - Why explicit protocols matter
- [FAQs](08-faqs.md) - Common questions

---

**Remember:** You don't need all four layers. Start with what solves your immediate problem. Add layers only when you feel the need.
