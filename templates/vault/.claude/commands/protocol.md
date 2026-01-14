---
description: Create or update a daily protocol from the current session
---

# Protocol Command

Create a protocol entry for this session. Follow these steps:

## 1. Determine Current Time

Run `date +"%H:%M"` to get the exact current time. Save this value for the session timestamp.

## 2. Protocol Path

Use the following path for all protocols:
`{VAULT_PATH}/protocols/`

## 3. Check for Existing File

Check if a file `{VAULT_PATH}/protocols/YYYY-MM-DD.md` (today's date) exists.

### If the file DOES NOT exist:

Create it with this format:

```markdown
---
type: protocol
project: [Name of Working Directory]
date: [YYYY-MM-DD]
tags:
  - protocol
  - claude-session
---

# Protocol: [YYYY-MM-DD]

## Session 1 ([HH:MM])

**Context**: [Brief description of what this session was about]

**Completed**:
- [Concrete points of what was done]

**Decisions**:
- [If relevant decisions were made, otherwise omit this section]
```

### If the file ALREADY exists:

Read the file and add a new session at the end. Count existing sessions and number the new one accordingly (Session 2, Session 3, etc.):

```markdown

---

## Session [N] ([HH:MM])

**Context**: [Brief description of what this session was about]

**Completed**:
- [Concrete points of what was done]

**Decisions**:
- [If relevant decisions were made, otherwise omit this section]
```

## 4. Content

Summarize the current conversation:

- **Context**: What was the topic/goal of the session? (1-2 sentences)
- **Completed**: What was concretely done? (Bullet points)
- **Decisions**: What decisions were made? (only if relevant, otherwise omit this section)

## Important Rules

- Write in the language of the current conversation
- Keep it concise - pure documentation, no interpretation
- No open questions or next steps
- Use the time determined in Step 1 for the session timestamp
