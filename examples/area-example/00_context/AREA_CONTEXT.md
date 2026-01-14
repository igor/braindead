---
type: reference
scope: internal
area: Operations
created: 2025-12-11
updated: 2026-01-14
---

# Operations

Governance infrastructure for studio operations. Defines conventions, schemas, and structures that govern how work is organized and documented.

---

## Purpose

Operations is the "operating system" for the practice - the definitional layer that determines:

- **How projects and areas are structured** (folder conventions)
- **How documents are classified** (YAML frontmatter schema)
- **How metadata flows to tools** (RAG indexing, search, automation)
- **How workflows are documented** (process definitions)

---

## What belongs here

| Content | Type | Example |
|---------|------|---------|
| Folder structure conventions | reference | Project Structure Manual |
| Metadata schemas | reference | YAML Frontmatter Schema |
| Workflow definitions | operations | Client Onboarding Process |
| Tool configurations | reference | RAG Setup Documentation |
| Process documentation | operations | Project Kickoff Checklist |

---

## What doesn't belong here

- **Implementation code** → stays in relevant projects (e.g., RAG System project)
- **Client work** → stays in `1. Projects/`
- **Company administration** → stays in Company area (HR, finance, etc.)
- **Personal development** → stays in Professional Development area

---

## Structure

```
Operations/
├── 00_context/
│   └── AREA_CONTEXT.md (this file)
├── Folder_Conventions/
│   ├── Project_Structure_Manual.md
│   └── PARA_Implementation.md
├── Metadata_Schemas/
│   ├── YAML_Frontmatter_Schema.md
│   └── Tagging_Strategy.md
├── Workflows/
│   ├── Project_Kickoff_Workflow.md
│   ├── Client_Onboarding_Process.md
│   └── Research_Documentation_Standards.md
└── Tool_Documentation/
    ├── RAG_System_Setup.md
    ├── Claude_Code_Configuration.md
    └── Obsidian_Setup.md
```

---

## Related

- [[YAML Frontmatter Schema]] - Metadata conventions
- [[Project Structure Manual]] - Folder structure conventions
- `1. Projects/Internal_RAG_System/` - Technical implementation
- `2. Areas/Professional_Development/` - Learning and skill development

---

## Maintenance

- Review quarterly for relevance
- Update when conventions change
- Document new patterns as they emerge
- Archive deprecated approaches to ARCHIVE folder
