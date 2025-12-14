# Guia de Análise de Stack de Negócios

Integrações SaaS comuns, seu suporte no n8n e recomendações arquiteturais.

---

## Categorias de Stack

### Plataformas de E-Commerce

| Plataforma | Node n8n | Tipo de Auth | Suporte Webhook | Padrão Recomendado |
|------------|----------|--------------|-----------------|---------------------|
| **Shopify** | `nodes-base.shopify` | OAuth | Sim | Processamento Webhook |
| **WooCommerce** | `nodes-base.wooCommerce` | OAuth | Sim | Processamento Webhook |
| **BigCommerce** | `nodes-base.bigCommerce` | OAuth | Sim | Processamento Webhook |
| **Magento** | HTTP Request | API Key | Sim | Integração API HTTP |
| **Stripe** | `nodes-base.stripe` | API Key | Sim | Processamento Webhook |
| **Square** | HTTP Request | OAuth | Sim | Integração API HTTP |

**Automações Comuns de E-commerce:**
1. Pedido → Notificação de fulfillment
2. Novo cliente → Sync com CRM
3. Carrinho abandonado → Disparo de sequência de email
4. Atualização de estoque → Sync multi-canal
5. Reembolso → Ticket de suporte + contabilidade

**Exemplo de Stack: Shopify + Klaviyo + Slack**
```
Veredicto: n8n Puro
Justificativa:
- Todos têm nodes nativos
- Padrões de webhook padrão
- Baixo volume de dados por evento
- OAuth gerenciado automaticamente
```

---

### Sistemas de CRM

| Plataforma | Node n8n | Tipo de Auth | Sync Real-time | Padrão Recomendado |
|------------|----------|--------------|----------------|---------------------|
| **HubSpot** | `nodes-base.hubspot` | OAuth | Webhooks | Processamento Webhook |
| **Salesforce** | `nodes-base.salesforce` | OAuth | Streaming API | Integração API HTTP |
| **Zoho CRM** | `nodes-base.zohoCrm` | OAuth | Webhooks | Processamento Webhook |
| **Pipedrive** | `nodes-base.pipedrive` | API Key | Webhooks | Processamento Webhook |
| **Copper** | HTTP Request | API Key | Webhooks | Integração API HTTP |
| **Close** | HTTP Request | API Key | Webhooks | Integração API HTTP |

**Automações Comuns de CRM:**
1. Formulário de lead → CRM + Notificação de vendas
2. Mudança de estágio de deal → Criação de tarefa
3. Atualização de contato → Sync com lista de email
4. Reunião agendada → Calendário + email de preparação
5. Deal ganho → Disparo de workflow de onboarding

**Exemplo de Stack: HubSpot + Typeform + Notion**
```
Veredicto: n8n Puro
Justificativa:
- OAuth do HubSpot gerenciado nativamente
- Integração webhook do Typeform
- API do Notion para atualizações de banco
- Workflow visual para equipe de vendas
```

---

### Automação de Marketing

| Plataforma | Node n8n | Tipo de Auth | Operações em Lote | Padrão Recomendado |
|------------|----------|--------------|-------------------|---------------------|
| **Mailchimp** | `nodes-base.mailchimp` | OAuth | Sim | Tarefas Agendadas |
| **Klaviyo** | `nodes-base.klaviyo` | API Key | Sim | Processamento Webhook |
| **ActiveCampaign** | `nodes-base.activeCampaign` | API Key | Sim | Integração API HTTP |
| **Brevo (Sendinblue)** | `nodes-base.sendInBlue` | API Key | Sim | Tarefas Agendadas |
| **ConvertKit** | `nodes-base.convertKit` | API Key | Limitado | Integração API HTTP |
| **Customer.io** | HTTP Request | API Key | Sim | Integração API HTTP |

**Automações Comuns de Marketing:**
1. Novo cadastro → Sequência de boas-vindas
2. Compra → Campanha pós-compra
3. Score de engajamento → Segmentação de lista
4. Participação em evento → Sequência de follow-up
5. Carrinho abandonado → Email de recuperação

**Exemplo de Stack: Klaviyo + Shopify + Google Sheets**
```
Veredicto: n8n Puro
Justificativa:
- Auth por API key do Klaviyo (simples)
- Webhooks do Shopify para triggers
- Google Sheets para relatórios (OAuth gerenciado)
- Sem processamento complexo necessário
```

---

### Produtividade & Colaboração

