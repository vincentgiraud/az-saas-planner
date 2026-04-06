# Azure SaaS Planner

A set of AI agent definitions for VS Code / GitHub Copilot that take a plain-English project idea and turn it into implementation-ready specs with a cost-optimized, compliance-ready Azure infrastructure stack.

Describe what you want to build — it generates PRD, tech architecture, frontend/backend design, task plan, and an Azure stack report. Then deploy with `azd up`.

## What's Included

This repo contains **16 agent definitions** across **2 pipelines**:

### Pipeline 1: Spec Planner (6 agents)

Generates a complete project specification from a plain-English idea using Spec-Driven Development (SDD). Output is Copilot-native `.instructions.md` files that Copilot loads automatically.

```
.github/
├── agents/
│   ├── spec-planner.agent.md         # Orchestrator — interviews user, coordinates 5 specialists
│   ├── prd-writer.agent.md           # User stories, features, acceptance criteria
│   ├── tech-architect.agent.md       # Stack selection, architecture, directory structure
│   ├── frontend-designer.agent.md    # Component hierarchy, page layouts, design system, state management
│   ├── backend-designer.agent.md     # API endpoints, database schema, auth flows
│   └── task-planner.agent.md         # Phased implementation plan with dependencies
└── instructions/
    └── spec-planner-config.instructions.md  # Shared config, quality standards, interview protocol
```

**Output** (generated when you run the Spec Planner): 5 instruction files written to `.github/instructions/`:

| File | Purpose |
|------|---------|
| `project-prd.instructions.md` | Product requirements, user stories, acceptance criteria |
| `project-tech-architecture.instructions.md` | Tech stack, architecture, directory structure |
| `project-frontend.instructions.md` | Component guidelines, design system, page layouts |
| `project-backend.instructions.md` | API endpoints, database schema, auth flows |
| `project-tasks.instructions.md` | Ordered implementation plan with phases and dependencies |

### Pipeline 2: Stack Planner (10 agents)

Researches and recommends a compliance-ready Azure stack for your SaaS startup.

```
.github/
├── agents/
│   ├── az-saas-planner.agent.md      # Orchestrator — coordinates the 9 specialists
│   ├── compliance-mapper.agent.md    # Maps frameworks (GDPR, SOC2, HIPAA, etc.)
│   ├── workload-profiler.agent.md    # Sizes compute, storage, bandwidth
│   ├── compute-advisor.agent.md      # Compares App Service / Container Apps / Functions / AKS
│   ├── data-advisor.agent.md         # Compares SQL / PostgreSQL / Cosmos DB / storage
│   ├── security-advisor.agent.md     # Entra ID, Key Vault, RBAC, Defender
│   ├── observability-advisor.agent.md  # App Insights, Log Analytics, alerts
│   ├── networking-advisor.agent.md   # VNet, Front Door, WAF, CDN, private endpoints
│   ├── cost-validator.agent.md       # Cross-validates pricing, finds free-tier overlap
│   └── growth-advisor.agent.md       # 10x/100x projections, scaling cliffs
├── instructions/
│   └── compliance-stack-config.instructions.md  # Shared config schema & compliance matrix
└── prompts/
    └── find-cost-optimized-stack.prompt.md      # One-click prompt template
```

### Chained Workflow

The Spec Planner can automatically invoke the Stack Planner when it detects Azure, compliance, or budget signals in your project brief — giving you a full idea-to-infrastructure pipeline in a single prompt.

**Auto-chaining triggers** — the Stack Planner is invoked if your description mentions any of:
- "Azure", "cloud", or a specific Azure service
- A compliance requirement (GDPR, SOC 2, HIPAA, PCI-DSS)
- A regulated industry (FinTech, HealthTech, EdTech, InsurTech, etc.)
- A hosting budget

When chaining activates, the Spec Planner extracts parameters (industry, region, budget, users, tech stack) from the specs it already generated and passes them to the Stack Planner — so you won't be re-asked for information you already provided.

If none of the triggers match, the Spec Planner completes after Phase 3 (task planning) and skips infrastructure research.

## Prerequisites

