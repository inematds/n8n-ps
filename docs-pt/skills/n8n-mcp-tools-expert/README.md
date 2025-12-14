# Especialista em Ferramentas MCP n8n

Guia expert para usar ferramentas n8n-mcp efetivamente.

---

## Propósito

Esta skill ajuda a selecionar e usar as ferramentas MCP corretas para descoberta de nodes, gerenciamento de workflows, validação e execução.

---

## Quando Usar

- Incerto sobre qual ferramenta MCP usar
- Precisa de ajuda com parâmetros de ferramentas
- Quer entender padrões de ferramentas
- Resolvendo problemas de MCP

---

## Referência Rápida de Ferramentas

### Descoberta de Nodes

| Ferramenta | Quando Usar | Exemplo |
|------------|-------------|---------|
| `search_nodes` | Encontrar nodes por palavra-chave | `search_nodes({query: "webhook"})` |
| `list_nodes` | Listar por categoria/pacote | `list_nodes({category: "core"})` |
| `get_node_essentials` | Visão rápida do node (use primeiro!) | `get_node_essentials({nodeName: "nodes-base.slack"})` |
| `get_node_info` | Documentação completa | `get_node_info({nodeName: "nodes-base.slack"})` |
| `get_node_documentation` | Docs legíveis com exemplos | `get_node_documentation({nodeName: "nodes-base.slack"})` |
| `list_ai_tools` | Nodes com capacidade IA | `list_ai_tools()` |

### Gerenciamento de Workflow

| Ferramenta | Quando Usar | Exemplo |
|------------|-------------|---------|
| `n8n_create_workflow` | Criar novo workflow | `n8n_create_workflow({name: "Meu Workflow", nodes: [...], connections: {...}})` |
| `n8n_get_workflow` | Obter workflow por ID | `n8n_get_workflow({workflowId: "abc123"})` |
| `n8n_update_partial_workflow` | Atualizações incrementais | `n8n_update_partial_workflow({workflowId: "abc", addNodes: [...]})` |
| `n8n_update_full_workflow` | Substituição completa | `n8n_update_full_workflow({workflowId: "abc", workflow: {...}})` |
| `n8n_list_workflows` | Listar todos workflows | `n8n_list_workflows()` |
| `n8n_delete_workflow` | Deletar workflow | `n8n_delete_workflow({workflowId: "abc"})` |

### Validação

| Ferramenta | Quando Usar | Exemplo |
|------------|-------------|---------|
| `validate_workflow` | Validar workflow completo | `validate_workflow({workflow: {...}})` |
| `validate_node_operation` | Validar node único | `validate_node_operation({node: {...}})` |
| `validate_workflow_connections` | Verificar apenas conexões | `validate_workflow_connections({...})` |
| `validate_workflow_expressions` | Verificar apenas expressões | `validate_workflow_expressions({...})` |
| `n8n_validate_workflow` | Validar por ID | `n8n_validate_workflow({workflowId: "abc"})` |

### Templates

| Ferramenta | Quando Usar | Exemplo |
|------------|-------------|---------|
| `search_templates` | Pesquisar por palavra-chave | `search_templates({query: "slack webhook"})` |
| `list_node_templates` | Templates usando nodes específicos | `list_node_templates({nodeType: "slack"})` |
| `get_template` | Obter JSON completo | `get_template({templateId: "123"})` |
| `get_templates_for_task` | Templates por tipo de tarefa | `get_templates_for_task({task: "notification"})` |

### Execução

| Ferramenta | Quando Usar | Exemplo |
|------------|-------------|---------|
| `n8n_trigger_webhook_workflow` | Disparar via webhook | `n8n_trigger_webhook_workflow({path: "my-webhook", data: {...}})` |
| `n8n_list_executions` | Histórico de execuções | `n8n_list_executions({workflowId: "abc"})` |
| `n8n_get_execution` | Detalhes de execução | `n8n_get_execution({executionId: "123"})` |
| `n8n_health_check` | Verificar conectividade | `n8n_health_check()` |

---

## Fluxo de Trabalho Típico

### 1. Descobrir Nodes Necessários

