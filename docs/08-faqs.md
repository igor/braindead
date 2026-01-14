# FAQs

Common questions about implementing Strategy-as-Protocol patterns.

---

## Getting Started

### Do I need all four layers?

**No.** Start with what solves your problem:

- **Level 1**: Document one process (no tools)
- **Level 2**: Add structure (any markdown tool)
- **Level 3**: Make it executable (Claude Code)
- **Level 4**: Add RAG (optional, for 1000+ files)

Most people start with Level 1 or 2. Many never need Level 4.

### Which level do I need?

**Ask yourself:**
- Need to make implicit knowledge explicit? → Start Level 1
- Drowning in unstructured notes? → Start Level 2
- Repeating instructions to AI? → Start Level 3
- Can't find connections across years of work? → Consider Level 4

### How long does this take?

**Progressive adoption:**
- Level 1: Hours to document one process
- Level 2: Days to establish PARA + frontmatter
- Level 3: Weeks to build operating manual
- Level 4: Weeks to months for RAG infrastructure

Don't do all at once. Add layers as you need them.

---

## Level 2: Structure Questions

### Do I need Obsidian?

**No.** Any markdown system works. Choose the tool you already use.

The patterns (PARA, frontmatter, operating manuals) work in any markdown environment.

### Do I need perfect frontmatter?

**No.** Add it progressively:
- New files from day one
- Old files when you edit them
- Migration happens over time

Imperfect but consistent > perfect but incomplete.

### What if I forget to archive?

**It's fine.** PARA is flexible. Archive when convenient, not on schedule.

### Should I version control my vault?

**Consider your needs:**
- **Pros**: History, collaboration, backup
- **Cons**: Complexity, merge conflicts, confidentiality concerns

Use what matches your workflow and risk tolerance.

---

## Level 3: Claude Code Questions

### Can I use this without Claude Code?

**Yes.** Levels 1-2 work with any markdown tool. The principles (structure, metadata, explicit protocols) are tool-agnostic.

Claude Code enables Levels 3-4 but isn't required for the underlying concepts.

### What about other AI tools?

**This system is designed for Claude Code's capabilities.** The principles of explicit protocols apply to any AI tool, but implementation will differ.

---

## Level 4: RAG Questions

### When should I re-index?

**Pattern, not prescription:**
- After significant content changes
- When results feel stale
- On a schedule that matches your workflow

Don't over-index. Find your rhythm.

---

## Adaptation Questions

### How do I adapt this to my domain?

**Don't replicate. Adapt the patterns:**

**Keep the principles:**
- Structure that creates semantic context
- Metadata that captures meaning
- Explicit protocols over implicit assumptions

**Adapt the specifics:**
- Your folder structure
- Your metadata fields
- Your workflows
- Your methodologies

See [examples](../examples/) for how consulting patterns translate to other domains.

### Is this only for consultants?

**No.** Useful for anyone with:
- Complex projects spanning time
- Extensive notes and research
- Need to reference past work
- Work involving AI tools

The professional context in examples is illustrative, not prescriptive.

### Can I use this with my team?

**Yes, with considerations:**

**Shared**: Structure, schema, templates, resources
**Individual**: Operating manuals, skills, personal workflows

Teams benefit from shared patterns, not identical implementations.

### How do I handle client confidentiality?

**Use the architecture:**
- Archive by year/client for isolation
- Use frontmatter `scope` field for filtering
- Exclude sensitive folders from RAG
- Sanitize when sharing

The structure enables privacy; you control the specifics.

---

## Philosophy Questions

### What if this seems like overkill?

**Start smaller.** You don't need the whole system. Pick one pattern that solves one problem.

The repository shows what's possible, not what's required.

### How do I know if this is working?

**Ask yourself:**
- Can you find what you need faster?
- Can AI follow your patterns without reminding?
- Are decisions more traceable?
- Is implicit knowledge becoming explicit?

Measure what matters to your work.

---

## Technical Questions

### What does this cost?

**Minimal to free:**
- Markdown files are free
- Many tools have free tiers
- Open-source options exist for all layers

Your time investment matters more than money.

### Does this work on my platform?

**The patterns work anywhere markdown works.** Implementation details vary by tool and platform.

### Is my data safe?

**You control the trade-offs:**
- Local tools = full control, more setup
- Cloud tools = convenience, less control
- Hybrid = balance both

Review terms for tools you choose. Consult legal counsel for compliance needs.

---

## Next Steps

- Start with [Getting Started Guide](01-getting-started.md)
- Review [Philosophy](00-philosophy.md) for the "why"
- Explore [Templates](../templates/) for structure
- Adapt patterns to your needs

---

**Remember:** This is architecture, not implementation. Patterns you adapt, not code you copy. There's no "right" way—only what works for you.
