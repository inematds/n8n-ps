# n8n Powerhouse

**An intelligent automation development environment combining Claude Code skills with n8n MCP tools for building production-ready workflows.**

Turn Claude into an n8n automation expert that can design, build, validate, and deploy workflows directly to your n8n instance.

---

## What is n8n Powerhouse?

n8n Powerhouse is a collection of **Claude Code skills** that give Claude deep expertise in n8n automation. When you install this in your project, Claude can:

- **Architect** automation solutions (n8n vs Python vs Hybrid decisions)
- **Design** workflows using proven patterns
- **Build** workflows directly in your n8n instance via MCP
- **Configure** nodes with operation-aware guidance
- **Write** Code node logic in JavaScript or Python
- **Validate** workflows before deployment
- **Debug** expression syntax and common errors

---

## Quick Start

### 1. Install n8n MCP

Add the n8n MCP server to Claude Code:

```bash
claude mcp add n8n-mcp-api \
  -e MCP_MODE=stdio \
  -e LOG_LEVEL=error \
  -e DISABLE_CONSOLE_OUTPUT=true \
  -e N8N_API_URL=https://YOUR-INSTANCE.app.n8n.cloud \
  -e N8N_API_KEY=your-api-key-here \
  -- npx -y n8n-mcp
```

**Get your API key:** n8n Dashboard → Settings → API → Create API Key

### 2. Clone This Repo

```bash
git clone https://github.com/promptadvisers/n8n-powerhouse.git
cd n8n-powerhouse
```

### 3. Start Claude Code

```bash
claude
```

Claude now has full n8n expertise via the skills in `.claude/skills/`.

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           USER REQUEST                                   │
│         "I need to automate X with Shopify, HubSpot, and AI"            │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                      PLANNING LAYER (Use First)                          │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │                    n8n-workflow-architect                          │  │
│  │  • Analyze business stack (Shopify, Zoho, HubSpot, etc.)          │  │
│  │  • Decide: n8n vs Python vs Hybrid                                 │  │
│  │  • Evaluate production readiness requirements                      │  │
│  │  • Route to appropriate implementation skills                      │  │
│  └───────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                    ┌───────────────┼───────────────┐
                    ▼               ▼               ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                    IMPLEMENTATION LAYER (Use Second)                     │
│  ┌─────────────────────┐ ┌─────────────────────┐ ┌───────────────────┐  │
│  │ n8n-workflow-       │ │ n8n-node-           │ │ n8n-mcp-tools-    │  │
│  │ patterns            │ │ configuration       │ │ expert            │  │
│  │                     │ │                     │ │                   │  │
│  │ • Webhook processing│ │ • Node setup        │ │ • Tool selection  │  │
│  │ • API integration   │ │ • Property deps     │ │ • Parameter help  │  │
│  │ • Database ops      │ │ • Auth configuration│ │ • MCP patterns    │  │
│  │ • AI agent workflows│ │ • Operation config  │ │                   │  │
│  │ • Scheduled tasks   │ │                     │ │                   │  │
│  └─────────────────────┘ └─────────────────────┘ └───────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                    ┌───────────────┼───────────────┐
                    ▼               ▼               ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                      CODE & SYNTAX LAYER (Use as Needed)                 │
