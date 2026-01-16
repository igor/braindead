# Project Workspace Setup

Create a project-specific `.claude/CLAUDE.md` file for Brain Dead vault projects.

## Invocation

User says: `/project-workspace` or mentions setting up a project workspace

## What This Skill Does

Creates a standardized `.claude/CLAUDE.md` file for project-specific workspaces within the Brain Dead vault. The file:
- References back to Brain Dead vault rules (frontmatter, PARA, RAG)
- Includes project-specific context (client, deliverables, timeline)
- Enables focused workspace while maintaining vault methodology

## Detection & Intelligence

1. **Check current directory** - Verify we're within Brain Dead vault
2. **Infer project name** - Extract from path (e.g., `1. Projects/ClientX/` → "ClientX")
3. **Ask minimal questions**:
   - Client name (if not obvious from path)
   - Brief project scope
   - Active deliverables (optional)
   - Timeline (optional)

## Template Structure

The created `.claude/CLAUDE.md` follows this pattern:

```markdown
# Project: [Client Name] - [Project Name]

## Project Context

- **Client**: [Name]
- **Scope**: [Brief description]
- **Active deliverables**: [List if provided]
- **Timeline**: [If provided]

## Vault Context

**This project is part of Brain Dead vault** - all Brain Dead vault rules apply:

- **PARA organization**: Files created here belong to the vault structure
- **Frontmatter requirements**: See `brain dead/.claude/agent_docs/yaml-frontmatter.md` for mandatory fields
- **RAG collections available**: Full vault searchable (braindead, readwise, 2025_portfolio, whatsapp)
- **Client archiving**: When complete, archive to `4. Archive/Clients/[Year]/[Client]/`

## Quick Links

- Vault root: `brain dead/`
- Project files: [inferred path]
- RAG operations: See global CLAUDE.md

## Notes

[Space for project-specific notes, constraints, or reminders]
```

## Workflow

1. User invokes skill
2. Detect current working directory
3. Verify it's within `brain dead/` structure
4. If in `1. Projects/[Name]/`, infer project name from path
5. Ask questions using AskUserQuestion:
   - Client name (prefill with inferred name if available)
   - Brief project scope
   - Optionally: deliverables, timeline
6. Create `.claude/` directory if needed (`mkdir -p`)
7. Write `.claude/CLAUDE.md` with filled template
8. Confirm creation with path

## Error Handling

- If not in Brain Dead vault → warn user this skill is for Brain Dead projects only
- If `.claude/CLAUDE.md` already exists → ask if they want to overwrite or append
- If path detection fails → ask user for project path explicitly

## Example Invocation

```
User: /project-workspace
Claude: I see you're in `brain dead/1. Projects/Vorwerk_AIStrategy/`. I'll help set up a project workspace CLAUDE.md.

[Asks questions about client, scope]

Created: `brain dead/1. Projects/Vorwerk_AIStrategy/.claude/CLAUDE.md`

You can now open this folder as a workspace in VS Code for focused work while maintaining access to Brain Dead's full vault via RAG.
```

## Integration with Multi-Workspace Strategy

This skill implements the multi-workspace pattern by:
- Keeping global methodology in global CLAUDE.md (inherited)
- Keeping vault rules in vault CLAUDE.md (referenced)
- Creating focused project context in project CLAUDE.md (minimal)

Result: Focused context window, full vault access, consistent methodology.
