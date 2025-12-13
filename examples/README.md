# Example Workflows

These are example workflows you can ask Claude to build. Just describe what you need and Claude will use the skills and MCP to create them.

---

## Example Prompts

### Webhook Processing

```
Build a webhook that receives form submissions, validates the email,
stores in a database, and sends a Slack notification.
```

### API Integration

```
Create a workflow that fetches data from a REST API every hour,
transforms the response, and updates a Google Sheet.
```

### Database Sync

```
Sync new customers from Shopify to my Postgres database daily.
Handle duplicates by checking email.
```

### AI Agent

```
Build a chatbot that can search our documentation and answer questions.
Use OpenAI for the AI and webhook for receiving messages.
```

### Scheduled Report

```
Every day at 9 AM, fetch yesterday's sales from Shopify,
calculate total revenue and order count, and post a summary to Slack.
```

---

## Pro Tips

### Start with Architecture

Before building, ask Claude:
```
Should I use n8n or Python for [your use case]?
```

Claude will use the `n8n-workflow-architect` skill to analyze your requirements.

### Get Pattern Guidance

Ask for pattern recommendations:
```
What's the best pattern for processing Stripe webhooks?
```

Claude will reference `n8n-workflow-patterns` for proven approaches.

### Validate Before Deploy

Always ask Claude to validate:
```
Validate the workflow you just created.
```

Claude will use MCP validation tools to check for issues.

---

## Workflow Files

If you want to import workflows manually, you can export them from Claude:

```
Export the workflow as JSON so I can import it manually.
```

Claude will provide the workflow JSON that you can import via n8n UI.
