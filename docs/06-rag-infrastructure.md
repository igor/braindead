# RAG Infrastructure

RAG (Retrieval Augmented Generation) enables semantic search across your knowledge base. This document explains the PATTERN - use Claude Code to IMPLEMENT it for your specific setup.

**Important:** This is an optional advanced layer (Level 4). You don't need RAG for Levels 1-3 to be valuable.

**This repository shows architecture, not implementation.** Think Christopher Alexander's "A Pattern Language" - patterns you adapt, not code you copy-paste.

## What is RAG?

### Keyword Search vs. Semantic Search

**Keyword search:**
- Query: "prioritization methods"
- Returns: Files containing exactly "prioritization" and "methods"
- Misses: Files about "ranking techniques", "deciding what matters first"

**Semantic search (RAG):**
- Query: "prioritization methods"
- Returns: Files about prioritization, ranking, decision-making, triage, etc.
- Finds: Concepts related to the query, not just exact words

### How It Works (Conceptually)

1. **Indexing:** Documents split into chunks and converted to embeddings (numerical representations of meaning)
2. **Storage:** Embeddings stored in vector database (ChromaDB)
3. **Query:** Your question converted to an embedding
4. **Retrieval:** Most similar chunks found using vector similarity
5. **Generation:** Claude uses retrieved chunks as context to answer

---

## Architecture Overview

### Components

**1. Embedding Model** (Ollama)
- Converts text to vectors
- Recommended: nomic-embed-text
- Runs locally (no API calls for embeddings)
- Alternative: OpenAI embeddings (API-based, higher quality, costs money)

**2. Vector Database** (ChromaDB)
- Stores document embeddings
- Fast similarity search
- Metadata filtering
- Alternative: Qdrant, Pinecone, Weaviate (for production/scale)

**3. MCP Server**
- Bridge between Claude Code and ChromaDB
- Handles queries and indexing
- Included with Claude Code

**4. Ingest Scripts**
- Python scripts to index documents
- One per collection (vault, methodologies, specific projects)
- You'll create these with Claude Code

### Infrastructure Patterns

**Pattern 1: Localhost** (recommended)

```
Your Computer
    ├── Ollama (embeddings)
    ├── ChromaDB (vector storage)
    ├── MCP Server
    └── Claude Code (queries)
```

**Pros:**

- Simplest setup, no networking complexity
- Works on single machine (laptop, desktop, workstation)
- No SSH configuration needed
- Fastest response times (no network latency)
- Easiest to debug

**Cons:**

- Embeddings consume local resources during indexing
- Large collections (50k+ chunks) may impact performance

**Best for:** Most users, getting started, collections under 50k chunks

---

**Pattern 2: Remote Server** (optional advanced)

```
Local Computer              Remote Server
    ├── Vault                   ├── Ollama (embeddings)
    └── Claude Code             ├── ChromaDB (storage)
                                └── MCP Server
                                    ↑
                                   SSH
```

**Pros:**

- Offloads resource-intensive embedding generation
- Better for very large collections (50k+ chunks)
- Can serve multiple computers/users
- Keeps local machine responsive during indexing

**Cons:**

- Requires network configuration
- SSH setup complexity
- Network latency on queries
- More complex troubleshooting

**Example infrastructure:** Dedicated server, old laptop, Mac Mini, cloud instance, NAS

**Best for:** Power users, very large collections, multi-user scenarios

---

### Which Pattern Should I Use?

**Start with Pattern 1 (Localhost)** unless you have:

- Very large collections (50k+ chunks)
- Multiple users querying the same collections
- A spare computer/server already available
- Performance issues with local setup (test first)

**Most users will never need Pattern 2.** The localhost setup handles tens of thousands of documents efficiently on modern hardware.

**Pro tip:** You can always migrate from Pattern 1 to Pattern 2 later. Start simple.

---

## Implementation Approach

**This repository documents PATTERNS. Claude Code helps you IMPLEMENT them.**

Don't copy-paste code from docs - your setup is unique. Instead:

1. **Understand the concepts** (read this document)
2. **Make architectural decisions** (see below)
3. **Ask Claude Code to implement** for YOUR specific setup
4. **Iterate** based on your needs

Claude Code knows these patterns and will implement them for your context.

---

## Architectural Decisions You'll Make

### Decision 1: Collection Strategy

**One big collection:**
- ✅ Simple to maintain, cross-domain connections
- ❌ Slower queries, less precise results

**Multiple collections:**
- ✅ Faster, more precise queries, tune n_results per collection
- ❌ More infrastructure, user must pick collection

**Hybrid approach** (recommended):
- Main vault collection (everything)
- Specialized collections (methodologies, specific projects)
- Query main for broad search, specialized for focused needs

**Recommendation:** Start with 2-3 collections. Add more only if needed.

### Decision 2: Chunking Strategy

