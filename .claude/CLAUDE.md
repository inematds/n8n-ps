# n8n Powerhouse - Claude Configuration

This project is an intelligent automation development environment combining n8n MCP tools with specialized skills for building production-ready workflows.

---

## System Architecture

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
│  ─────────────────  │  ──────────────────  │  ──────────────────────    │
│  Templates          │  Execution          │  Diagnostics                │
│  ─────────────────  │  ──────────────────  │  ──────────────────────    │
│  • search_templates │  • n8n_trigger_     │  • n8n_health_check         │
│  • get_template     │    webhook_workflow │  • n8n_diagnostic           │
│  • list_node_       │  • n8n_list_        │  • get_database_statistics  │
│    templates        │    executions       │                             │
│  • get_templates_   │  • n8n_get_         │                             │
│    for_task         │    execution        │                             │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Skill Reference Guide

### 1. n8n-workflow-architect (Start Here)

**When to Use:**
- User describes their business/tech stack
- User asks "should I use n8n or Python?"
- Planning a new automation project
- Evaluating feasibility of automation
- Need production readiness guidance

**Key Capabilities:**
- Stack analysis (Shopify, Zoho, HubSpot compatibility)
- Tool selection matrix (n8n vs Python vs Hybrid)
- Production readiness checklist
- Cost estimation for AI features

**Triggers Plan Mode When:**
- 3+ services to integrate
- High-stakes automation (payments, customer data)
- Complex multi-step processes
- AI/ML components involved

**Files:**
- `SKILL.md` - Decision framework
- `tool-selection-matrix.md` - Detailed criteria
- `business-stack-analysis.md` - SaaS integration guides
- `production-readiness.md` - Pre-launch checklist

---

### 2. n8n-workflow-patterns (Architecture)

**When to Use:**
- Building a new workflow
- Choosing workflow structure
- Need pattern guidance for specific use case

**The 5 Core Patterns:**
1. **Webhook Processing** - Receive HTTP → Process → Respond
2. **HTTP API Integration** - Fetch APIs → Transform → Store
3. **Database Operations** - Read/Write/Sync databases
4. **AI Agent Workflow** - AI with tools and memory
5. **Scheduled Tasks** - Recurring automation

**Files:**
- `SKILL.md` - Pattern overview
- `webhook_processing.md`
- `http_api_integration.md`
- `database_operations.md`
- `ai_agent_workflow.md`
- `scheduled_tasks.md`

---

### 3. n8n-node-configuration (Node Setup)

**When to Use:**
- Configuring specific node operations
- Understanding property dependencies
- Determining required fields
- Learning node-specific patterns

**Key Capabilities:**
- Operation-aware configuration
- Property visibility rules
- Authentication setup
- Common configuration patterns

---

### 4. n8n-mcp-tools-expert (MCP Guidance)

**When to Use:**
- Unsure which MCP tool to use
- Need help with tool parameters
- Want to understand tool patterns
- Troubleshooting MCP issues

**Key Capabilities:**
- Tool selection guidance
- Parameter format help
- Common usage patterns
- Error resolution

---

### 5. n8n-code-javascript (JS Code Nodes)

**When to Use:**
- Writing JavaScript in Code nodes
- Using `$input`, `$json`, `$node` syntax
- Making HTTP requests with `$helpers`
- Working with dates using DateTime
- Troubleshooting Code node errors

**Key Syntax:**
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

---

### 6. n8n-code-python (Python Code Nodes)

**When to Use:**
- Writing Python in Code nodes
- Using `_input`, `_json`, `_node` syntax
- Working with standard library
- Understanding Python limitations in n8n

**Key Syntax:**
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

---

### 7. n8n-expression-syntax (Expressions)

**When to Use:**
- Writing n8n expressions
- Using `{{ }}` syntax
- Accessing `$json`, `$node` variables
- Troubleshooting expression errors
- Working with webhook data

**Common Patterns:**
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

---

### 8. n8n-validation-expert (Quality)

**When to Use:**
- Encountering validation errors
- Before deploying workflows
- Understanding validation warnings
- Fixing operator structure issues

