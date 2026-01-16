# RAG Advanced Patterns (Optional)

**Prerequisites:** [RAG Infrastructure](06-rag-infrastructure.md) set up and working

**This document:** Shows architectural patterns for production-grade RAG retrieval. Most users won't need this initially—start simple, evolve when you feel the pain.

---

## The Problem with Naive Semantic Search

When you first set up RAG, the simple approach works:

```
User Query → Embedding → Cosine Similarity → Top N Results
```

**This breaks down at scale:**

- Returns too many mediocre results (high recall, low precision)
- Treats all results equally (no recency, importance, or type weighting)
- Searches everything (no filtering by collection or metadata)
- Misses exact keyword matches (semantic similarity isn't perfect)
- Gets worse as you add more data (signal-to-noise ratio decreases)

**Example problem:**

Query: "Find AI strategy work for automotive clients"

**Naive search returns:**
- 50+ results mixing high and low relevance
- 3-year-old newsletter mentions
- Passing references in chat logs
- Documents with "AI" or "strategy" but wrong context
- No prioritization of recent client deliverables

**User must manually filter through noise.**

---

## Signals You're Ready to Upgrade

**Stick with naive search if:**
- You have <10k chunks
- You work in a single domain
- You don't mind browsing results
- Your system is still experimental

**Upgrade to production patterns when:**
- You have 50k+ chunks (or plan to add more data)
- You're frustrated by irrelevant results
- You search across multiple domains/collections
- You need to filter by date, client, project, or type
- Time-to-answer matters (can't browse 50+ results)
- Your system has moved from prototype to production

---

## Production RAG Architecture (4 Phases)

### Phase 1: Metadata Filtering & Hybrid Search

**Problem:** Can't filter by date, client, project; missing exact keyword matches

**Solution:** Expose metadata filtering + combine semantic and keyword search

**What it enables:**
- Filter by metadata: `date >= "2024-01-01"`, `client = "ACME"`, `type = "deliverable"`
- Keyword matching: Find documents containing exact terms (e.g., "superposition" in strategy docs)
- Combined search: Semantic similarity + keyword presence + metadata constraints

**Implementation pattern:**
```python
# Metadata filtering
results = collection.query(
    query_texts=["AI strategy"],
    where={
        "date": {"$gte": "2024-01-01"},  # Recent only
        "scope": "client",                 # Client work only
        "type": "deliverable"              # Deliverables only
    },
    n_results=10
)

# Hybrid: semantic + keyword + metadata
results = collection.query(
    query_texts=["brand positioning"],
    where_document={"$contains": "superposition"},  # Must mention this
    where={"client": "ACME"},                        # For this client
    n_results=10
)
```

**ChromaDB supports this natively** - just expose the parameters in your interface.

**Impact:** Immediate improvement. Users can narrow results to relevant subsets.

**Metadata design tip:** Store what you'll filter by:
- `date`, `created`, `updated` - Temporal filtering
- `client`, `project`, `area` - Organizational filtering
- `type`, `scope`, `deliverable_type` - Document classification
- `tags` - Thematic filtering (store as arrays for `$in` operator)

---

### Phase 2: Query Routing & Collection Selection

**Problem:** Searching all collections returns noise from irrelevant sources

**Solution:** Route queries to appropriate collection(s) based on intent

**What it enables:**
- Email queries → email collection only
- Message queries → messaging collections only
- Client work queries → portfolio collection only
- Ambiguous queries → smart collection selection

**Routing strategies:**

**Option A: Keyword-Based (Fast, Deterministic)**
```python
def route_query(query_text):
    if "email" in query_text.lower():
        return ["email"]
    if any(word in query_text.lower() for word in ["chat", "message"]):
        return ["messaging"]
    if "book" in query_text or "reading" in query_text:
        return ["readwise"]

    # Default: primary collections
    return ["vault", "portfolio"]
```

**Option B: LLM-Based (Smart, Flexible)**
- Use Claude/GPT to classify query intent
- Select collections based on understanding
- Handles ambiguous queries well
- Adds 100-200ms latency

**Option C: Hybrid (Recommended)**
- Keyword patterns for obvious cases (fast path)
- LLM for ambiguous queries (smart fallback)

**Benefits:**
- Reduces irrelevant results by 50-70%
- Faster searches (smaller result sets)
- Better precision without sacrificing recall

**Collection design tip:** Separate collections by domain:
- `vault` - Personal knowledge
- `portfolio` - Client work
- `email` - Email archives
- `messaging` - Chat/messages
- `readwise` - Reading highlights

Don't create too many collections (routing gets complex). 5-10 is manageable.

---

### Phase 3: Re-Ranking with Cross-Encoders

**Problem:** Initial retrieval uses fast but imprecise bi-encoder similarity

**Solution:** Two-stage retrieval: over-retrieve → re-rank → return best

**How it works:**

1. **Stage 1 (Fast, High Recall):** Retrieve 50-100 candidates
   - Uses existing semantic search + metadata filters
   - Goal: Capture all potentially relevant documents
   - Fast (~50ms) but less precise

2. **Stage 2 (Precise, High Precision):** Re-rank candidates
   - Cross-encoder scores each query-document pair
   - More expensive but more accurate
   - Only evaluates 50-100 items (manageable)
   - Goal: Surface most relevant results

3. **Return:** Top 10-15 after re-ranking

**Pattern:**
```python
def retrieve_and_rerank(query, filters, n_final=10):
    # Stage 1: Over-retrieve
    initial_results = collection.query(
        query_texts=[query],
        where=filters,
        n_results=50  # More than needed
    )

    # Stage 2: Re-rank with cross-encoder
    scores = cross_encoder.predict([
        (query, doc)
        for doc in initial_results['documents'][0]
    ])

    # Sort by cross-encoder score
    reranked = sorted(
        zip(initial_results['documents'][0], scores),
        key=lambda x: x[1],
        reverse=True
    )

    return reranked[:n_final]
```

**Cross-encoder options:**

**Local Models (No cost, 10ms latency):**
- `cross-encoder/ms-marco-MiniLM-L-6-v2` (general domain)
- `cross-encoder/ms-marco-MiniLM-L-12-v2` (better quality, slower)
- Run via Ollama or directly with sentence-transformers

**API Services ($, ~200ms latency):**
- Cohere Rerank API ($1 per 1k searches)
- Jina Rerank API (similar pricing)
- Higher quality, especially on domain-specific content

**Recommendation:** Start with local cross-encoder, upgrade to API if quality isn't sufficient.

**Metadata-aware re-ranking:**

Don't just use cross-encoder scores—combine with metadata:

```python
def metadata_aware_score(cross_encoder_score, metadata):
    """Combine relevance with recency/importance."""

    # Base score from cross-encoder
    score = cross_encoder_score

    # Recency boost (exponential decay)
    age_days = (now - metadata['date']).days
    recency_multiplier = 1.0 + (0.5 * exp(-age_days / 180))  # 50% boost for recent
    score *= recency_multiplier

    # Document type weighting
    type_weights = {
        "deliverable": 1.5,   # Prioritize deliverables
        "research": 1.2,      # Then research
        "note": 1.0,          # Standard notes
        "chat": 0.8           # Deprioritize chat logs
    }
    score *= type_weights.get(metadata['type'], 1.0)

    return score
```

**Impact:** Research shows 20-40% improvement in relevance metrics. Users find answers faster.

**Diversity filtering:**

Avoid returning 10 chunks from the same document:

```python
def ensure_diversity(ranked_results, max_per_doc=3):
    """Limit chunks from same document."""
    doc_counts = {}
    diverse = []

    for result in ranked_results:
        doc_id = result['metadata']['source']
        count = doc_counts.get(doc_id, 0)

        if count < max_per_doc:
            diverse.append(result)
            doc_counts[doc_id] = count + 1

    return diverse
```

---

### Phase 4: Multi-Stage Pipeline Integration

**Problem:** Managing all improvements separately is complex

**Solution:** Unified retrieval pipeline orchestrating all phases

**Pipeline flow:**

```
User Query
    ↓
1. Intent Classification & Collection Routing (Phase 2)
    ↓
2. Metadata Filter Construction (Phase 1)
    ↓
3. Hybrid Search (Semantic + Keyword) (Phase 1)
    ↓
4. Initial Retrieval (50-100 candidates)
    ↓
5. Re-Ranking with Cross-Encoder (Phase 3)
    ↓
6. Metadata-Aware Scoring (recency, type, importance)
    ↓
7. Diversity Filtering (avoid duplicates from same doc)
    ↓
8. Return Top N Results (10-15)
```

**Abstraction pattern:**

```python
class ProductionRetriever:
    """Unified RAG retrieval pipeline."""

    def __init__(self, db_client, reranker, config):
        self.db = db_client
        self.reranker = reranker
        self.config = config

    def retrieve(self, query, n_results=10, **options):
        """Execute full retrieval pipeline."""

        # Phase 2: Route to collections
        collections = self.route_query(query, options.get('collections'))

        # Phase 1: Build metadata filters
        filters = self.build_filters(query, options.get('filters'))

        # Phase 1: Hybrid search across routed collections
        candidates = self.hybrid_search(
            collections=collections,
            query=query,
            filters=filters,
            n_results=50  # Over-retrieve
        )

        # Phase 3: Re-rank
        reranked = self.rerank(query, candidates)

        # Phase 3: Metadata-aware scoring
        scored = self.apply_metadata_scores(reranked)

        # Phase 3: Ensure diversity
        diverse = self.ensure_diversity(scored)

        return diverse[:n_results]
```

**Configuration-driven:**

```yaml
# retrieval_config.yaml
routing:
  default_collections: ["vault", "portfolio"]
  rules:
    - pattern: "email"
      collections: ["email"]
    - pattern: ["chat", "message"]
      collections: ["messaging"]

filtering:
  default_date_range: "last_year"

reranking:
  model: "cross-encoder/ms-marco-MiniLM-L-6-v2"
  candidate_count: 50

scoring:
  recency_weight: 0.5
  type_weights:
    deliverable: 1.5
    research: 1.2
    note: 1.0
    chat: 0.8

diversity:
  max_chunks_per_doc: 3
```

**Toggle stages for debugging:**

```python
# Disable re-ranking for debugging
retriever.retrieve(query, skip_reranking=True)

# Test routing only
collections = retriever.route_query(query, debug=True)
```

**Impact:** Professional-grade retrieval system. All phases work together seamlessly.

---

## Before/After Comparison

### Test Query: "Find AI strategy work for automotive clients"

**Before (Naive Semantic Search):**
- **Search scope:** All 100k chunks across all collections
- **Returns:** 50+ results, mix of high/low relevance
- **Includes:** 3-year-old newsletters, passing mentions in chat, unrelated docs with "AI" or "strategy"
- **User experience:** Must manually browse and filter
- **Time to answer:** 2-5 minutes

**After (Production Architecture):**
- **Phase 2 (Routing):** → portfolio collection only (9k chunks)
- **Phase 1 (Filtering):** + `where={"client": {"$contains": "automotive"}, "date": {"$gte": "2023-01-01"}}`
- **Phase 1 (Hybrid):** + semantic similarity + keyword match for "strategy"
- **Phase 3 (Re-ranking):** + cross-encoder scoring + recency boost + deliverable prioritization
- **Returns:** 10 highly relevant results
- **User experience:** Scan top 3 results, find answer
- **Time to answer:** <30 seconds

**Improvement:** 4-10x faster time-to-answer, higher confidence in results

---

## Phased Implementation Approach

### Start Simple

**Level 1: Basic RAG (Start Here)**
- Embedding + semantic search
- Single collection
- Return top N results
- **Works well for:** <10k chunks, single domain

### Add Filtering

**Level 2: Metadata Filtering (First Upgrade)**
- Expose `where` parameter for metadata filtering
- Filter by date, client, project, type
- **When:** You need to narrow by known metadata

### Add Intelligence

**Level 3: Query Routing (Second Upgrade)**
- Route queries to appropriate collections
- Keyword-based or LLM-based classification
- **When:** You have multiple collections and get noise from irrelevant sources

### Add Quality

**Level 4: Re-Ranking (Third Upgrade)**
- Two-stage retrieval with cross-encoder
- Metadata-aware scoring
- Diversity filtering
- **When:** Relevance matters and you can tolerate 200-500ms latency

### Orchestrate

**Level 5: Unified Pipeline (Advanced)**
- All phases integrated
- Configuration-driven
- Monitoring and observability
- **When:** System is production-critical and must scale

---

## Decision Points

### Re-Ranking: Local vs API

**Local Cross-Encoder:**
- ✅ No cost
- ✅ Fast (~10ms per pair)
- ✅ Privacy (runs on your machine)
- ⚠️ Quality: Good but not excellent
- ⚠️ Requires setup (model download, dependencies)

**API Service (Cohere, Jina):**
- ✅ Excellent quality
- ✅ No setup
- ⚠️ Cost: ~$1 per 1k searches
- ⚠️ Slower: 100-300ms (network latency)
- ⚠️ Privacy: Data sent to API

**Recommendation:** Start local, switch to API if quality isn't sufficient or you value convenience over cost.

---

### Query Routing: Keyword vs LLM

**Keyword-Based Routing:**
- ✅ Fast (<1ms)
- ✅ Deterministic (easy to debug)
- ✅ No cost
- ⚠️ Brittle (rules need maintenance)
- ⚠️ Misses nuanced intent

**LLM-Based Routing:**
- ✅ Smart (understands natural language)
- ✅ Flexible (handles ambiguity)
- ⚠️ Slower (100-200ms)
- ⚠️ Non-deterministic (harder to debug)
- ⚠️ Costs (if using API; free if local Ollama)

**Recommendation:** Hybrid approach:
- Keyword rules for obvious patterns (fast path)
- LLM for ambiguous queries (smart fallback)
- Default to primary collections if uncertain

---

### Metadata Boost: How Much?

**Light Boosting (10-20%):**
- Recency/type has subtle influence
- Relevance dominates
- **Use when:** Your data is consistently high quality

**Medium Boosting (30-50%):**
- Recency/type has noticeable influence
- Balances relevance and metadata
- **Use when:** Recent or deliverable docs are typically more valuable

**Heavy Boosting (100%+):**
- Recency/type dominates
- Older or note-type docs rarely surface
- **Use when:** Recent client work is almost always more relevant than old notes

**Recommendation:** Start medium, tune based on your feedback.

---

## Common Pitfalls

### Over-Engineering Too Early

**Symptom:** Implementing all 4 phases before testing Phase 1

**Solution:** Start with metadata filtering only. Add phases when current approach fails.

---

### Ignoring Chunking Strategy

**Symptom:** Great retrieval architecture but poor results due to bad chunks

**Solution:** Fix chunking first. Research shows chunking strategy matters as much as retrieval architecture.

**Good chunking:**
- Size: 500-1000 tokens per chunk (context-dependent)
- Overlap: 10-20% between chunks
- Semantic boundaries: Split on headings, paragraphs, not arbitrary character counts
- Metadata: Preserve context (title, author, date, section)

---

### Not Validating Improvements

**Symptom:** Added re-ranking but didn't measure if it helped

**Solution:** Create test query set with known-good results. Measure before/after:
- Precision: % of returned results that are relevant
- Recall: % of relevant docs that are returned
- MRR (Mean Reciprocal Rank): Position of first relevant result
- Time to answer: How long user takes to find answer

---

### Forgetting Privacy

**Symptom:** Sending sensitive data to re-ranking APIs

**Solution:**
- Use local models for sensitive data
- Or redact sensitive info before API calls
- Or use API providers with privacy guarantees (Cohere, Jina have business plans)

---

## Tools & Technologies

**Vector Databases with Advanced Features:**
- ✅ ChromaDB - Metadata filtering, where_document, Python-native
- ✅ Weaviate - Hybrid search built-in, BM25 + vector
- ✅ Qdrant - Excellent filtering, payload-based search
- ✅ Pinecone - Managed, sparse-dense hybrid search
- ✅ Milvus - Scalable, enterprise features

**Cross-Encoder Models:**
- `cross-encoder/ms-marco-MiniLM-L-6-v2` (lightweight, fast)
- `cross-encoder/ms-marco-MiniLM-L-12-v2` (better, slower)
- `cross-encoder/ms-marco-electra-base` (high quality)

**Embedding Models (if upgrading):**
- `nomic-embed-text` (good all-around, 768 dim)
- `text-embedding-3-small` (OpenAI, 1536 dim)
- `embed-english-v3.0` (Cohere, excellent quality)

**Re-Ranking APIs:**
- Cohere Rerank API
- Jina Rerank API
- Mixedbread AI Rerank

---

## Testing Your System

### Create a Test Query Set

**10-20 queries representing real usage:**

```yaml
# test_queries.yaml
queries:
  - query: "AI strategy work for automotive clients"
    expected_collections: ["portfolio"]
    expected_filters: {client: automotive, type: deliverable}
    known_relevant_docs: ["doc_123", "doc_456"]

  - query: "What did John say about the project deadline?"
    expected_collections: ["messaging"]
    expected_filters: {person: John}
    known_relevant_docs: ["msg_789"]
```

### Measure Metrics

**For each query:**
1. Run through your pipeline
2. Check: Are known-relevant docs in top 10?
3. Measure: Position of first relevant result (MRR)
4. Time: Latency end-to-end

**Compare before/after each phase:**
- Phase 1: Did filtering reduce noise?
- Phase 2: Did routing improve precision?
- Phase 3: Did re-ranking improve relevance?

### User Validation

**The ultimate test:**
- Use your system for real work
- Notice: Am I finding answers faster?
- Notice: Are results more relevant?
- Notice: Do I trust the system more?

If yes → improvement worked. If no → investigate why.

---

## When NOT to Upgrade

**Your naive search works fine if:**
- Results are consistently relevant
- You don't mind browsing 20-30 results
- Your collection is small and focused
- Your queries are simple and unambiguous

**Don't upgrade just because these patterns exist.** Upgrade when you feel the pain of the current approach.

**Complexity has a cost:**
- More code to maintain
- More configuration to manage
- More things that can break
- Harder to debug

**Only add complexity when simpler approaches fail.**

---

## Further Reading

**Research Papers:**
- "Lost in the Middle: How Language Models Use Long Contexts" (Liu et al., 2023)
- "Precise Zero-Shot Dense Retrieval without Relevance Labels" (Gao et al., 2022)
- "In Defense of Cross-Encoders for Zero-Shot Retrieval" (Askari et al., 2023)

**Production RAG Guides:**
- Gradient Flow: "Best Practices in Retrieval Augmented Generation"
- Google Cloud: "Optimizing RAG Retrieval"
- DeepLearning.AI: "Advanced Retrieval for AI"

**ChromaDB Documentation:**
- [Filtering Guide](https://docs.trychroma.com/usage-guide#filtering)
- [Advanced Queries](https://docs.trychroma.com/usage-guide#querying-a-collection)

---

## Next Steps

1. **Assess your current state:** Do you have the signals that you're ready to upgrade?
2. **Start with Phase 1:** Metadata filtering gives immediate improvement with low effort
3. **Validate improvement:** Measure before/after with test queries
4. **Add phases incrementally:** Only move to next phase when current one is working
5. **Stop when it's good enough:** Don't implement Phase 4 if Phase 2 solves your problem

**Remember:** Production RAG is about making retrieval good enough for your use case, not about implementing every technique in the literature. Start simple, evolve when you feel the pain.