How you split documents affects retrieval quality:

**Semantic Chunking** (recommended)
- Split by markdown headers (`##`, `###`)
- Pros: Respects document structure, meaningful units
- Cons: Variable chunk sizes

**Fixed-Size Chunking**
- Split every N characters with overlap
- Pros: Predictable sizes, works for any format
- Cons: Can split mid-sentence, ignores structure

**Hybrid Approach** (best)
- Split by headers, but subdivide large sections (>1500 chars)
- Pros: Semantic boundaries + size control
- Cons: More complex logic

**Recommendation:** Start with semantic chunking. Move to hybrid if needed.

### Decision 3: Metadata Strategy

What frontmatter fields to extract:

**Minimal:**
- `type`, `created`, `source` (file path)

**Recommended:**
- Above + `project`, `area`, `tags`
- Enables filtering: "Find deliverables from Project X"

**Advanced:**
- Above + `scope`, `status`, `client`
- Enables complex queries with metadata filters

**Recommendation:** Start minimal, add fields as you discover filtering needs.

### Decision 4: When to Re-Index

**Definitely re-index:**
- After adding 100+ new files
- After major reorganization
- Monthly maintenance

**Maybe re-index:**
- Weekly for active vaults
- Before important research
- When results feel stale

**Don't over-index:** Not after every file change. First index takes time, re-indexing is faster (only changed files).

---

## Using Claude Code for Implementation

### Initial Setup

**Example Prompt:**

```
I want to set up a RAG system to semantically search my markdown vault. Here's my setup:

- Vault location: ~/Documents/my-vault
- 3,500+ markdown files organized with PARA
- YAML frontmatter with: type, created, project, area, tags
- MacBook Pro (want localhost setup)
- Never set up RAG before

Help me:
1. Install Ollama and nomic-embed-text embedding model
2. Set up Python environment with ChromaDB
3. Create the collection registry at .claude/agent_docs/rag-collections.yaml
4. Write an ingest script using semantic chunking by markdown headers
5. Configure the MCP server

Use the patterns from the braindead repository.
```

**What Claude Code will do:**
- Check prerequisites (Python version, disk space)
- Guide installation steps (Ollama, dependencies)
- Create all necessary files and folders
- Write implementation code for YOUR setup
- Handle edge cases (large files, missing frontmatter, errors)
- Test the setup and verify it works

### Creating Ingest Scripts

**What an ingest script needs to do:**

1. **Connect to ChromaDB** - Initialize persistent client
2. **Traverse your vault** - Find all markdown files to index
3. **Extract metadata** - Parse YAML frontmatter for context
4. **Chunk content** - Split documents using your chosen strategy
5. **Generate embeddings** - Use Ollama to embed each chunk
6. **Store in collection** - Add chunks with metadata to ChromaDB

**Example Prompt:**

```
Create an ingest script for my vault at ~/Documents/my-vault.

Requirements:
- Collection name: "my_vault"
- Chunking: Semantic by markdown headers, but split sections >1500 chars
- Metadata: Extract type, created, project, area from YAML frontmatter
- Embedding model: nomic-embed-text via Ollama
- Error handling: Skip files with parsing errors, log them
- Progress: Show progress bar for large vaults
- Resumability: Track indexed files, skip unchanged on re-index

Save to: ~/rag-system/scripts/ingest_vault.py
```

**What Claude Code will do:**
- Write the full Python script
- Handle edge cases (malformed YAML, binary files, symlinks)
- Add logging and progress tracking
- Make it resumable for large vaults
- Test it on a few files first

### Creating Query Skills

**Example Prompt:**

```
Create a /vault query skill that:
- Searches the "my_vault" collection
- Uses 15-20 results for exploratory queries
- Presents results with source file path and frontmatter metadata
- Groups results by project/area if relevant
- Formats output clearly

Save to: .claude/commands/vault.md
```

### Troubleshooting

When something doesn't work:

**Example Prompt:**

```
I'm getting this error when running my ingest script:

[paste error message]

The script is at ~/rag-system/scripts/ingest_vault.py
It happens when processing files in the "Projects/ClientX/" folder
These files have embedded images and complex frontmatter

Help me debug and fix this.
```

**Claude Code will:**
- Read your actual implementation
- Identify the issue in context
- Fix it for your specific case
- Test the fix
- Explain what went wrong

**Don't ask the repository maintainer for implementation support.** Your setup is unique. Use Claude Code to debug YOUR specific implementation.

---

## Collection Registry Pattern

**File:** `.claude/agent_docs/rag-collections.yaml`

**Purpose:** Single source of truth for all collections. Claude consults this before any RAG operation.

**Pattern:**
- Lists all collections with metadata
- Documents infrastructure details (host, port, embedding model)
- Tracks chunk counts, last indexed date, recommended n_results
- References ingest scripts and slash commands

