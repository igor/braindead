---
description: Create a new project structure in the vault
---

# New Project Command

Create a new project structure in `{VAULT_PATH}/1. Projects/`.

## Steps to Follow

### 1. Gather Project Information

Ask the user for the following information:

- **Project name**: {PROJECT_NAME}
- **Project type**: {client/internal}
- **Client name** (if applicable): {CLIENT_NAME}
- **Project description** (brief): {DESCRIPTION}

### 2. Create Project Directory

Create the project directory with this naming convention:

- **Client projects**: `Client_{CLIENT_NAME}_{PROJECT_NAME}`
- **Internal projects**: `Internal_{PROJECT_NAME}`

Example: `Client_Acme_BrandStrategy` or `Internal_ProcessDocumentation`

### 3. Create Folder Structure

Create the following folders within the project directory:

```
{PROJECT_NAME}/
├── 00_Project_Management/
│   ├── README.md
│   ├── INSTRUCTIONS.md
│   └── .claude/  (optional, if project-specific config needed)
├── 01_Strategic_Foundation/
│   ├── Stakeholder_Presentations/
│   ├── Client_Materials/
│   └── Project_Context/
├── 02_Research_Frameworks/
│   └── Interview_Materials/
├── 03_Research_Insights/
│   └── _Research_Insights_Index.md
├── 04_Strategy_and_Planning/
├── 05_Data_and_Exports/  (optional)
├── 06_Transcripts/  (optional)
├── 07_RFP/  (optional)
└── ARCHIVE/
```

### 4. Create README.md

Copy the README.md template from `{VAULT_PATH}/.claude/templates/_PROJECT_TEMPLATE/00_Project_Management/README.md.template`

Replace all placeholders:
- `{PROJECT_NAME}`
- `{PROJECT_TITLE}`
- `{SCOPE}` (client or internal)
- `{PROJECT_OBJECTIVE}`
- etc.

### 5. Create INSTRUCTIONS.md

Copy the INSTRUCTIONS.md template from `{VAULT_PATH}/.claude/templates/_PROJECT_TEMPLATE/00_Project_Management/INSTRUCTIONS.md.template`

Replace all placeholders with project-specific information.

### 6. Create Research Insights Index

Copy the Research Insights Index template from `{VAULT_PATH}/.claude/templates/_PROJECT_TEMPLATE/03_Research_Insights/_Research_Insights_Index.md.template`

Customize for the project's specific research needs.

### 7. Confirm Creation

Display to the user:
- Path to the new project
- List of folders created
- Next steps (e.g., "Start by filling out INSTRUCTIONS.md")

## Important Guidelines

- Always use the proper naming convention (Client_/Internal_ prefix)
- Include YAML frontmatter in all markdown files
- Ask user for `type` and `scope` for frontmatter
- Set created and updated dates to today
- Infer project field from folder path
