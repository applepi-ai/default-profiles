# Research Report Job Best Practices

Reference guide for designing DeepWork jobs that produce research reports, analytical documents, or similar investigative deliverables. Use this when defining jobs via the `define` step.

## The General Pattern

Most report-authoring jobs follow a five-phase structure. Not every job needs all five as separate steps, and some phases combine naturally, but understanding the full arc helps you design a job that doesn't skip critical work.

### 1. Prepare

**Purpose**: Verify that the tools and data sources the job will rely on are actually accessible before any real work begins.

This step is about validating prerequisites, not doing research. It also means that critical security requests of the user get made early. Common activities:

- **Database connectivity**: Use the sql query tools you expect to need with a trivial query to confirm credentials work and the schema is reachable.
- **Web search tools**: Confirm web search and browsing tools are enabled. If the job needs to read specific sites, verify they don't require login. If they do, get the user to authenticate (e.g., via Claude in Chrome) before proceeding.
- **API access**: Test API keys or tokens against a lightweight endpoint.
- **Directories Setup**: Make the output directory, working directory, and inital placeholder files in them to make sure you have locations ready.

### 2. Align

**Purpose**: Build enough understanding of the domain and the user's intent to scope the analysis correctly.

This is a cyclical step: do light research, then ask clarifying questions, then refine understanding, repeat. It ends when both the agent and user agree on what "done" looks like.

**The cycle**:

1. **Light grounding research** - Just enough to ask smart questions. Not deep analysis.
2. **Clarify with the user** - Surface ambiguities and propose scope boundaries.
3. **Repeat** until there's shared understanding.

**Example - Private data (SQL-centric)**:
- Run broad queries to get the lay of the land: total record counts, key column names, date ranges, apparent segmentation columns (e.g., `division`, `region`).
  - Use the `write_queries` flow in sub-agents for generating batches of queries. Be sure to continue the same sub-agent convos if you need to iterate on queries or need more new queries.
  - Run the appropriate sql query tool for each query file, but run them in large, parallel batches.
- Then ask the user: "I see 45,000 customer records across 3 divisions. Should we scope to a particular division? I'm defining churn as customers with no activity in 90 days - does that match your definition?"

**Outputs**: A scoping document that captures the agreed-upon research questions, data sources, definitions, exclusions, and success criteria. This becomes the north star for the Analyze step.

### 3. Analyze

**Purpose**: The core research cycle. Query, record, synthesize, and deepen iteratively.

This is where most of the work happens. The key discipline is maintaining structured working files so that nothing gets lost and the narrative builds progressively.

This keep using the same sub-agent conversation for the write_queries flow, so you can continue the same sub-agent convos if you need to iterate on queries or need more new queries. Only start a new sub-agent conversation if you need to query a different datasource.

**Working files to maintain**:

| File | Purpose |
|------|---------|
| [individual .query files] | The raw queries and their outputs. |
| Questions & Answers | Running list of research questions. As you find answers, record them. As answers suggest new questions, add those. This drives the iterative deepening. |
| Draft report | The evolving narrative. Updated as new findings emerge. Forces you to synthesize as you go rather than dumping data at the end. |

**The iterative deepening pattern**:

Analysis should deepen in layers, not stay shallow across many topics. Each answer should prompt "why?" or "what drives that?" questions:

- **Layer 1**: Top-level facts. "What was our AWS spend last month?" -> $10k. "How does that compare to prior month?" -> Up $1k.
- **Layer 2**: Decomposition. "What services drove the spend?" -> $8k EC2, $1k S3, $1k other. "Where was the increase?" -> All in EC2.
- **Layer 3**: Root causes. "Is our EC2 fleet well-utilized?" -> Many instances with attribute X are underutilized. "Are specific workloads driving the increase?" -> Yes, instances tagged `daily_sync_*` are up ~$2k.
- **Layer 4+**: Continue until you hit actionable findings or diminishing returns.

