# Custom Skills

Claude Code skills (also called slash commands) are markdown files that define custom workflows. They turn repeated multi-step procedures into single commands.

**Learn about Claude Code skills:** [Claude Code Skills Documentation](https://docs.anthropic.com/claude/docs/claude-code-skills)

**This document:** Shows PATTERNS for skills in this architecture, not a complete skills tutorial.

## Why Custom Skills

**Instead of this:**
```
You: "Create a diary entry. First find the session transcript, then parse it,
     then extract key decisions, then format it as markdown, then save it to
     ~/.claude/memory/diary/ with today's date..."
```

**Do this:**
```
You: "/diary"
Claude: [Executes the entire workflow automatically]
```

**Benefits:**
- **Consistency**: Same procedure every time
- **Efficiency**: One command instead of explaining steps
- **Documentation**: The skill file IS the procedure
- **Evolution**: Refine the workflow in one place

---

## Real Example: Gamestorming

I had the [Gamestorming](https://gamestorming.com/) book (facilitation methods by Dave Gray, Sunni Brown, and James Macanufo) I'd used for years—trusted methodologies I knew well. Instead of flipping through pages during client planning, I made it queryable.

###Step 1: Structured the Content

Created markdown files for each method:

```
3. Resources/Methodologies/Facilitation/Gamestorming/
├── Problem-Solving/
│   ├── Fishbone_Diagram.md
│   ├── Five_Whys.md
│   └── SWOT_Analysis.md
├── Team-Building/
│   ├── Empathy_Map.md
│   └── Affinity_Map.md
└── Planning/
    ├── Backcasting.md
    └── Pre-Mortem.md
```

Each method documented:
- Purpose
- When to use it
- Materials needed
- Steps
- Example usage

### Step 2: Indexed as RAG Collection

Added to `rag-collections.yaml`:

```yaml
gamestorming:
  description: "Facilitation methodologies and workshop games"
  purpose: "Plan client engagements and workshops"
  source_paths:
    - "3. Resources/Methodologies/Facilitation/Gamestorming"
  slash_command: /gamestorming
  chunks: 939
  files: 128
  n_results_recommended: 10-15
```

Ran indexing:

```bash
cd ~/rag-system && source venv/bin/activate && python scripts/ingest_gamestorming.py
```

*Note: If using remote RAG server, replace with `ssh {RAG_HOST} 'cd {RAG_PATH} && source venv/bin/activate && python scripts/ingest_gamestorming.py'`*

### Step 3: Created Slash Command

**File:** `.claude/commands/gamestorming.md`

```markdown
---
description: Query the Gamestorming facilitation methodologies collection
---

# Gamestorming Query Command

Search the Gamestorming collection for relevant facilitation methods.

## Query Instructions

1. Understand what the user is trying to facilitate or accomplish
2. Query the gamestorming collection with 10-15 results
3. Present relevant methods with context

## Example Queries

- "method for prioritizing features with stakeholders"
- "team-building exercise for remote teams"
- "technique to generate creative solutions"
```

### Step 4: Use It

**Now instead of:**
"Let me find my Gamestorming book... flip to the index... find prioritization methods... read through options..."

**I do:**
```
You: "/gamestorming what's a good prioritization method for distributed teams?"

Claude: Found 12 relevant methods from Gamestorming:

1. **Dot Voting** - Quick consensus on priorities
   - Works well remotely with digital tools
   - 15-30 minutes
   - Best for: Feature prioritization, backlog grooming

2. **Impact/Effort Matrix** - Visualize effort vs value
   - Easy to do in Miro/Mural
   - 30-45 minutes
   - Best for: Strategic decisions, roadmap planning

[etc.]

Based on your distributed team context, I'd recommend Impact/Effort Matrix
if you want structured analysis, or Dot Voting for quick consensus.
```

**What changed:**
- Book content is now conversational
- Methods surface based on context, not page order
- Can ask follow-up questions
- Combines multiple methods intelligently

**Same pattern works for:**

- [Thinking Tools](https://untools.co/) (mental models and frameworks by Adam Amran)
- Your proprietary frameworks
- Industry research
- Client methodologies
- Any trusted reference material

---

## Skill Types

### Session Logging Skills

**Purpose:** Document work as you go

**Examples:**
- `/diary` - Create structured session diary
- `/protocol` - Add entry to daily protocol

**Pattern:**
1. Review conversation context
2. Extract key information
3. Format consistently
4. Save to designated location

**Template:** [commands/diary.md](../templates/vault/.claude/commands/diary.md)

### Project Management Skills

**Purpose:** Automate project setup and archiving

**Examples:**
- `/new-project` - Create project structure from template
- `/archive-project` - Move completed project to archive

**Pattern:**
1. Gather information (ask user)
2. Create folders and files
3. Apply templates with placeholders replaced
4. Confirm completion

**Template:** [commands/new-project.md](../templates/vault/.claude/commands/new-project.md)

### RAG Query Skills

**Purpose:** Search specific collections conversationally

**Examples:**
- `/gamestorming` - Query facilitation methods
- `/thinking-tools` - Query mental models
- `/braindead` - Query main vault
- `/portfolio` - Query client work
- `/readwise` - Query book highlights

**Pattern:**
1. Understand user's need
2. Query appropriate collection
3. Present results with context
4. Synthesize if needed

**Template:** [commands/rag-query.md.template](../templates/vault/.claude/commands/rag-query.md.template)

### Workflow Automation Skills

**Purpose:** Complex multi-step procedures

**Examples:**
- `/coherence` - Analyze patterns across diary entries
- `/weekly-summary` - Generate weekly summary from protocols
- `/move` - Move files with frontmatter updates

**Pattern:**
1. Define clear steps
2. Include error handling
3. Confirm destructive actions
4. Document completion

---

## Creating Your First Skill

### Step 1: Identify a Repeated Workflow

**Look for:**
- Tasks you explain the same way multiple times
- Multi-step procedures you do weekly
- Queries you run frequently
- Workflows that follow a template

**Good candidates:**
- "Create a new project structure"
- "Archive this project to the right folder"
- "Find me examples of X from past work"
- "Generate a summary of this week's work"

### Step 2: Document the Procedure

Create a markdown file in `.claude/commands/`:

**File:** `.claude/commands/your-skill.md`

```markdown
---
description: Brief description of what this skill does
---

# Your Skill Name

What this skill accomplishes.

## Steps to Follow

### 1. First Step

What to do and why.

### 2. Second Step

What to do and why.

### 3. Final Step

What to do and why.

## Important Guidelines

- Key principle 1
- Key principle 2
- Edge case handling
```

### Step 3: Test It

Run the skill:
```
You: "/your-skill"
```

Watch Claude execute the workflow. Refine the instructions based on what goes wrong.

### Step 4: Evolve It

As you use the skill, notice:
- Steps that are unclear
- Edge cases not handled
- Improvements to output format

**Update the skill file.** Next time, Claude uses the improved version.

---

## Skill Best Practices

### Be Specific

**Bad:**
```markdown
1. Find the file
2. Update it
3. Save it
```

**Good:**
```markdown
1. Locate the session transcript in ~/.claude/projects/{project-hash}/
2. Extract tool usage counts using: jq -r 'select(.message.content[]?.name)'
3. Save diary entry to ~/.claude/memory/diary/YYYY-MM-DD-session-N.md
```

### Handle Errors

**Include:**
- What to do if file not found
- How to recover from failed steps
- When to ask user for clarification

**Example:**
```markdown
### Error Handling

**If transcript not found:**
- Show the path you checked
- List available transcripts
- Ask user which to use
```

### Confirm Completion

**Always end with:**
- What was accomplished
- Where files were saved
- Any follow-up actions needed

**Example:**
```markdown
### Confirm Completion

Display:
- "Saved diary entry to: {path}"
- "Captured {N} design decisions and {M} user preferences"
- "Next: Review entry for pattern analysis"
```

### Keep It Focused

**One skill = One workflow**

Don't combine unrelated tasks. Better to have:
- `/new-project`
- `/archive-project`

Than:
- `/project-management` (does too much)

---

## Skill Composition

Skills can call other skills:

**Example: Weekly Summary Skill**

```markdown
## Steps

1. Run `/protocol` to ensure today's protocol is captured
2. Read all protocols from this week
3. Extract key accomplishments
4. Format as weekly summary
5. Save to `summaries/YYYY-WW.md`
```

This creates higher-level workflows from basic building blocks.

---

## Skill Discovery

### Built-in Skills

Claude Code has built-in skills. List them:
```
/help
```

### Your Custom Skills

Your custom skills appear alongside built-in ones when you type `/`.

**Naming convention:**
- Use lowercase with hyphens
- Be descriptive but concise
- Avoid conflicts with built-in skills

**Examples:**
- `/new-project` (not `/np` - unclear)
- `/archive-client` (not `/archive` - too broad)
- `/gamestorming` (not `/g` - unclear)

---

## Advanced: Skills with Parameters

Some skills can accept parameters:

```markdown
# Archive Project Skill

Accepts optional parameter: project name

## Usage

- `/archive-project` - Archive current project
- `/archive-project ClientName` - Archive specific project

## Steps

1. Determine project name:
   - If parameter provided, use it
   - Otherwise, infer from current directory
2. [rest of workflow]
```

---

## Examples from This Repository

### Session Logging

- [diary.md](../templates/vault/.claude/commands/diary.md) - Create session diary
- [protocol.md](../templates/vault/.claude/commands/protocol.md) - Daily protocol entry

### Project Management

- [new-project.md](../templates/vault/.claude/commands/new-project.md) - Create project structure

### RAG Queries

- [rag-query.md.template](../templates/vault/.claude/commands/rag-query.md.template) - Collection query template

**Adapt these templates to your workflows.**

---

## Integration with RAG

Skills and RAG work together:

**Pattern:**
1. User asks vague question
2. Skill queries RAG collection
3. RAG returns relevant chunks
4. Skill synthesizes answer

**Example: /thinking-tools**

```
You: "/thinking-tools how do I think about complex systems?"

[Skill queries thinking_tools collection]
[RAG returns: Systems Thinking, Causal Loop Diagrams, Stock and Flow, etc.]
[Skill synthesizes these into coherent answer]

Claude: For complex systems, Thinking Tools offers several models:

1. **Causal Loop Diagrams** - Map feedback loops and delays
2. **Stock and Flow** - Understand accumulation and rates
3. **Iceberg Model** - Look below surface events

For your scenario, I'd start with Causal Loop Diagrams to map
the relationships, then use Stock and Flow to understand dynamics.
```

**Without the skill:** You'd need to manually query RAG, read chunks, synthesize.

**With the skill:** One command, conversational answer.

---

## Maintenance

### Weekly

- Use your skills - they improve with feedback
- Note any unclear steps or errors
- Update skill files immediately

### Monthly

- Review all skills for relevance
- Archive unused skills
- Consolidate similar skills
- Document new patterns as skills

### Quarterly

- Major skill refactoring if needed
- Update templates based on learned patterns
- Share useful skills with team (if applicable)

---

## Next Steps

1. Identify one repeated workflow
2. Document it as a skill file
3. Test and refine
4. Create 2-3 more skills for common tasks
5. Set up [RAG infrastructure](06-rag-infrastructure.md) to enable query skills (optional)
6. Explore [workflow patterns](07-workflows.md) for inspiration

---

**Templates:**
- [diary.md](../templates/vault/.claude/commands/diary.md)
- [protocol.md](../templates/vault/.claude/commands/protocol.md)
- [new-project.md](../templates/vault/.claude/commands/new-project.md)
- [rag-query.md.template](../templates/vault/.claude/commands/rag-query.md.template)

**Learn more:** [Claude Code Skills Documentation](https://docs.anthropic.com/claude/docs/claude-code-skills)
