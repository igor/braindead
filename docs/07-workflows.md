# Integration Patterns

How PARA + Frontmatter + CLAUDE.md + Skills + RAG work together.

**Learn about workflows:** [Claude Code Documentation](https://docs.anthropic.com/claude/docs/claude-code)

This document shows integration PATTERNS, not step-by-step workflows. Use Claude Code to implement these patterns for your specific needs.

---

## Pattern 1: RAG-First Research

**Concept:** Query your vault semantically before turning to external sources.

**Why it works:**

- Your past work has context external sources lack
- Research you've already done and validated
- Patterns from your actual experience
- Client-specific insights and constraints

**How it integrates:**

1. **PARA structure** creates semantic boundaries (Projects vs Resources vs Archive)
2. **Frontmatter** adds metadata for filtering (type: research, project: X)
3. **RAG** finds relevant content across years of work
4. **Skills** make queries conversational (`/vault`, `/portfolio`)
5. **CLAUDE.md** documents when to use which collection

**Real example:**
```
You: "What brand positioning frameworks have I used successfully?"

Claude: [Queries portfolio collection, finds 3 past projects with positioning work]

Returns: Specific frameworks you've used, what worked, what didn't,
client context, with links to original work.
```

**Value:** Your past work informs new work. Research builds on research instead of starting from zero.

**Learn more:** [Claude Code Skills](https://docs.anthropic.com/claude/docs/claude-code-skills)

---

## Pattern 2: Methodology as Code

**Concept:** Transform reference books and methodologies into queryable collections.

**Real example:** [Gamestorming](https://gamestorming.com/) (facilitation methods by Dave Gray, Sunni Brown, and James Macanufo)

I had this book I'd used for years—trusted methodologies I knew well. Instead of flipping through pages during client planning, I:

1. **Structured the content** - Created markdown files for each method
2. **Indexed as RAG collection** - Added to ChromaDB
3. **Created slash command** - Built `/gamestorming` skill
4. **Now it's conversational** - Ask "What's a good prioritization method for distributed teams?" instead of searching an index

**How it integrates:**

- **PARA Resources** organize methodologies by domain
- **Frontmatter** tags methods by purpose, complexity, time needed
- **RAG** enables semantic search ("I need a team-building exercise" finds relevant methods)
- **Skills** create domain-specific queries
- **CLAUDE.md** references when to consult which methodology

**Same pattern works for:**

- [Thinking Tools](https://untools.co/) (mental models and frameworks by Adam Amran)
- Your proprietary frameworks
- Industry research
- Academic papers
- Design patterns
- Any reference material you use repeatedly

**Pattern:**
```
Trusted reference → Markdown structure → RAG index → Custom skill → Conversational access
```

**Value:** Reference material becomes contextual. Methods surface based on your actual question, not page order in a book.

---

## Pattern 3: Living Operating Manual

**Concept:** CLAUDE.md evolves with your preferences and patterns.

**How it integrates:**

- **CLAUDE.md (global)** defines your general work preferences
- **CLAUDE.md (project)** adds project-specific context
- **Diary/protocol skills** capture decisions and patterns
- **Reflection analysis** identifies repeated preferences
- **CLAUDE.md updates** codify those patterns

**Example evolution:**

1. You notice Claude keeps asking about presentation format
2. You add to CLAUDE.md: "Board presentations: 15-20 slides, headline-driven, evidence on slide"
3. Next time: Claude follows this automatically
4. You document it once, it applies forever

**Integration with other patterns:**

- References RAG collections to consult (`/gamestorming` for facilitation)
- Points to strategic docs (`board_presentation_standards.md`)
- Documents research preferences (RAG-first, then Perplexity)
- Captures quality standards (frameworks over prose, evidence-driven)

**Value:** Your working preferences become explicit. AI collaboration becomes more efficient over time as patterns are documented.

**Learn more:** [CLAUDE.md Configuration](https://docs.anthropic.com/claude/docs/claude-code-configuration)

---

## Pattern 4: Progressive Knowledge Capture

**Concept:** Extract reusable insights from project work into Resources.

**How it integrates:**
```
Client project teaches you something →
Extract methodology to 3. Resources/ →
Add frontmatter (type: reference, scope: internal) →
Index in RAG collection →
Now queryable for future projects
```

**Why folder structure matters:**

- **1. Projects/** - Active, client-specific, temporary
- **3. Resources/** - Generalized, reusable, permanent
- **4. Archive/** - Completed, historical, searchable

**Integration points:**

- **PARA** separates temporary (Projects) from permanent (Resources)
- **Frontmatter** marks what's client-specific vs generalizable
- **Archive strategy** keeps past work searchable without cluttering active work
- **RAG** finds patterns across all three domains
- **Skills** can filter by scope (client vs internal)

**Value:** Client work compounds. Each project adds to your knowledge base instead of living in isolated folders.

---

## Pattern 5: RAG + Skills + CLAUDE.md Integration

**Concept:** The three systems work together to provide context-aware assistance.

**Example scenario:** Facilitating a prioritization session

**Setup:**

1. Methodologies indexed in RAG ([Gamestorming](https://gamestorming.com/), [Thinking Tools](https://untools.co/))
2. Skills created for querying (`/gamestorming`, `/thinking-tools`)
3. CLAUDE.md references facilitation approach

**Workflow:**
```
You: "I need to facilitate a prioritization session with 8 stakeholders next week"

Claude:
[Checks CLAUDE.md → sees facilitation preferences]
[Queries /gamestorming collection → finds prioritization methods]
[Considers context → 8 people, remote/in-person unknown]

Returns: Impact/Effort Matrix (primary), Dot Voting (backup),
with time estimates, materials needed, process steps, why each works.

Also references your board presentation standards for documenting results.
```

**How integration works:**

- **CLAUDE.md** provides context (facilitation approach, quality standards)
- **RAG** retrieves relevant methodology (Gamestorming methods)
- **Skills** structure the query (domain-specific search)
- **Frontmatter** enables filtering (prioritization vs team-building methods)
- **PARA** organizes where to save prep materials (project-specific)

**Result:** RAG provides content, skills structure queries, CLAUDE.md provides your preferences. You get methodology + your context.

---

## Pattern 6: Semantic Project Memory

**Concept:** RAG makes past project work searchable by concept, not just keywords.

**Keyword search problem:**

- Query: "client concerns about pricing"
- Returns: Only files with words "client", "concerns", "pricing"
- Misses: "stakeholder anxiety about cost", "budget sensitivities", "price objections"

**Semantic search solution:**

- Query: "client concerns about pricing"
- Returns: All conceptually related content regardless of exact wording
- Includes: Stakeholder feedback, pricing discussions, cost objections, budget constraints

**How it integrates:**

- **4. Archive/Clients/** organized by year and client
- **Frontmatter** captures project phase, deliverable type, stakeholders
- **RAG indexing** includes archived work (not just active)
- **Skills** can filter by date range, client, project phase
- **CLAUDE.md** documents archiving workflow

**Value:** Years of client work become a searchable knowledge base. "What did previous clients say about X?" becomes answerable.

---

## Pattern 7: On-Demand Migration

**Concept:** Structure adopts progressively, not all at once.

**Anti-pattern:** "Migrate all 10,000 notes to PARA this weekend"

**Working pattern:**
```
New work → Starts in structure (use templates)
Old notes → Migrate when you need them
Never → Batch migrate everything upfront
```

**How it integrates:**

- **Templates** ensure new work has proper structure
- **Frontmatter** added when old notes are accessed
- **PARA** adoption happens file-by-file over time
- **RAG** finds old notes even if they're not structured yet
- **Archive** collects migrated work

**Value:** You get value immediately (new work structured) without massive upfront investment (migrating everything).

---

## Implementation

These patterns show HOW the pieces work together. To implement for your setup:

**Ask Claude Code to help:**

- "Set up a RAG collection for my vault" (Pattern 1)
- "Index my Gamestorming notes as a queryable collection" (Pattern 2)
- "Create my CLAUDE.md with these preferences" (Pattern 3)
- "Build a skill for querying past client work" (Pattern 6)

**Start with one pattern:**

- Pick the pattern that solves your most pressing problem
- Implement with Claude Code
- Use it for a week
- Add another pattern when needed

**Don't implement everything:**

- Not everyone needs Pattern 2 (Methodology as Code)
- Not everyone needs Pattern 6 (Semantic Project Memory)
- Start simple, add complexity only when you feel the pain

---

## Learn More

**Core Documentation:**

- [Getting Started](01-getting-started.md) - Which level do you need?
- [PARA Organization](02-para-organization.md) - Why folder structure matters
- [Frontmatter Schema](03-frontmatter-schema.md) - Metadata for machine-readable context
- [Claude Configuration](04-claude-configuration.md) - Creating your operating manual
- [Custom Skills](05-custom-skills.md) - Building slash commands for workflows
- [RAG Infrastructure](06-rag-infrastructure.md) - Setting up semantic search

**External Resources:**

- [Claude Code](https://docs.anthropic.com/claude/docs/claude-code) - Official documentation
- [Claude Code Skills](https://docs.anthropic.com/claude/docs/claude-code-skills) - Building custom commands
- [CLAUDE.md Guide](https://docs.anthropic.com/claude/docs/claude-code-configuration) - Configuration patterns
- [Gamestorming](https://gamestorming.com/) - Facilitation methodologies
- [Thinking Tools](https://untools.co/) - Mental models and frameworks

---

**Remember:** This document shows PATTERNS, not instructions. Use Claude Code to implement these patterns for your specific context. Your implementation will be unique to your work, your vault structure, and your needs.