| Plataforma | Node n8n | Tipo de Auth | Real-time | Padrão Recomendado |
|------------|----------|--------------|-----------|---------------------|
| **Notion** | `nodes-base.notion` | OAuth | Polling | Operações de Banco |
| **Airtable** | `nodes-base.airtable` | API Key | Webhooks (pago) | Operações de Banco |
| **Google Sheets** | `nodes-base.googleSheets` | OAuth | Polling | Operações de Banco |
| **Coda** | `nodes-base.coda` | API Key | Webhooks | Operações de Banco |
| **Monday.com** | `nodes-base.mondayCom` | API Key | Webhooks | Processamento Webhook |
| **ClickUp** | `nodes-base.clickUp` | OAuth | Webhooks | Processamento Webhook |
| **Asana** | `nodes-base.asana` | OAuth | Webhooks | Processamento Webhook |

**Automações Comuns de Produtividade:**
1. Tarefa completada → Atualização de status + notificação
2. Atualização de banco → Sync cross-platform
3. Envio de formulário → Criação de tarefa
4. Marco de projeto → Geração de relatório
5. Atribuição de equipe → Calendário + notificação

**Exemplo de Stack: Notion + Slack + Calendar**
```
Veredicto: n8n Puro
Justificativa:
- OAuth do Notion para acesso seguro
- Slack para notificações
- Google Calendar para agendamento
- Padrões de sync de banco de dados padrão
```

---

### Plataformas de Comunicação

| Plataforma | Node n8n | Tipo de Auth | Tratam. Mensagens | Padrão Recomendado |
|------------|----------|--------------|-------------------|---------------------|
| **Slack** | `nodes-base.slack` | OAuth | Slash commands, Events | Processamento Webhook |
| **Discord** | `nodes-base.discord` | OAuth | Webhooks, bots | Processamento Webhook |
| **Microsoft Teams** | `nodes-base.microsoftTeams` | OAuth | Webhooks | Processamento Webhook |
| **Telegram** | `nodes-base.telegram` | API Token | Webhooks | Processamento Webhook |
| **WhatsApp Business** | HTTP Request | API Key | Webhooks | Integração API HTTP |
| **Twilio** | `nodes-base.twilio` | API Key | Webhooks | Processamento Webhook |

**Automações Comuns de Comunicação:**
1. Slash command → Busca de dados + resposta
2. Mensagem em canal → Criação de tarefa
3. Alerta → Notificação multi-canal
4. Envio de formulário → Notificação no chat
5. Relatório agendado → Post no canal

---

### Suporte & Atendimento

| Plataforma | Node n8n | Tipo de Auth | Gerenc. Tickets | Padrão Recomendado |
|------------|----------|--------------|-----------------|---------------------|
| **Zendesk** | `nodes-base.zendesk` | OAuth | Completo | Processamento Webhook |
| **Intercom** | `nodes-base.intercom` | OAuth | Completo | Processamento Webhook |
| **Freshdesk** | `nodes-base.freshdesk` | API Key | Completo | Processamento Webhook |
| **Help Scout** | HTTP Request | API Key | Completo | Integração API HTTP |
| **Crisp** | HTTP Request | API Key | Completo | Integração API HTTP |

**Automações Comuns de Suporte:**
1. Novo ticket → Roteamento por prioridade
2. Escalação de ticket → Notificação ao gerente
3. Violação de SLA → Alerta + auto-escalação
4. Feedback de cliente → Tracking de NPS
5. Ticket resolvido → Pesquisa de follow-up

---

### IA & Analytics

| Plataforma | Node n8n | Tipo de Auth | Rate Limits | Padrão Recomendado |
|------------|----------|--------------|-------------|---------------------|
| **OpenAI** | `nodes-langchain.openAi` | API Key | Sim | Workflow Agente IA |
| **Anthropic** | `nodes-langchain.anthropic` | API Key | Sim | Workflow Agente IA |
| **Google AI** | `nodes-langchain.googleAi` | API Key | Sim | Workflow Agente IA |
| **Pinecone** | HTTP Request | API Key | Sim | Workflow Agente IA |
| **Google Analytics** | `nodes-base.googleAnalytics` | OAuth | Sim | Tarefas Agendadas |
| **Mixpanel** | HTTP Request | API Key | Sim | Integração API HTTP |

**Automações Comuns com IA:**
1. Consulta de cliente → Classificação IA + roteamento
2. Conteúdo recebido → Sumarização IA
3. Formulário de lead → Pontuação de qualificação IA
4. Ticket de suporte → Rascunho de resposta IA
5. Upload de documento → Extração IA + armazenamento

**Exemplo de Stack: OpenAI + HubSpot + Slack**
```
Veredicto: Híbrido
Justificativa:
- HubSpot/Slack via n8n (OAuth)
- Processamento de IA em node Code ou serviço externo
- Prompts complexos podem precisar de Python para gerenciamento
- Tracking de custos importante
```

---

## Framework de Decisão Multi-Stack

### Template de Análise