```
search_nodes({query: "slack"})
↓
Resultado: nodes-base.slack, nodes-base.slackTrigger
↓
get_node_essentials({nodeName: "nodes-base.slack"})
↓
Visão rápida de operações disponíveis
```

### 2. Encontrar Templates de Exemplo

```
search_templates({query: "slack webhook notification"})
↓
Resultado: Lista de templates relevantes
↓
get_template({templateId: "best-matching-id"})
↓
JSON completo do workflow para referência
```

### 3. Criar Workflow

```
n8n_create_workflow({
  name: "Notificação Webhook → Slack",
  nodes: [...],
  connections: {...}
})
↓
Resultado: {id: "new-workflow-id", ...}
```

### 4. Validar Antes de Ativar

```
validate_workflow({workflow: {...}})
↓
Resultado: {valid: true/false, errors: [...]}
```

### 5. Testar

```
n8n_trigger_webhook_workflow({
  path: "my-webhook",
  data: {test: "data"}
})
↓
n8n_list_executions({workflowId: "abc"})
↓
n8n_get_execution({executionId: "latest"})
```

---

## Padrões Comuns

### Pesquisa → Essenciais → Detalhes

Para descobrir e entender um node:

```
1. search_nodes({query: "termo"})         # Encontrar candidatos
2. get_node_essentials({nodeName: "..."}) # Visão rápida
3. get_node_info({nodeName: "..."})       # Detalhes completos (se necessário)
```

### Template → Modificar → Criar

Para criar workflow baseado em template:

```
1. search_templates({query: "caso de uso"})
2. get_template({templateId: "id"})
3. Modificar workflow conforme necessário
4. n8n_create_workflow({...})
```

### Criar → Validar → Iterar

Para desenvolvimento de workflow:

```
1. n8n_create_workflow({...})
2. validate_workflow({...})
3. Se erros: n8n_update_partial_workflow({...})
4. Repetir até válido
```

---

## Estrutura de Workflow

### Formato Básico

```javascript
{
  name: "Nome do Workflow",
  nodes: [
    {
      id: "uuid-unico",
      name: "Nome do Node",
      type: "nodes-base.tipo",
      position: [x, y],
      parameters: { /* config específica */ }
    }
  ],
  connections: {
    "Nome Node Origem": {
      main: [
        [
          { node: "Nome Node Destino", type: "main", index: 0 }
        ]
      ]
    }
  }
}
```

### Exemplo Completo

```javascript
{
  name: "Webhook para Slack",
  nodes: [
    {
      id: "webhook-1",
      name: "Webhook",
      type: "nodes-base.webhook",
      position: [0, 0],
      parameters: {
        path: "form-submit",
        httpMethod: "POST",
        responseMode: "onReceived"
      }
    },
    {
      id: "slack-1",
      name: "Slack",
      type: "nodes-base.slack",
      position: [200, 0],
      parameters: {
        resource: "message",
        operation: "post",
        channel: "#notifications",
        text: "={{$json.body.message}}"
      },
      credentials: {
        slackApi: { id: "cred-id", name: "Slack Creds" }
      }
    }
  ],
  connections: {
    "Webhook": {
      main: [[{ node: "Slack", type: "main", index: 0 }]]
    }
  }
}
```

---

## Dicas de Troubleshooting

### Ferramenta não encontra o node

- Use nome completo: `nodes-base.slack` não apenas `slack`
- Verifique se o node existe: `search_nodes({query: "nome"})`

### Validação falha

- Verifique campos obrigatórios com `get_node_info`
- Use perfil de validação apropriado
- Veja skill `n8n-validation-expert` para interpretar erros

### Workflow não ativa

- **Ativação requer UI do n8n** - API/MCP não pode ativar workflows
- Workflow é criado inativo por padrão
- Usuário deve ativar manualmente na interface

### Conexões não funcionam

- Verifique nomes dos nodes (case-sensitive)
- Verifique estrutura de conexões
- Use `validate_workflow_connections` para diagnóstico

---

## Integração com Outras Skills

- **n8n Workflow Patterns** - Estruturas de workflow para criar
- **n8n Node Configuration** - Como configurar nodes específicos
- **n8n Expression Syntax** - Expressões em parâmetros
- **n8n Validation Expert** - Interpretar erros de validação
