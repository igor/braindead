# RAG Query Examples

Real examples of RAG queries and results (sanitized).

## Example 1: Exploratory Query

**Collection:** main_vault (45,000 chunks, 13,000 files)
**Query:** "What brand positioning frameworks have I used successfully?"
**n_results:** 20 (exploratory)

### Results

Found 20 relevant results from your vault:

1. **Aaker Brand Identity Model** (Client_TechCo, 2024-03-15)
   - Source: `4. Archive/Clients/2024/TechCo_BrandStrategy/01_Strategic_Foundation/Positioning_Framework.md`
   - Used for: B2B technology company
   - Strength: Comprehensive brand essence capture
   - Implementation: Brand identity prism, value proposition canvas
   - Outcome: Successful board approval, implemented Q2 2024

2. **Jobs-to-be-Done Positioning** (Client_AppCo, 2024-06-20)
   - Source: `4. Archive/Clients/2024/AppCo_MarketResearch/04_Strategy_and_Planning/JTBD_Positioning.md`
   - Used for: Consumer mobile app
   - Strength: User outcome focus, clear differentiation
   - Integration: Combined with emotional/social job framework
   - Outcome: 40% increase in conversion messaging

3. **Kapferer Brand Identity Prism** (Client_LuxuryBrand, 2024-09-10)
   - Source: `4. Archive/Clients/2024/LuxuryBrand_Positioning/03_Research_Insights/Brand_Prism_Application.md`
   - Used for: Premium fashion brand
   - Strength: Physique-to-Relationship dimensions, cultural resonance
   - Note: Client preferred over Aaker for luxury context
   - Outcome: Refined positioning led to 25% brand perception lift

[17 more results...]

**Synthesis:**
Based on past work, framework choice depends on client type:
- **B2B/Tech:** Aaker (comprehensive, logical structure)
- **Consumer/App:** JTBD (outcome-focused, conversion-optimized)
- **Premium/Luxury:** Kapferer (cultural depth, brand heritage)

---

## Example 2: Focused Query

**Collection:** methodologies (1,200 chunks, 150 files)
**Query:** "prioritization method for distributed teams working async"
**n_results:** 10 (focused)

### Results

Found 10 relevant methods from Gamestorming collection:

1. **Impact/Effort Matrix** (Gamestorming: Planning)
   - Time: 45-60 minutes
   - Works async: Yes (Miro/Mural)
   - Process: Plot initiatives on 2x2 grid (high/low impact × easy/hard effort)
   - Best for: Strategic prioritization, roadmap planning
   - Remote adaptation: Use digital whiteboard, async voting rounds

2. **Dot Voting** (Gamestorming: Decision-Making)
   - Time: 15-30 minutes
   - Works async: Yes (digital dots, 24hr voting window)
   - Process: Each participant gets fixed number of votes to allocate
   - Best for: Quick consensus, feature prioritization
   - Remote adaptation: Use Miro sticky dots or voting plugin

3. **Weighted Scoring** (Thinking Tools: Decision-Making)
   - Time: 60-90 minutes
   - Works async: Partially (async scoring, sync discussion)
   - Process: Score options against weighted criteria
   - Best for: Complex decisions with multiple factors
   - Remote adaptation: Shared spreadsheet, async scoring, sync synthesis

[7 more results...]

**Recommendation:**
For distributed team prioritization:
- **Quick consensus (< 30 min):** Dot Voting
- **Strategic planning (45-60 min):** Impact/Effort Matrix
- **Complex multi-criteria (90 min):** Weighted Scoring

All three work well async with digital tools.

---

## Example 3: Multi-Query Strategy

**Question:** "How should I approach brand strategy for a sustainability-focused startup?"

**Query 1:** "brand positioning frameworks for startups"
**Collection:** main_vault
**n_results:** 15

**Query 2:** "sustainability positioning and messaging"
**Collection:** main_vault
**n_results:** 15

**Query 3:** "startup brand building with limited budget"
**Collection:** portfolio (past client work)
**n_results:** 10

### Synthesis Across Queries

**From Query 1 (Brand Frameworks):**
- Lean Brand Stack (Eric Ries approach)
- Minimal Viable Brand (focus on core differentiation)
- Values-first positioning

**From Query 2 (Sustainability):**
- Authenticity critical (avoid greenwashing perception)
- Evidence-based claims (certifications, data)
- Community-driven narrative

**From Query 3 (Startup Constraints):**
- Focus on owned channels (social, content)
- Partner with aligned organizations (borrowed credibility)
- Document impact metrics from day one

**Integrated Recommendation:**
1. Start with Values-first positioning (sustainability as core, not feature)
2. Use Minimal Viable Brand approach (focus resources on differentiation)
3. Build evidence-based sustainability narrative (data, certifications)
4. Leverage community and partnerships (constraint-to-strength)
5. Document impact metrics continuously (supports long-term credibility)

---

## Example 4: Project-Specific Collection

**Collection:** project_clientA (dense, 3,000 chunks from single project)
**Query:** "consumer pain points around current solutions"
**n_results:** 5 (lower for dense collection)

### Results

Found 5 highly relevant insights:

1. **Interview Wave 1 - Trust Issues** (Research_Insights, 2025-10-05)
   - Pain: Existing solutions feel "too cheap to trust"
   - Quote: "If it's free, they're selling my data" (7 of 12 participants)
   - Implication: Price signals quality and privacy in this market

2. **Survey Results - Feature Overload** (Research_Insights, 2025-10-12)
   - Pain: 82% feel overwhelmed by competitor feature lists
   - Quote: "I just want it to work, not learn a manual"
   - Implication: Simplicity and ease-of-use are differentiators

3. **Interview Wave 2 - Integration Friction** (Research_Insights, 2025-10-18)
   - Pain: Existing solutions don't work with tools users already have
   - Quote: "Another app to check? No thanks" (9 of 12)
   - Implication: Ecosystem integration is table stakes

4. **Competitive Analysis - Support Gaps** (Research_Insights, 2025-10-25)
   - Pain: Poor customer support across all competitors
   - Evidence: 2.3 avg rating for "support" across 8 competitor apps
   - Implication: Support quality could be significant differentiator

5. **JTBD Analysis - Emotional Jobs** (Research_Insights, 2025-11-01)
   - Pain: Existing solutions address functional jobs but miss emotional/social
   - Insight: Users want to "feel confident" and "look knowledgeable"
   - Implication: Emotional messaging underserved in category

**Opportunity Map:**
Differentiate on: Trust (premium pricing), Simplicity (vs feature bloat), Integration (vs standalone), Support (vs self-service), Emotional resonance (vs purely functional)

---

## Notes on Query Effectiveness

### What Works Well

- **Conceptual queries:** "How do I approach X?"
- **Pattern matching:** "What have I done successfully for Y?"
- **Cross-domain:** Finding connections across projects/domains
- **Exploratory:** "What do I know about X?"

### What Works Less Well

- **Exact phrases:** Use keyword search instead
- **Names:** Without context ("John" returns too many results)
- **Very recent:** If not re-indexed yet

### Best Practices

1. **Be specific:** "brand positioning frameworks for B2B SaaS" not just "frameworks"
2. **Include context:** "sustainability positioning for premium market" not just "sustainability"
3. **Adjust n_results:** 15-20 for exploration, 5-10 for precision, 3-5 for dense collections
4. **Multi-query for complex questions:** Break down, query parts, synthesize
5. **Combine with keyword when needed:** RAG for concepts, keyword for exact matches