Quando usuário descrever seu stack, colete:

```markdown
## Inventário de Stack

### Sistemas Primários (Core do Negócio)
1. [Sistema] - [Papel] - [Volume de Dados]
2. ...

### Sistemas Secundários (Funções de Suporte)
1. [Sistema] - [Papel] - [Frequência]
2. ...

### Pontos de Integração Necessários
1. [Sistema A] → [Sistema B]: [Trigger] → [Ação]
2. ...

### Características de Dados
- Registros por dia: [estimativa]
- Pico de carga: [quando]
- Sensibilidade de dados: [PII/financeiro/padrão]

### Capacidades da Equipe
- Nível técnico: [não-técnico/misto/equipe dev]
- Dono da manutenção: [cargo]
```

### Pontuação de Complexidade

| Fator | Baixa (1) | Média (2) | Alta (3) |
|-------|-----------|-----------|----------|
| Número de sistemas | 2-3 | 4-5 | 6+ |
| Integrações OAuth | 0-1 | 2-3 | 4+ |
| Volume de dados (diário) | < 1000 | 1000-10000 | > 10000 |
| Requisitos real-time | Nenhum | Alguns | Crítico |
| Envolvimento IA/ML | Nenhum | Classificação | Generativo |
| Nível técnico da equipe de manutenção | Equipe dev | Mista | Não-técnico |

**Interpretação da Pontuação:**
- 6-9: n8n puro recomendado
- 10-14: Híbrido provavelmente necessário
- 15-18: Componente Python significativo necessário

---

## Combinações de Stack Comuns

### Stack de Startup SaaS
**Stripe + HubSpot + Slack + Notion + Intercom**

```
Veredicto: n8n Puro
Complexidade: Média
Automações Principais:
1. Pagamento → Atualização CRM + Notificação Slack
2. Novo cliente → Workflow de onboarding
3. Ticket de suporte → Roteamento por prioridade
4. Sinal de churn → Workflow de retenção

Padrão: Múltiplos workflows disparados por webhook
```

### Operações de E-commerce
**Shopify + Klaviyo + ShipStation + QuickBooks**

```
Veredicto: n8n Puro (possivelmente híbrido para inventário)
Complexidade: Média-Alta
Automações Principais:
1. Pedido → Fulfillment + notificação ao cliente
2. Envio → Atualização de tracking
3. Devolução → Processamento de reembolso
4. Diário → Sync de vendas para contabilidade

Padrão: Processamento webhook + sync agendado
Aviso: Alto volume pode precisar de processamento em lote
```

### Negócio de Serviços com IA
**Typeform + OpenAI + Airtable + Calendly + Zoom**

```
Veredicto: Híbrido
Complexidade: Alta
Automações Principais:
1. Formulário → Análise IA → Score de qualificação
2. Lead qualificado → Agendamento automático de reunião
3. Reunião → Resumo IA → Atualização CRM
4. Follow-up → Outreach personalizado

Padrão: Workflow de agente IA com checkpoints humanos
Aviso: Custos de IA, necessário estratégia de cache
```

### Pipeline de Dados Enterprise
**Salesforce + Snowflake + Tableau + Slack**

```
Veredicto: Python-primário com triggers n8n
Complexidade: Alta
Automações Principais:
1. Agendado → Extração de dados
2. Transformar → Carregar para warehouse
3. Anomalia detectada → Alerta
4. Diário → Distribuição de relatório

Padrão: Tarefas agendadas + processamento em lote
Aviso: Volume requer streaming Python
```

---

## Pegadinhas de Integração por Plataforma

### Shopify
- Payload de webhook é aninhado
- Rate limit: 2 chamadas/segundo (burst), 40/minuto (sustentado)
- Considere: Sync de inventário pode ser alto volume

### HubSpot
- Lookups de associação requerem múltiplas chamadas
- Rate limit: 100 chamadas/10 segundos
- Considere: Histórico de propriedades pode inflar payloads

### Salesforce
- Queries SOQL têm limites
- Use Bulk API para operações grandes
- Considere: Streaming API para real-time

### Google Sheets
- 300 requests/minuto
- Sem webhooks (precisa de polling)
- Considere: Use Airtable para necessidades real-time

### Slack
- Modo socket vs webhooks
- Rate limits variam por endpoint
- Considere: Thread replies para contexto

### OpenAI/Claude
- Custos de tokens acumulam rápido
- Rate limits por tier
- Considere: Caching, seleção de modelo

---

## Skills Relacionadas

- **n8n-node-configuration** - Configurar nodes de plataformas específicas
- **n8n-workflow-patterns** - Padrões de implementação
- **n8n-mcp-tools-expert** - Encontrar nodes específicos de plataformas
