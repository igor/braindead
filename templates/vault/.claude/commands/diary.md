---
description: Create a structured diary entry from the current session transcript
---

# Create Diary Entry from Current Session

You are going to create a structured diary entry that documents what happened in the current Claude Code session.

## Steps to Follow

### 1. Review the Session Context

Reflect on the conversation history loaded in this session. You have access to:

- All user messages and requests
- Your responses and tool invocations
- Files you read, edited, or wrote
- Errors encountered and solutions applied
- Design decisions discussed
- User preferences expressed

### 2. Create the Diary Entry

Based on the conversation context, create a structured markdown diary entry with these sections:

```markdown
# Session Diary Entry

**Date**: [YYYY-MM-DD]
**Time**: [HH:MM:SS]
**Session ID**: [uuid]
**Project**: [project path]

## Task Summary

[2-3 sentences: What was the user trying to accomplish?]

## Work Summary

[Bullet list of what was accomplished:]
- Features implemented
- Bugs fixed
- Documentation added
- Tests written

## Design Decisions Made

[Document key technical decisions and WHY they were made:]
- Architectural choices
- Technology selections
- API design decisions
- Pattern selections

## Actions Taken

[Based on tool usage and file operations:]
- Files edited: [list paths]
- Commands executed: [list commands]
- Tools used: [list tools]

## Challenges Encountered

[Based on errors and user corrections:]
- Errors encountered
- Failed approaches
- Debugging steps

## Solutions Applied

[How problems were resolved]

## User Preferences Observed

[Document preferences for future sessions]

### Commit & PR Preferences:
- [Patterns around commit messages, PR descriptions]

### Code Quality Preferences:
- [Testing requirements, linting preferences]

### Technical Preferences:
- [Libraries, patterns, frameworks preferred]

## Notes

[Any other observations]
```

### 3. Save the Diary Entry

Use the Write tool to save the entry to an appropriate location.

Suggested path: `~/.claude/memory/diary/YYYY-MM-DD-session-N.md`

### 4. Confirm Completion

Display:
- Path where diary was saved
- Brief summary of what was captured

## Important Guidelines

- **Be factual and specific**: Include concrete details (file paths, error messages)
- **Capture the 'why'**: Explain reasoning behind decisions
- **Document ALL user preferences**: Especially around commits, PRs, linting, testing
- **Include failures**: What didn't work is valuable learning
- **Keep it structured**: Follow the template consistently