**Validation Profiles:**
- `minimal` - Required fields only
- `runtime` - What n8n actually checks
- `ai-friendly` - Balanced for AI use
- `strict` - Everything validated

---

## MCP Tools Quick Reference

### Node Discovery
| Tool | Use Case |
|------|----------|
| `search_nodes` | Find nodes by keyword ("webhook", "slack") |
| `list_nodes` | List by category/package |
| `get_node_essentials` | Quick node overview |
| `get_node_info` | Full node documentation |
| `get_node_documentation` | Readable docs with examples |
| `list_ai_tools` | List AI-capable nodes |

### Workflow Management
| Tool | Use Case |
|------|----------|
| `n8n_create_workflow` | Create new workflow |
| `n8n_get_workflow` | Retrieve workflow by ID |
| `n8n_update_partial_workflow` | Incremental updates (add/remove nodes) |
| `n8n_update_full_workflow` | Complete workflow replacement |
| `n8n_list_workflows` | List all workflows |
| `n8n_delete_workflow` | Delete a workflow |

### Validation
| Tool | Use Case |
|------|----------|
| `validate_workflow` | Full workflow validation |
| `validate_node_operation` | Single node validation |
| `validate_workflow_connections` | Check connections only |
| `validate_workflow_expressions` | Check expressions only |
| `n8n_validate_workflow` | Validate by workflow ID |

### Templates
| Tool | Use Case |
|------|----------|
| `search_templates` | Search by keyword |
| `list_node_templates` | Find templates using specific nodes |
| `get_template` | Get full workflow JSON |
| `get_templates_for_task` | Curated templates by task type |

### Execution
| Tool | Use Case |
|------|----------|
| `n8n_trigger_webhook_workflow` | Trigger via webhook |
| `n8n_list_executions` | List execution history |
| `n8n_get_execution` | Get execution details |

---

## Workflow: From Idea to Production

### Phase 1: Planning
```
User Request
    │
    ▼
┌─────────────────────────────────────┐
│ Invoke: n8n-workflow-architect      │
│                                     │
│ 1. Analyze stack compatibility      │
│ 2. Decide n8n vs Python vs Hybrid   │
│ 3. Identify pattern type            │
│ 4. Note production requirements     │
└─────────────────────────────────────┘
```

### Phase 2: Design
```
Architecture Decision
    │
    ▼
┌─────────────────────────────────────┐
│ Invoke: n8n-workflow-patterns       │
│                                     │
│ 1. Select appropriate pattern       │
│ 2. Plan node sequence               │
│ 3. Design error handling            │
│ 4. Map data flow                    │
└─────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────┐
│ Use MCP: search_nodes, get_node_    │
│          essentials, list_ai_tools  │
│                                     │
│ Find and understand required nodes  │
└─────────────────────────────────────┘
```

### Phase 3: Implementation
```
Design Complete
    │
    ▼
┌─────────────────────────────────────┐
│ Invoke: n8n-node-configuration      │
│                                     │
│ Configure each node properly        │
└─────────────────────────────────────┘
    │
    ├─── Code needed? ───┐
    │                    ▼
    │    ┌─────────────────────────────┐
    │    │ Invoke: n8n-code-javascript │
    │    │     or: n8n-code-python     │
    │    └─────────────────────────────┘
    │                    │
    ├────────────────────┘
    │
    ├─── Expressions needed? ───┐
    │                           ▼
    │    ┌─────────────────────────────┐
    │    │ Invoke: n8n-expression-     │
    │    │         syntax              │
    │    └─────────────────────────────┘
    │                           │
    ├───────────────────────────┘
    │
    ▼
┌─────────────────────────────────────┐
│ Use MCP: n8n_create_workflow        │
│          n8n_update_partial_workflow│
│                                     │
│ Build the actual workflow           │
└─────────────────────────────────────┘
```