- **VS Code** (or VS Code Insiders) with **GitHub Copilot Chat**
- A Copilot subscription that supports agent mode (Copilot Pro / Business / Enterprise)
- **Azure CLI** (`az`) and **Azure Developer CLI** (`azd`) — for deploying the generated stack
- **Docker Desktop** — for building container images during deployment

## Quick Start: Spec Planner

### 1. Get the agent definitions

**Option A: Try it directly in this repo** — just open this repo in VS Code. The agents are ready to use immediately. Any generated spec files will be written into this repo's `.github/instructions/` folder — you can move them to your real project later.

**Option B: Copy into your own project** — if you want the agents in an existing project:

```bash
# Clone this repo
git clone https://github.com/vincentgiraud/az-saas-planner.git
cd az-saas-planner

# Copy just the agent definitions into your project
cp -r .github/ /path/to/your-project/.github/
```

### 2. Open Copilot Chat in agent mode

In VS Code, open the Copilot Chat panel and switch to **Agent** mode (the dropdown at the top of the chat).

### 3. Describe your project

Just describe what you want to build in plain English. The system figures out compliance, infrastructure, and architecture automatically.

**E-commerce** (lightweight compliance):
```
@Spec Planner I want to build an online marketplace where independent 
sellers list handmade products. Buyers browse, add to cart, and pay with 
Stripe. Sellers get a dashboard to manage listings and orders. Deploy to 
Azure, budget ~$100/mo, expecting ~1K users in year one.
```

**FinTech** (medium compliance — GDPR auto-detected):
```
@Spec Planner I want to build a multi-tenant invoice management SaaS for 
small businesses in Europe. Users create, send, and track invoices with 
automated VAT calculations. Needs Stripe payments, PDF generation, and 
a client portal. Deploy to Azure, budget ~$150/mo, expecting ~500 users 
in year one.
```

**Healthcare** (strict compliance — HIPAA auto-detected):
```
@Spec Planner I want to build a patient portal where doctors share lab 
results and treatment plans with patients. Patients can message their 
care team, book appointments, and upload medical documents. Needs to 
handle medical records securely. Deploy to Azure, budget ~$200/mo.
```

### 4. Walk through the interview

The orchestrator extracts what it can from your description and asks targeted clarifying questions about auth model, data model, scale expectations, and tech preferences.

### 5. Get your specs

The orchestrator runs 5 sub-agents in 3 phases:

| Phase | Agents | Output |
|-------|--------|--------|
| **1. Foundation** | prd-writer → tech-architect | PRD + architecture (sequential) |
| **2. Domain** | frontend-designer + backend-designer | UI guidelines + API/database design (parallel) |
| **3. Planning** | task-planner | Ordered implementation plan |

If Azure/compliance/budget signals are detected, it chains into the Stack Planner automatically (Phase 4).

### 6. Start building

The specs are written as `.instructions.md` files — Copilot loads them automatically when you edit matching files. Follow the task plan:

```
Implement Task 1.1 from project-tasks
```

## Quick Start: Stack Planner

You can also run the Stack Planner independently.

**Using the slash command** (recommended):

```
/find-cost-optimized-stack Industry: FinTech, Region: Europe, Budget: $500, Users: 10K
```