**Why this matters:** Prevents Claude from querying non-existent collections, provides context for tuning queries.

---

## Query Patterns

### n_results Tuning

**Exploratory queries:** 15-20 results
- "What do I know about X?"
- "Overview of topic Y"
- Broad research questions

**Focused queries:** 5-10 results
- "Specific technique for problem Z"
- "Definition of term A"
- Precise lookups

**Dense collections:** Lower n_results (3-5)
- Project-specific collections
- Highly focused domains

### Query Crafting

**Be specific:**
- ❌ "facilitation"
- ✅ "facilitation methods for remote team prioritization sessions"

**Include context:**
- ❌ "framework"
- ✅ "brand positioning framework for luxury market"

**Use natural language:**
- ❌ "SWOT analysis docs"
- ✅ "how do I use SWOT analysis for strategic planning?"

### Multi-Query Strategies

For complex questions, query multiple times:

**Example:**
```
User asks: "How should I approach brand strategy for a sustainability-focused startup?"

Claude:
1. Query: "brand strategy frameworks" (15 results)
2. Query: "sustainability positioning" (15 results)
3. Query: "startup brand building" (10 results)
4. Synthesize across all results
```

**Parallel queries = richer context**

---

## Advanced Topics

### Metadata Filtering

Use frontmatter as query filters:

**Pattern:**
- Query with `where` clause: `{"type": "deliverable", "scope": "client"}`
- Enables: "Find all client deliverables from 2025"
- Enables: "Show internal research only"
- Enables: "Filter by project or area"

**Implementation:** Ask Claude Code to add filtering to your query skill.

### Custom Embedding Models

**Alternatives to nomic-embed-text:**
- `all-MiniLM-L6-v2` - Faster, smaller
- `mxbai-embed-large` - More accurate, slower
- OpenAI embeddings - Best quality, requires API, costs money

**Trade-offs:**
- Speed vs. accuracy
- Local vs. API
- Model size vs. precision

**Recommendation:** Start with nomic-embed-text. Switch only if you have specific quality needs.

### Remote Server Pattern

**When to use:**
- Very large collections (50k+ chunks)
- Multiple users querying same collections
- You have a spare server/old laptop available

**When not to use:**
- Starting out (use localhost first)
- Collections under 50k chunks
- Single user

---

## Costs and Performance

### Local RAG (Ollama)

**Costs:**
- $0 per query (no API calls)
- Compute: CPU/GPU for embeddings
- Storage: ~1-5 GB per 10K documents

**Performance:**
- Initial indexing: 10-30 min for 10K docs (depends on hardware)
- Queries: <1 second
- Re-indexing: Incremental (only changed files)

### API-Based Embeddings (OpenAI)

**Costs:**
- ~$0.0001 per 1K tokens
- For 10K docs (5M tokens): ~$0.50 to index
- Queries: $0.0001 per query

**Performance:**
- Faster than local for large batches
- Network dependency
- Higher quality embeddings

**Recommendation:** Start with local (free). Move to API only if you need better quality.

---

## Common Issues & Patterns

### "Collection not found"

**Pattern:**
- Collection name in query must match `rag-collections.yaml`
- Collections are case-sensitive
- Check collection was actually indexed (check logs)

### "Poor result quality"

**Pattern:**
- Increase n_results (more context)
- Refine query (be more specific)
- Check chunking strategy (too large/small?)
- Consider different embedding model

### "Slow queries"

**Pattern:**
- Reduce n_results if too high
- Add metadata filters to narrow search
- Split into multiple smaller collections
- Check server resources (CPU/RAM)

### "Stale results"

**Pattern:**
- Re-index the collection
- Update `last_indexed` in `rag-collections.yaml`
- Set up regular maintenance schedule (monthly)

---

## Next Steps

1. **Assess if you need RAG**
   - Do you have 1000+ files?
   - Do you do complex cross-domain queries?
   - Is keyword search not finding what you need?

2. **Start with localhost setup**
   - Ask Claude Code to help you set up (see "Using Claude Code" section above)
   - Index your main vault collection
   - Create one query skill

3. **Use it for a week**
   - See how it performs
   - Adjust n_results based on your needs
   - Add more collections only if needed

4. **Iterate**
   - Refine chunking strategy
   - Add metadata filtering
   - Explore [workflow patterns](07-workflows.md) that combine RAG + skills

---

## Resources

**External Documentation:**
- [ChromaDB documentation](https://docs.trychroma.com/) - Vector database
- [Ollama documentation](https://ollama.ai/docs) - Local embeddings
- [Custom Skills Guide](05-custom-skills.md) - Building slash commands

---

**Remember:** This document shows the ARCHITECTURE. Use Claude Code to IMPLEMENT it for your specific setup. Your implementation will be unique to your vault, your metadata, and your needs.
