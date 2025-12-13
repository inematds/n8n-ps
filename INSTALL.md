# Installation Guide

## Prerequisites

- [Claude Code](https://claude.ai/claude-code) installed
- An n8n instance (cloud or self-hosted)
- Node.js 18+ (for npx)

---

## Step 1: Clone This Repository

```bash
git clone https://github.com/promptadvisers/n8n-powerhouse.git
cd n8n-powerhouse
```

---

## Step 2: Get Your n8n API Key

1. Log into your n8n instance
2. Go to **Settings** (gear icon in sidebar)
3. Click **API**
4. Click **Create API Key**
5. Copy the generated key

---

## Step 3: Install n8n MCP

Run this command, replacing the placeholders:

```bash
claude mcp add n8n-mcp-api \
  -e MCP_MODE=stdio \
  -e LOG_LEVEL=error \
  -e DISABLE_CONSOLE_OUTPUT=true \
  -e N8N_API_URL=https://YOUR-INSTANCE.app.n8n.cloud \
  -e N8N_API_KEY=your-api-key-here \
  -- npx -y n8n-mcp
```

### For n8n Cloud

Replace `YOUR-INSTANCE` with your n8n cloud subdomain:
```
N8N_API_URL=https://mycompany.app.n8n.cloud
```

### For Self-Hosted n8n

Use your n8n instance URL:
```
N8N_API_URL=https://n8n.mycompany.com
```

---

## Step 4: Verify Installation

Start Claude Code in the project directory:

```bash
claude
```

Then ask Claude:
```
What n8n skills do you have available?
```

Claude should list the 8 skills from the `.claude/skills/` directory.

---

## Step 5: Test MCP Connection

Ask Claude:
```
List my n8n workflows
```

If configured correctly, Claude will use the MCP to list workflows from your n8n instance.

---

## Troubleshooting

### "MCP not found" or "n8n-mcp-api not available"

1. Check that the MCP was added:
   ```bash
   claude mcp list
   ```

2. Verify environment variables are set correctly

3. Try removing and re-adding:
   ```bash
   claude mcp remove n8n-mcp-api
   claude mcp add n8n-mcp-api ...
   ```

### "API key invalid" or "401 Unauthorized"

1. Regenerate API key in n8n Settings → API
2. Check for trailing spaces in the key
3. Ensure the key hasn't expired

### "Cannot connect to n8n"

1. Verify N8N_API_URL is correct
2. Check that your n8n instance is running
3. For self-hosted: ensure API is enabled in n8n settings

---

## Updating

To get the latest skills:

```bash
cd n8n-powerhouse
git pull origin main
```

The skills will be automatically available next time you start Claude Code.

---

## Uninstalling

Remove the MCP:
```bash
claude mcp remove n8n-mcp-api
```

Delete the repo:
```bash
rm -rf n8n-powerhouse
```