> **How this works:** The file `.github/prompts/find-cost-optimized-stack.prompt.md` is a [Copilot prompt file](https://code.visualstudio.com/docs/copilot/chat/prompt-files). VS Code automatically discovers `.prompt.md` files in `.github/prompts/` and exposes them as `/slash-commands` in the Chat panel. No configuration needed — just type `/` to see available commands.

**Or invoke the agent directly:**

```
@Azure SaaS Planner Industry: Healthcare, Region: US, Budget: $200, Markets: US+EU, Users: 1K
```

### Walk through the guided discovery

The orchestrator asks you to confirm 3 groups of parameters:

1. **Identity & Compliance** — industry, region, target markets, customer sectors, data sensitivity → auto-resolves which compliance frameworks apply
2. **Workload & Scale** — workload type, expected users, data volume, required services, tech stack
3. **Budget & Operations** — monthly cap, startup stage, team size, SLA target

### Wait for the 9-agent research

After confirmation, the orchestrator runs research in 3 parallel phases:

| Phase | Agents | What they do |
|-------|--------|-------------|
| **1. Foundation** | compliance-mapper, workload-profiler | Map frameworks → Azure requirements, size resources |
| **2. Service-level** | compute, data, security, observability, networking | Compare services, recommend tiers, estimate costs |
| **3. Validation** | cost-validator, growth-advisor | Cross-check pricing, project 10x/100x scaling costs |

### Get your stack report

Output: `docs/stack-report.md` — a comprehensive report containing:

- Recommended Azure services with specific SKUs and tiers
- Monthly cost breakdown (cost-optimized vs. recommended)
- Compliance mapping per service
- Growth projections and scaling cliffs
- Migration paths for tier upgrades

### Deploy (optional)

After the report, you can ask Copilot to generate the infrastructure and deploy:

```
Prepare this stack for deployment to Azure
```

This triggers the `azure-prepare` → `azure-validate` → `azure-deploy` workflow, generating Bicep IaC, `azure.yaml`, Dockerfiles, and application scaffolds — then deploying with `azd up`.

> **Note:** The deployment workflow relies on [Copilot skills](https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode) (`azure-prepare`, `azure-validate`, `azure-deploy`) that are part of the GitHub Copilot for Azure extension — not this repo. Make sure you have the **GitHub Copilot for Azure** extension installed in VS Code before running deployment commands. The agents and stack report in this repo work independently of the deployment skills.

## Example Output

Here's a snippet of what the generated specs look like:

<details>
<summary><strong>project-prd.instructions.md</strong> (excerpt)</summary>

```markdown
---
description: "Product requirements for Handmade Marketplace"
applyTo: "**"
---

## User Stories

### Buyer
- As a buyer, I can browse products by category and search by keyword
- As a buyer, I can add items to my cart and checkout with Stripe
- As a buyer, I can track my order status and view order history

### Seller
- As a seller, I can create and manage product listings with images
- As a seller, I can view my dashboard with sales analytics
- As a seller, I can manage incoming orders and update fulfillment status

## Acceptance Criteria
- Product search returns results in < 500ms for up to 10K products
- Checkout completes Stripe payment in a single page flow
- Seller dashboard loads in < 2s on 3G connections
```

</details>

<details>
<summary><strong>docs/stack-report.md</strong> (excerpt)</summary>

```markdown
## Recommended Stack

| Category        | Service                        | SKU/Tier         | Monthly Cost |
|-----------------|--------------------------------|------------------|--------------|
| Compute         | Azure Container Apps           | Consumption      | $0 (free)    |
| Database        | Azure Database for PostgreSQL  | Burstable B1ms   | $13          |
| Auth            | Entra ID B2C                   | Free (50K MAU)   | $0           |
| Storage         | Azure Blob Storage             | Hot LRS          | $2           |
| Monitoring      | Application Insights           | Free (5GB/mo)    | $0           |
| **Total**       |                                |                  | **$15/mo**   |
```

</details>

## Supported Industries

Healthcare, FinTech, EdTech, E-commerce, LegalTech, GovTech, InsurTech, PropTech, HRTech, RegTech, BioTech, MediaTech, TravelTech, CyberSecurity, General SaaS

## Supported Compliance Frameworks

GDPR, HIPAA, SOC2, PCI-DSS, ISO 27001, FedRAMP, FERPA, CCPA, DORA, NIS2

Framework applicability is auto-resolved from your industry, target markets, customer sectors, revenue, and company size. Conditional frameworks (marked with †) are evaluated against your specific context.

## Customization

- **Add services**: Edit `required_services` in the prompt (auth, payments, email, file-storage, search, queues, caching, CDN, analytics)
- **Change tech stack**: Set `tech_stack` to Node.js, Python, .NET, Java, Go, or Rust
- **Multi-tenancy**: Choose shared, schema-per-tenant, db-per-tenant, or silo
- **Budget**: Set any monthly cap from $0 to uncapped
- **SLA**: best-effort, 99.9%, 99.95%, or 99.99% (the last requires multi-region)

## License

MIT
