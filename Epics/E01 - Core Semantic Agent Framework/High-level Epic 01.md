
#### Epic #1 — **Core Semantic-Agent Framework**

_Objective: ship a fully-self-contained micro-service template that can be pointed at any structured source and, within minutes, publish a secure, ontology-enriched MCP/A2A card._

|**Feature Cluster**|**Key Features**|**What “Done” Looks Like**|
|---|---|---|
|**1. Agent Runtime Core**|• Lightweight container image (≤ 250 MB) with FastAPI + async scheduler• Config file & env-var schema (source URL, creds, LLM endpoint, ontology repo, polling intervals)• Hot-reload & graceful shutdown hooks|• `docker run quillux/semantic-agent` starts with one command• 95th-percentile cold-start < 5 s|
|**2. Connector SDK**|• Pluggable adapter API (`connect()`, `introspect()`, `query_sample()`) for JDBC/ODBC/REST• Reference adapters: Fabric, Snowflake, Databricks, BigQuery• Dialect utility for quoting, pagination, sampling|• New adapter added by subclassing `BaseConnector` in <30 LOC• Adapters pass unit tests for auth errors, network blips, pagination edge-cases|
|**3. Schema & Profiling Engine**|• Auto-crawl tables, views, stored procs; capture PK/FK, types, row counts• Statistical profiler (min/max/nullable/%distinct) on sampled columns• Change-detection diff to flag drift|• Full crawl on first run; subsequent runs emit in <60 s for 10k-table DB|
|**4. Documentation Ingestion**|• File watcher / SharePoint crawler• Extractor for PDF, DOCX, Markdown to clean text + headings• Embedding cache (vector store) to avoid re-LLM-ing unchanged docs|• >90 % of table/column names found in docs matched to schema entities|
|**5. Semantic Enrichment Pipeline**|• LLM prompt-chain: “draft descriptions → propose ontology mapping → generate synonyms/tags”• Confidence score + explanation• Delta-only updates to semantic store|• ≥80 % of high-confidence mappings auto-approved in pilot datasets|
|**6. Human-in-Loop Workflows**|• REST + Teams bot endpoint: “Review mapping #1234? (👍/👎)”• Callback to persist confirmations or corrections• Audit log (who, what, when)|• SME can approve/decline a mapping from chat in ≤ 3 clicks; audit appears in `/metrics`|
|**7. Security & Policy Hooks**|• Per-connector secret vault (KMS-backed)• Bearer-token pass-through for row/column RLS• Policy stub to integrate with org IAM (Azure Entra)|• Pen-test: credential never leaves pod; row-level filter enforced in downstream query|
|**8. MCP / A2A Interface**|• `/mcp/schema` returns raw + enriched JSON-L• `/a2a/card` emits OpenAPI-style agent card• Webhooks for “schema-updated”, “agent-healthy”|• Query Agent can discover a new source <30 s after agent goes **running**|
|**9. Observability & Health**|• Prometheus metrics: uptime, crawl latency, LLM tokens, mapping accuracy• Structured logs with trace IDs• `/health` (liveness) & `/ready` (readiness) endpoints|• Grafana dashboard shows crawl duration trend & drift count per day|
|**10. Packaging & CI/CD**|• Helm chart with value overrides• GitHub Actions: test, scan, sign, publish image• Semantic-versioned API docs auto-generated (Swagger)|• `helm upgrade --set source=snowflake` deploys to dev cluster with zero downtime|

**Out of Scope for this Epic (pushed to later epics)**

- Multi-source agents
    
- Advanced caching & query acceleration
    
- Continuous ontology reconciliation across agents
    
- Full SOC 2/HIPAA artifact generation
    

This breakdown turns Epic #1 into ten concrete workstreams, each with a crisp acceptance criterion—so teams can swarm, ship incrementally, and light up the very first Semantic Agents in production.