**When to stop deepening**: When additional queries aren't changing the narrative, or when you've answered the questions from the Align step to a sufficient depth. But make sure that any questions that a reasonable business person is likely to ask when looking at your output are answered.

**Section titles should state conclusions, not processes.** Name h2 sections for the finding or takeaway, not the work being done. Good: "2 Departments Drive the Change", "Personal Care Dominates Growth", "Volume Shifted to Price After 2021". Bad: "Department Breakout", "Service Category Analysis", "Volume vs Price Decomposition". The reader should learn the conclusion from the heading alone.

**Outputs**: The working files above (queries, Q&A tracker, draft report)

#### Analyze Step Reviews

Reviews are not a standalone phase but checkpoints woven into all the steps, especially the Analyze step. Use DeepWork's `reviews` mechanism in `job.yml` to define quality gates.

**Review Quality Gates for the Analyze phase**:

- **Query completeness**: The key research questions are all addressed with citations referencing query documents.
- **Draft coherence**: The draft report tells a logical story. Sections are connected rather than isolated findings.
- **Depth adequacy**: The analysis goes deep enough on the important threads. No obvious follow-up questions are left unasked.
- **Citation integrity**: Claims in the draft are backed by specific queries/sources from the query log.
- **Q&A completeness**: All questions are answered. No obvious follow-up questions are left unasked.

### 5. Present

**Purpose**: Polish the narrative and then delegate to `report_formatting/format_report` for final formatted delivery.

> **Quality target**: The gold-standard DocBaker report is at `.deepwork/learning-agents/presentation-expert/examples/docbaker/hhs_medicaid_spending_report.html` (also available in the presentation_expert agent directory). Open it in a browser to calibrate what the final output should look and feel like: conclusion-oriented H2 headings, MetricCards at the top, charts with source citations, Callout blocks for findings and caveats, two-column newspaper layout. The default format for reports is **DocBaker** (`theme: newspaper`) — only use Slidev when the user explicitly asks for a deck/presentation.

The draft report from the Analyze step has the right content but may not be presentation-ready. This step has two parts: narrative polish (done in-place on the draft) and formatting (delegated to the `report_formatting/format_report` workflow).

**Common activities**:

- **Narrative polish**: Edit the draft in-place — tighten prose, add executive summary, ensure the document flows well for someone reading it cold.
- **Q&A surfacing**: Review the Q&A tracker for important deferred or partially answered questions and add them to the draft if not already covered.
- **Supporting materials**: Assemble appendices, data tables, methodology notes in the report directory.
- **Format for delivery**: Invoke the `report_formatting/format_report` workflow. This handles visualization creation, formatting (Slidev presentations or DocBaker HTML documents), and visual proofing — do not duplicate that work in the Present step itself.

**Outputs**: The final formatted deliverable produced by `report_formatting/format_report`, plus supporting materials.

#### Present Step Reviews

Separate the content and formatting reviews:

- **Content accuracy**: Citations are preserved from the draft, numbers match source data, and arguments are logically sound.
- **Units on all numbers**: Every numeric value in charts, tables, and metric cards includes its unit (e.g., `"$1.2M"`, `"42%"`, `"1,200 patients"`). No bare numbers without units.
- **Audience fit**: Language, detail level, and framing match the intended audience (executives vs. engineers vs. clients).
- **Formatting quality**: The `format_report` workflow produced a well-structured deliverable with no rendering issues.

### 6. Feedback

**Purpose**: Get feedback from the user on the final deliverable and iterate if necessary.

**Common activities**:

- **Present the final deliverable** to the user. Use `preview` or similar tools to present the deliverable in a way that is easy for the user to consume.
- **Get feedback** from the user on the final deliverable.
- **Iterate** on the final deliverable based on the user's feedback.

If the user indicates that they are satisfied, if the analysis was very lengthy, then offer to the user that you can run `/deepwork Run learn to get better at future analyses` to help you get better at future analyses.

This step does not have any reviews.