│  ┌─────────────────────┐ ┌─────────────────────┐ ┌───────────────────┐  │
│  │ n8n-code-           │ │ n8n-code-           │ │ n8n-expression-   │  │
│  │ javascript          │ │ python              │ │ syntax            │  │
│  │                     │ │                     │ │                   │  │
│  │ • $input/$json      │ │ • _input/_json      │ │ • {{ }} syntax    │  │
│  │ • $helpers usage    │ │ • Standard library  │ │ • $json access    │  │
│  │ • DateTime handling │ │ • Python limits     │ │ • $node refs      │  │
│  │ • HTTP requests     │ │                     │ │ • Error fixing    │  │
│  └─────────────────────┘ └─────────────────────┘ └───────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                      VALIDATION LAYER (Use Before Deploy)                │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │                    n8n-validation-expert                           │  │
│  │  • Interpret validation errors                                     │  │
│  │  • Fix common issues                                               │  │
│  │  • Validation profiles (minimal, runtime, ai-friendly, strict)    │  │
│  │  • Pre-deployment checklist                                        │  │
│  └───────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                         n8n MCP TOOLS (Execution)                        │
│                                                                          │
│  Discovery          │  Workflow Mgmt      │  Validation & Deploy        │
│  ─────────────────  │  ──────────────────  │  ──────────────────────    │
│  • search_nodes     │  • n8n_create_      │  • validate_workflow        │
│  • get_node_info    │    workflow         │  • validate_node_operation  │
│  • list_nodes       │  • n8n_update_      │  • n8n_validate_workflow    │
│  • get_node_        │    partial_workflow │  • validate_workflow_       │
│    essentials       │  • n8n_get_workflow │    connections              │
│  • list_ai_tools    │  • n8n_list_        │  • validate_workflow_       │
│                     │    workflows        │    expressions              │
│                     │  • n8n_delete_      │                             │
│                     │    workflow         │                             │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## The 8 Skills

### 1. n8n-workflow-architect
**Purpose:** Strategic planning and architecture decisions

**Use when:**
- Starting a new automation project
- Deciding between n8n, Python, or hybrid
- Evaluating if automation is feasible
- Planning production-ready systems

**Key files:**
- `SKILL.md` - Decision framework
- `tool-selection-matrix.md` - n8n vs Python criteria
- `business-stack-analysis.md` - SaaS integration guides
- `production-readiness.md` - Pre-launch checklist

---

### 2. n8n-workflow-patterns
**Purpose:** Proven architectural patterns for workflows

**The 5 Core Patterns:**

| Pattern | Use Case | Example |
|---------|----------|---------|
| **Webhook Processing** | Receive HTTP → Process → Respond | Stripe webhooks, form submissions |
| **HTTP API Integration** | Fetch APIs → Transform → Store | Data sync, API aggregation |
| **Database Operations** | Read/Write/Sync databases | ETL, data migration |
| **AI Agent Workflow** | AI with tools and memory | Chatbots, content generation |
| **Scheduled Tasks** | Recurring automation | Daily reports, cleanup jobs |

**Key files:**
- `SKILL.md` - Pattern overview
- `webhook_processing.md` - Webhook patterns
- `http_api_integration.md` - API integration
- `database_operations.md` - Database patterns
- `ai_agent_workflow.md` - AI agent patterns
- `scheduled_tasks.md` - Scheduled automation

---

### 3. n8n-node-configuration
**Purpose:** Operation-aware node configuration

**Key concepts:**
- **Operation-aware:** Different operations need different fields
- **Property dependencies:** Fields show/hide based on other values
- **Progressive discovery:** Start with essentials, add complexity

**Key files:**
- `SKILL.md` - Configuration workflow
- `DEPENDENCIES.md` - Property visibility rules
- `OPERATION_PATTERNS.md` - Common patterns by node type

---

### 4. n8n-mcp-tools-expert
**Purpose:** Guide for using n8n MCP tools effectively

**Tool categories:**
- **Discovery:** `search_nodes`, `get_node_essentials`, `list_nodes`
- **Workflow Management:** `n8n_create_workflow`, `n8n_update_partial_workflow`
- **Validation:** `validate_workflow`, `validate_node_operation`
- **Templates:** `search_templates`, `get_template`
- **Execution:** `n8n_trigger_webhook_workflow`, `n8n_list_executions`

**Key files:**
- `SKILL.md` - Tool overview
- `SEARCH_GUIDE.md` - Finding nodes and templates
- `WORKFLOW_GUIDE.md` - Creating and managing workflows
- `VALIDATION_GUIDE.md` - Validation strategies

---

### 5. n8n-code-javascript
**Purpose:** Writing JavaScript in n8n Code nodes

**Key syntax:**
```javascript
// Access input data
const items = $input.all();
const firstItem = $json;

// Reference other nodes
const prevData = $node["Previous Node"].json;

// HTTP requests
const response = await $helpers.httpRequest({
  method: 'GET',
  url: 'https://api.example.com/data'
});

// DateTime
const now = DateTime.now();
```