### Phase 4: Validation
```
Workflow Built
    │
    ▼
┌─────────────────────────────────────┐
│ Use MCP: validate_workflow          │
│          validate_node_operation    │
│                                     │
│ Check for errors                    │
└─────────────────────────────────────┘
    │
    ├─── Errors found? ───┐
    │                     ▼
    │    ┌─────────────────────────────┐
    │    │ Invoke: n8n-validation-     │
    │    │         expert              │
    │    │                             │
    │    │ Interpret and fix errors    │
    │    └─────────────────────────────┘
    │                     │
    ├─────────────────────┘
    │
    ▼
┌─────────────────────────────────────┐
│ Review: n8n-workflow-architect      │
│         production-readiness.md     │
│                                     │
│ Final pre-launch checklist          │
└─────────────────────────────────────┘
```

### Phase 5: Deploy & Monitor
```
Validation Passed
    │
    ▼
┌─────────────────────────────────────┐
│ Use MCP: n8n_trigger_webhook_       │
│          workflow (for testing)     │
│          n8n_list_executions        │
│          n8n_get_execution          │
│                                     │
│ Test and monitor                    │
└─────────────────────────────────────┘
```

---

## Common Scenarios

### Scenario 1: "I want to automate order notifications"
```
1. n8n-workflow-architect → Analyze Shopify stack → Recommend pure n8n
2. n8n-workflow-patterns → Select webhook_processing pattern
3. MCP: search_nodes → Find Shopify, Slack nodes
4. n8n-node-configuration → Configure Shopify trigger
5. MCP: n8n_create_workflow → Build workflow
6. MCP: validate_workflow → Check for errors
7. Deploy
```

### Scenario 2: "Build an AI lead qualifier"
```
1. n8n-workflow-architect → Recommend hybrid (n8n + Code node for AI)
2. n8n-workflow-patterns → Select ai_agent_workflow pattern
3. MCP: list_ai_tools → Find AI nodes
4. n8n-code-javascript → Write AI prompt logic
5. n8n-expression-syntax → Handle dynamic data
6. MCP: n8n_create_workflow → Build workflow
7. n8n-validation-expert → Fix any issues
8. Review production-readiness.md for AI cost management
```

### Scenario 3: "Sync 50,000 records daily"
```
1. n8n-workflow-architect → Volume exceeds limits → Recommend Python
2. Design: n8n trigger → Python service → n8n notification
3. n8n-workflow-patterns → Select scheduled_tasks pattern
4. Python handles batch processing externally
5. n8n handles scheduling and notifications only
```

---

## Key Principles

### 1. Viability Over Possibility
Build systems that survive production, not just work in demos.

### 2. Right Tool for the Job
- **n8n**: OAuth, non-tech maintainers, waits, standard integrations
- **Python**: Large data, complex algorithms, cutting-edge AI
- **Hybrid**: Best of both when needed

### 3. Production First
Always consider: observability, idempotency, cost management, rate limits, manual overrides.

### 4. Validate Before Deploy
Never skip validation. Use `n8n-validation-expert` and MCP validation tools.

### 5. Skills Complement Each Other
No skill works in isolation. They form a pipeline from planning to deployment.

---

## File Structure

```
.claude/
├── CLAUDE.md (this file)
└── skills/
    ├── n8n-workflow-architect/   # Planning & architecture
    ├── n8n-workflow-patterns/    # Implementation patterns
    ├── n8n-node-configuration/   # Node setup
    ├── n8n-mcp-tools-expert/     # MCP guidance
    ├── n8n-code-javascript/      # JS Code nodes
    ├── n8n-code-python/          # Python Code nodes
    ├── n8n-expression-syntax/    # Expression syntax
    └── n8n-validation-expert/    # Validation & QA
```

---

## Quick Start

When a user asks about automation:

1. **Start with `n8n-workflow-architect`** to understand their needs
2. **Use MCP tools** to discover available nodes
3. **Follow the appropriate pattern** from `n8n-workflow-patterns`
4. **Configure nodes** using `n8n-node-configuration`
5. **Write code/expressions** using language-specific skills
6. **Validate everything** with `n8n-validation-expert` + MCP
7. **Deploy with confidence**
