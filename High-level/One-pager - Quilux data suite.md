[DRAFT initial point of view]

---

  
# Quillux data suite

\[DRAFT initial point of view\]  
---

## **1 Purpose & Scope**

**Empower agents with the full potential of enterprise data.**

Enable autonomous AI agents to discover, understand and activate business data — adding human knowledge to the data itself unlocking new forms of intelligent collaboration.

Build distributed AI-native semantic agents that understand data sources, integrate and manage data by collaborating with information and business professionals allowing to organize data into a set of interconnected knowledge graphs. We aim to ensure that every business enters the age of AI powered by the full potential of its data.

Quillux aims to make enterprise data useful by connecting agents, data, and people in a secure and open way. As more agents learn from and share data with each other, organizations gain a clearer, more unified view of their information. This growing network makes it easier for everyone to find, understand, and use data—helping each business get more value from their unique knowledge and stand out in a world powered by AI.

Each agent discovers its host schema, enriches semantic context from downstream uses, documentation and interaction with human professionals, and enforces secure access. Agents and orchestration can query the data with confidence. Running inside the customer tenant and integrating with Fabric, Snowflake, Databricks, and BigQuery, Quillux is the data plane for the emerging AI agent ecosystem.

### Quillux Semantic Agent

Quillux semantic agents understand and monitor data sources to derive insights from data across the organization.

A **Quillux Semantic Agent** is a self-contained micro-service that:

1. Connects to **one** structured data source (JDBC, ODBC or REST).  
2. Accepts **uploaded documentation** (PDF, DOCX, TXT, Markdown) \[or auto-discovers in Sharepoint and enterprise data sources\]  
3. Auto-discovers the source schema, views, stored procedures and profiles the data source (basic statistics on the data source).  
4. Calls an **LLM** to combine schema *and* documentation-derived terms to propose ontology mappings and enriched descriptions.  
5. In cases when there is not enough information confirms details with human and actively interviews a human contacts (experts) to uncover and capture tacit knowledge.  
6. Checks the mapping and description with human in the loop.  
7. Exposes via  (Model context protocol):  
   * Raw schema, metadata and enrichments  
   * Suggested and accepted semantic mappings  
8.  Creates and broadcasts an A2A(agent to agent protocol) agent card that includes the relevant schema and access details.  
9. Registers with Agent hub (on creation)  
10. Maintains observability and health metrics via API  
11. Transmits observability to Agent hub

The combination of multiple agents allows to build a virtual knowledge graph on top of data and allows querying ‘talk-to-your-data’ agents to ask questions taking into account wider context and getting high fidelity without .

Out-of-scope for MVP: agent connecting to multiple sources, advanced caches, connection to data catalogs (for ingestion and deposit of enrichments).

### Quillux Query Agent

A Quillux Query Agent is a self-contained micro-service that federates all Quillux Semantic Agent into one virtual knowledge graph. It accepts a single natural language request, translates it into a SQL request, sends sub-queries to the source agents, and streams back a unified, provenance-rich answer in real time.

Features:

1. **Provides Natural-Language Query Interface (own web chat or Teams app)**   
2. **Pulls in \- all Semantic agent schemas from Agent Hub (**enriched schema, semantic mappings, dialect, SQL endpoint, access token \[?\]**)** \- this forms the unified catalog view for query construction.  
3. **Converts user text into federated SQL sub-queries.**  
4. **Federated auto-update** – auto-refreshes its routing table when a new agent goes *running* or when a drift alert updates an agent card.  
5. **Receives a query and assesses** information completeness (replies to the user if there is insufficient data)  
6. **Cross-Source query build-out**– can create a joint query that produces the answer on top of the knowledge graph schema.  
7. **Query break-down into minimal elements by source** – breaks the query into subqueries delegated to each of the agents \[as sql query / or natural language query\]  
8. **Pushes the query into each of the agents** – asks each agent to provide the response.  
9. **Policy-Aware Delegation** – forwards the caller’s bearer token to each semantic agent; if a sub-query fails ACL checks the planner prunes the entire branch.  
10. **Collects answers, joins, applies relevant filter**   
11. **Validates correctness** \- validates logic (through LLM) of the answer  
12. **Presentation layer** \- returns the answer as a table to the principal that asked it  
13. **Health Surface** – exposes `/health` with uptime and latency metrics, mirroring the pattern used by every Semantic Agent. 

Out of scope for MVP**:** more than two sources, performance optimization, sequential and other types of federation, knowledge graph size limits, feedback to semantic agents (continuous semantics evolution), query caching, query storage, query plan assessment by human-in-the-loop.

### Quillux Agent Hub

Quillux Agent Hub centralizes the lifecycle, configuration and discovery of Quillux agents. It provides a single pane of glass for deploying, managing and governing the agents.

A Quillux Agent Hub is a service that:

1. **Registers new agents** on creation adds the agent’s domain to the registry

2. **Manages a data-source registry** \- holds connection profiles (JDBC/ODBC/REST, file paths, credentials), runs health checks, and surfaces connection status.

3. **Hosts a shared ontology repository** \- upload, edit, version and browse your business vocabulary so all agents reference the same set of semantic models.

4. **Maintains agent observability**—pulls logs and metrics from every agent, exposes lifecycle APIs (`/health`, `/metrics`), and streams activity to a central dashboard.