**Key files:**
- `SKILL.md` - JavaScript in n8n
- `DATA_ACCESS.md` - Accessing data patterns
- `BUILTIN_FUNCTIONS.md` - $helpers, DateTime, etc.
- `COMMON_PATTERNS.md` - Frequent use cases
- `ERROR_PATTERNS.md` - Debugging common errors

---

### 6. n8n-code-python
**Purpose:** Writing Python in n8n Code nodes

**Key syntax:**
```python
# Access input data
items = _input.all()
first_item = _json

# Reference other nodes
prev_data = _node["Previous Node"].json

# Standard library available
import json
import datetime
```

**Key files:**
- `SKILL.md` - Python in n8n
- `DATA_ACCESS.md` - Accessing data patterns
- `STANDARD_LIBRARY.md` - Available modules
- `COMMON_PATTERNS.md` - Frequent use cases
- `ERROR_PATTERNS.md` - Debugging common errors

---

### 7. n8n-expression-syntax
**Purpose:** Writing n8n expressions correctly

**Common patterns:**
```javascript
// Access current item
{{ $json.fieldName }}

// Access webhook body
{{ $json.body.email }}

// Reference other nodes
{{ $node["Node Name"].json.field }}

// Conditional
{{ $json.status === 'active' ? 'Yes' : 'No' }}
```

**Key files:**
- `SKILL.md` - Expression syntax guide
- `EXAMPLES.md` - Common expression examples
- `COMMON_MISTAKES.md` - Errors and fixes

---

### 8. n8n-validation-expert
**Purpose:** Validate workflows and fix errors

**Validation profiles:**
| Profile | Use Case |
|---------|----------|
| `minimal` | Required fields only |
| `runtime` | What n8n actually checks |
| `ai-friendly` | Balanced for AI use |
| `strict` | Everything validated |

**Key files:**
- `SKILL.md` - Validation overview
- `ERROR_CATALOG.md` - Common errors and fixes
- `FALSE_POSITIVES.md` - When to ignore warnings

---

## File Structure

```
.claude/
├── CLAUDE.md                          # Main configuration
└── skills/
    ├── n8n-workflow-architect/        # Planning & architecture
    │   ├── SKILL.md
    │   ├── tool-selection-matrix.md
    │   ├── business-stack-analysis.md
    │   └── production-readiness.md
    │
    ├── n8n-workflow-patterns/         # Implementation patterns
    │   ├── SKILL.md
    │   ├── webhook_processing.md
    │   ├── http_api_integration.md
    │   ├── database_operations.md
    │   ├── ai_agent_workflow.md
    │   └── scheduled_tasks.md
    │
    ├── n8n-node-configuration/        # Node setup
    │   ├── SKILL.md
    │   ├── DEPENDENCIES.md
    │   └── OPERATION_PATTERNS.md
    │
    ├── n8n-mcp-tools-expert/          # MCP guidance
    │   ├── SKILL.md
    │   ├── SEARCH_GUIDE.md
    │   ├── WORKFLOW_GUIDE.md
    │   └── VALIDATION_GUIDE.md
    │
    ├── n8n-code-javascript/           # JS Code nodes
    │   ├── SKILL.md
    │   ├── DATA_ACCESS.md
    │   ├── BUILTIN_FUNCTIONS.md
    │   ├── COMMON_PATTERNS.md
    │   └── ERROR_PATTERNS.md
    │
    ├── n8n-code-python/               # Python Code nodes
    │   ├── SKILL.md
    │   ├── DATA_ACCESS.md
    │   ├── STANDARD_LIBRARY.md
    │   ├── COMMON_PATTERNS.md
    │   └── ERROR_PATTERNS.md
    │
    ├── n8n-expression-syntax/         # Expression syntax
    │   ├── SKILL.md
    │   ├── EXAMPLES.md
    │   └── COMMON_MISTAKES.md
    │
    └── n8n-validation-expert/         # Validation & QA
        ├── SKILL.md
        ├── ERROR_CATALOG.md
        └── FALSE_POSITIVES.md
```

---

## How Skills Work Together

### Example: Building an Order Automation

