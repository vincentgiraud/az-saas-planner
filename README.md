# Azure SaaS Planner

A set of AI agent definitions for VS Code / GitHub Copilot that research and generate a cost-optimized, compliance-ready Azure infrastructure stack for SaaS startups.

Give it your industry, region, budget, and user count — it runs 9 specialist agents in parallel and produces a full stack report with Bicep IaC ready to deploy with `azd up`.

## What's Included

```
.github/
├── agents/                  # 10 agent definitions
│   ├── az-saas-planner.agent.md      # Orchestrator — coordinates the 9 specialists
│   ├── compliance-mapper.agent.md    # Maps frameworks (GDPR, SOC2, HIPAA, etc.)
│   ├── workload-profiler.agent.md    # Sizes compute, storage, bandwidth
│   ├── compute-advisor.agent.md      # Compares App Service / Container Apps / Functions / AKS
│   ├── data-advisor.agent.md         # Compares SQL / PostgreSQL / Cosmos DB / storage
│   ├── security-advisor.agent.md     # Entra ID, Key Vault, RBAC, Defender
│   ├── observability-advisor.agent.md# App Insights, Log Analytics, alerts
│   ├── networking-advisor.agent.md   # VNet, Front Door, WAF, CDN, private endpoints
│   ├── cost-validator.agent.md       # Cross-validates pricing, finds free-tier overlap
│   └── growth-advisor.agent.md       # 10x/100x projections, scaling cliffs
├── instructions/
│   └── compliance-stack-config.instructions.md  # Shared config schema & compliance matrix
└── prompts/
    └── find-cost-optimized-stack.prompt.md      # One-click prompt template
```

## Prerequisites

- **VS Code** (or VS Code Insiders) with **GitHub Copilot Chat**
- A Copilot subscription that supports agent mode (Copilot Pro / Business / Enterprise)
- **Azure CLI** (`az`) and **Azure Developer CLI** (`azd`) — for deploying the generated stack
- **Docker Desktop** — for building container images during deployment

## Quick Start

### 1. Copy the `.github/` folder into your project

```bash
# Clone this repo
git clone https://github.com/vincentgiraud/az-saas-planner.git
cd az-saas-planner

# Copy just the agent definitions into your project
cp -r .github/ /path/to/your-project/.github/
```

Or start fresh:

```bash
mkdir my-saas && cd my-saas
git init
git remote add planner https://github.com/vincentgiraud/az-saas-planner.git
git fetch planner template
git checkout planner/template -- .
```

### 2. Open Copilot Chat in agent mode

In VS Code, open the Copilot Chat panel and switch to **Agent** mode (the dropdown at the top of the chat).

### 3. Run the prompt

Use the built-in prompt file:

```
/find-cost-optimized-stack Industry: FinTech, Region: Europe, Budget: $500, Users: 10K
```

Or invoke the orchestrator agent directly:

```
@az-saas-planner Industry: Healthcare, Region: US, Budget: $200, Markets: US+EU, Users: 1K
```

### 4. Walk through the guided discovery

The orchestrator asks you to confirm 3 groups of parameters:

1. **Identity & Compliance** — industry, region, target markets, customer sectors, data sensitivity → auto-resolves which compliance frameworks apply
2. **Workload & Scale** — workload type, expected users, data volume, required services, tech stack
3. **Budget & Operations** — monthly cap, startup stage, team size, SLA target

### 5. Wait for the 9-agent research

After confirmation, the orchestrator runs research in 3 parallel phases:

| Phase | Agents | What they do |
|-------|--------|-------------|
| **1. Foundation** | compliance-mapper, workload-profiler | Map frameworks → Azure requirements, size resources |
| **2. Service-level** | compute, data, security, observability, networking | Compare services, recommend tiers, estimate costs |
| **3. Validation** | cost-validator, growth-advisor | Cross-check pricing, project 10x/100x scaling costs |

### 6. Get your stack report

Output: `docs/stack-report.md` — a comprehensive report containing:

- Recommended Azure services with specific SKUs and tiers
- Monthly cost breakdown (cost-optimized vs. recommended)
- Compliance mapping per service
- Growth projections and scaling cliffs
- Migration paths for tier upgrades

### 7. Deploy (optional)

After the report, you can ask Copilot to generate the infrastructure and deploy:

```
Prepare this stack for deployment to Azure
```

This triggers the `azure-prepare` → `azure-validate` → `azure-deploy` workflow, generating Bicep IaC, `azure.yaml`, Dockerfiles, and application scaffolds — then deploying with `azd up`.

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
