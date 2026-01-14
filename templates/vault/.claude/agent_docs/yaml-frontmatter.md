# YAML Frontmatter Rules for Vault

## Applicable Folders

When creating a new `.md` file in these folders (including subfolders):

- `1. Projects/`
- `2. Areas/`
- `3. Resources/`
- `4. Archive/`

**ALWAYS add YAML frontmatter.** Before writing the file, ASK the user for the `type` field.

## Required Fields

- `type`: **ASK USER** — options: research | concept | deliverable | reference | transcript | protocol | index | operations
- `scope`: **ASK USER** — options: client | internal
- `created`: Today's date (YYYY-MM-DD)
- `updated`: Today's date (YYYY-MM-DD)

## Contextual Fields (at least one required)

- `project`: Infer from folder path (for `1. Projects/` content)
- `area`: Infer from folder path (for `2. Areas/` content) — can coexist with project

## Optional Fields (ask when relevant)

- `deliverable_type`: For type=deliverable — framework | workshop | analysis | presentation | text | guide
- `language`: de | en
- `tags`: List of keywords

## Template

```yaml
---
type: [ask user]
scope: [ask user]
project: "[inferred from path]"  # for 1. Projects/
area: "[inferred from path]"     # for 2. Areas/ (can coexist with project)
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

## Folders Excluded from This Rule

- `Daily/`
- `protocols/`
- `0. Inbox/`
- Root-level files
- Any folder not listed above