```
1. User: "I need to sync Shopify orders to Zoho CRM"
   │
   ▼
2. n8n-workflow-architect analyzes:
   - Stack: Shopify (OAuth) + Zoho CRM (OAuth)
   - Recommendation: Pure n8n (both have native nodes)
   - Pattern: Webhook Processing
   │
   ▼
3. n8n-workflow-patterns provides:
   - Webhook Processing pattern structure
   - Idempotency strategy (check-before-create)
   - Error handling approach
   │
   ▼
4. n8n-mcp-tools-expert guides:
   - Use search_nodes to find Shopify, Zoho nodes
   - Use n8n_create_workflow to build
   - Use validate_workflow before deploy
   │
   ▼
5. n8n-node-configuration helps:
   - Configure Shopify trigger for orders/create
   - Set up Zoho CRM search + create operations
   │
   ▼
6. n8n-expression-syntax fixes:
   - Webhook data access: $json.body.email
   - Node references: $node["Previous"].json.field
   │
   ▼
7. n8n-validation-expert checks:
   - All required fields present
   - Expressions valid
   - Connections correct
   │
   ▼
8. Deploy to n8n instance via MCP
```

---

## MCP Tools Quick Reference

### Discovery
```bash
search_nodes         # Find nodes by keyword
get_node_essentials  # Quick node overview
get_node_info        # Full documentation
list_nodes           # List by category
list_ai_tools        # AI-capable nodes
```

### Workflow Management
```bash
n8n_create_workflow         # Create new workflow
n8n_get_workflow            # Get by ID
n8n_update_partial_workflow # Incremental updates
n8n_update_full_workflow    # Complete replacement
n8n_list_workflows          # List all
n8n_delete_workflow         # Delete
```

### Validation
```bash
validate_workflow            # Full validation
validate_node_operation      # Single node
validate_workflow_connections # Connections only
validate_workflow_expressions # Expressions only
```

### Templates
```bash
search_templates      # Search by keyword
list_node_templates   # Find by nodes used
get_template          # Get full JSON
get_templates_for_task # Curated by task
```

### Execution
```bash
n8n_trigger_webhook_workflow # Trigger via webhook
n8n_list_executions          # Execution history
n8n_get_execution            # Execution details
```

---

## Example Conversations

### "Build me a Slack notification workflow"

Claude will:
1. Use `n8n-workflow-patterns` → Webhook Processing
2. Use `search_nodes` to find Slack node
3. Use `n8n_create_workflow` to build it
4. Use `validate_workflow` to check
5. Provide activation instructions

### "Should I use n8n or Python for processing 50,000 records?"

Claude will:
1. Use `n8n-workflow-architect` → Tool Selection Matrix
2. Recommend: **Python** (>5,000 records threshold)
3. Suggest: n8n for trigger + Python service for processing

### "Why is my expression not working?"

Claude will:
1. Use `n8n-expression-syntax` to diagnose
2. Check common mistakes (missing `$json.body`, wrong `{{ }}` syntax)
3. Provide corrected expression

---

## Production Readiness

The skills include production guidance:

### Observability
- Error notification workflows
- Execution logging
- Health checks

### Idempotency
- Duplicate webhook handling
- Check-before-create patterns
- Idempotency keys

### Cost Management
- AI API cost calculation
- Caching strategies
- Model right-sizing

### Operational Control
- Kill switches
- Approval queues
- Audit trails

See `.claude/skills/n8n-workflow-architect/production-readiness.md` for the full checklist.

---

## Contributing

To add or improve skills:

1. Fork this repo
2. Add/edit files in `.claude/skills/`
3. Update skill descriptions in `.claude/CLAUDE.md`
4. Submit a PR

### Skill File Structure

Each skill should have:
- `SKILL.md` - Main skill file (loaded when skill is invoked)
- `README.md` - Overview for humans
- Additional `.md` files for detailed topics

---

## License

MIT License - Use freely for your automation projects.

---

## Links

- [n8n Documentation](https://docs.n8n.io/)
- [n8n MCP](https://www.npmjs.com/package/n8n-mcp)
- [Claude Code](https://claude.ai/claude-code)
- [Claude Code Skills](https://docs.anthropic.com/en/docs/claude-code/skills)