5. **Stores global configurations**—timeouts, retry/back-off policies, ACL templates, and drift-alert thresholds that all agents inherit.

6. **Provides guided creation UIs**—step-by-step wizards and templates for spinning up Semantic agents in minutes.

7. **Optionally caches A2A cards** for low-latency lookup by Query Agents or other consumers.

By centralizing registry, policies and telemetry, the Agent Hub ensures that adding, scaling or retiring agents stays lightweight—and that your entire semantic estate remains discoverable, consistent and secure.

Continuing the **Personas** section for the Quillux Agents document, grounded in the tone and scope of your draft:

---

## **2 Personas**

### 2.1 IT Administrator / Platform Owner

Responsible for system security, compliance, and uptime. Oversees governance and access policies \- via Agent hub.

**Needs**: Central management of credentials, policies and monitoring. Auditability and secure integration with enterprise systems.

**What Quillux Enables**: Set ACL templates, view live agent telemetry, manage policies via Agent Hub, and ensure federated queries are policy-compliant.

**Success Metric**: Governance overhead reduced without compromising on compliance, better overall IT service.

### 2.2 Data Product Owner (Data/Analytics Engineer)

Data Product Owner (agent creator) often holds the position of data engineer, architect or advanced data analyst in the company.

Data product owner is responsible for maintaining the trustworthiness and performance of data artefacts. They are the primary deployers and maintainers of Semantic Agents.

* **Needs**: Simple onboarding of new sources, semantic consistency across agents, observability into failures or schema drift.

* **What Quillux Enables**: Spin up a semantic agent for a new source in minutes via Agent Hub; enforce shared ontologies; monitor data quality and agent health centrally.

* **Success Metric**: Time to onboard new data source drops from weeks to hours.

### 2.3 Domain Expert / Business SME

Business subject matter experts provide the tacit knowledge that Semantic agents encode. They are infrequent users but essential during setup and refinement of .

* **Needs**: Minimal involvement for maximum knowledge extraction: easy mechanisms to share undocumented logic or definitions, clear interfaces for reviewing and correcting semantics.

* **What Quillux Enables**: Be prompted to clarify ambiguous terms or validate mappings during the enrichment loop of the Semantic Agent (rather than proactively participating).

* **Success Metric**: % of semantic enrichments validated and confirmed by domain SMEs.

### 2.4 Data Analyst (Operational / Business)

The data analyst is a daily consumer of Quillux Query Agent capabilities. They often work in Excel or BI tools, and rely on the Query Agent’s natural-language interface to obtain ad-hoc answers without needing to navigate complex schemas or identify sources of data.

* **Needs**: Easy access to fresh data across systems, ability to trust results, quick turnaround \- ability for ad hoc and routine runs of data queries for analytics.

* **What Quillux Enables**:   
1) Ask complex business questions across multiple systems like "Which SKUs were most overstocked last week across coastal regions?" and get structured results without needing to file IT requests.  
2) For complex dashboards or analytics the query can be consolidated and saved for repeated queries.

* **Success Metric**: Reduced query turnaround time from days to minutes.

\[TBD do we add the role of Software engineer\]

### 2.5 AI Agents across the organization

AI agents across the organization require access to data \- on behalf of their users and processes they facilitate. These needs are diverse. By extracting answers dynamically from 

* **Needs**: Easy access to fresh data across systems, ability to trust results, quick turnaround.

* **What Quillux Enables**: Ask complex business questions across multiple systems like "Which SKUs were most overstocked last week across coastal regions?" and get structured results without needing to file IT requests.

* **Success Metric**: Reduced query turnaround time from days to minutes.

\[TBD do we add the role of Software engineers who develop Agents? (they should be directing agents to the right data sources)\]

**![][image1]**

---

# **3 Value proposition**

**Quillux: Turn scattered enterprise data into instant, trusted answers.**

* **Plug-and-Play Semantics** – Stand up a smart “lens” on any structured source in minutes. Each Semantic Agent auto-discovers schema, pulls in docs, and spits out human-readable business terms, so data finally speaks the same language across finance, ops, and sales.

* **One Virtual Knowledge Graph, Zero Migration Pain** – The Query Agent federates every Semantic Agent on the fly. Ask one natural-language question and get a single, provenance-rich result—no ETL pipelines, cross-DB tinkering, or data duplication.

* **Governance Built-In, Not Bolted-On** – The Agent Hub centralizes policies, credentials, and health metrics. IT keeps airtight control; auditors get traceability; teams stay unblocked.

* **Minutes-to-Insight, Not Months** – Data engineers onboard new sources in hours; analysts pull answers in seconds; executives see the full picture faster than shifting priorities.

* **Future-Proof Architecture** – Agents scale horizontally, inherit shared ontologies automatically, and surface drift alerts the moment things change. Swap sources, add domains, or embed into Copilot/Teams without ripping and replacing.

* **Lower TCO, Higher Trust** – Cut integration costs, shrink query backlogs, and boost confidence with explainable, lineage-aware results—all while using the storage, BI, and identity stacks you already own.

**Bottom line:** Quillux lets every person (or AI agent) in the company ask business-level questions and get reliable answers, fast—turning your data estate into a competitive weapon instead of a maintenance headache.
