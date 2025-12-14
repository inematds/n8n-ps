# Especialista em Validação n8n

Interprete erros de validação e guie a correção.

---

## Propósito

Esta skill ajuda a interpretar erros de validação, entender avisos, corrigir problemas comuns e usar perfis de validação apropriados antes de implantar workflows.

---

## Quando Usar

- Encontrando erros de validação
- Antes de implantar workflows
- Entendendo avisos de validação
- Corrigindo problemas de estrutura de operadores

---

## Perfis de Validação

O n8n MCP oferece diferentes perfis de validação:

| Perfil | Descrição | Uso |
|--------|-----------|-----|
| `minimal` | Apenas campos obrigatórios | Desenvolvimento rápido |
| `runtime` | O que o n8n realmente verifica | Pré-deploy |
| `ai-friendly` | Balanceado para uso com IA | Padrão recomendado |
| `strict` | Tudo validado | Workflows críticos |

### Usando Perfis

```javascript
validate_workflow({
  workflow: {...},
  profile: "ai-friendly"
})
```

---

## Tipos de Erro Comuns

### 1. Campo Obrigatório Faltando

**Erro**:
```
Missing required field: channel
```

**Causa**: Node Slack sem canal especificado

**Solução**:
```javascript
{
  resource: "message",
  operation: "post",
  channel: "#notifications",  // Adicionar campo
  text: "Mensagem"
}
```

### 2. Tipo de Dado Inválido

**Erro**:
```
Invalid type: expected string, got number
```

**Causa**: Passando número onde string é esperada

**Solução**:
```javascript
// ERRADO
{ limit: "100" }

// CORRETO
{ limit: 100 }
```

### 3. Conexão Inválida

**Erro**:
```
Invalid connection: Node "X" output cannot connect to Node "Y" input
```

**Causa**: Tentando conectar tipos incompatíveis

**Solução**: Verifique tipos de porta (main vs ai_tool, etc.)

### 4. Node Não Encontrado

**Erro**:
```
Unknown node type: nodes-base.nonexistent
```

**Causa**: Tipo de node incorreto ou não existe

**Solução**: Use `search_nodes` para encontrar nome correto

### 5. Credencial Não Configurada

**Erro**:
```
Credentials not configured for node: Slack
```

**Causa**: Node requer credenciais não especificadas

**Solução**: Adicionar referência de credencial:
```javascript
{
  credentials: {
    slackApi: { id: "cred-id", name: "Slack Creds" }
  }
}
```

---

## Erros de Expressão

### Sintaxe Inválida

**Erro**:
```
Invalid expression syntax: {{ $json.email }
```

**Causa**: Faltando `}}` de fechamento

**Solução**:
```javascript
// ERRADO
{{ $json.email }

// CORRETO
{{ $json.email }}
```

### Variável Não Definida

**Erro**:
```
Cannot read property 'email' of undefined
```

**Causa**: Acessando propriedade de objeto que não existe

**Solução**:
```javascript
// ERRADO
{{ $json.user.email }}

// CORRETO (com optional chaining)
{{ $json.user?.email }}
{{ $json.user?.email || 'N/A' }}
```

### Node Referenciado Não Existe

**Erro**:
```
Node "Webhook" not found
```

**Causa**: Referenciando node que não existe no workflow

**Solução**: Verificar nome exato do node (case-sensitive)

---

## Falsos Positivos

Alguns erros de validação podem ser falsos positivos:

### Credenciais

Validação pode alertar sobre credenciais faltando, mas:
- Credenciais são gerenciadas na UI do n8n
- IDs de credencial podem não estar disponíveis via MCP
- **Ação**: Ignorar se credenciais serão configuradas na UI

### Campos Opcionais

Alguns campos são opcionais mas podem aparecer como "recomendados":
- **Ação**: Avaliar se são realmente necessários para seu caso

### Expressões Dinâmicas

Expressões como `{{ $json.field }}` podem parecer inválidas:
- Validador não consegue avaliar em tempo de build
- **Ação**: Ignorar se expressão está correta

---

## Checklist de Validação

### Antes de Criar Workflow

- [ ] Todos os nodes têm tipo correto (`nodes-base.xxx`)
- [ ] Campos obrigatórios preenchidos
- [ ] Expressões com sintaxe correta `{{ }}`
- [ ] Nomes de nodes únicos
- [ ] Posições definidas para cada node

### Depois de Criar Workflow

- [ ] Executar `validate_workflow`
- [ ] Resolver erros críticos
- [ ] Avaliar avisos (podem ser falsos positivos)
- [ ] Testar com dados de amostra

### Antes de Ativar

- [ ] Credenciais configuradas na UI do n8n
- [ ] Workflow testado manualmente
- [ ] Error handling configurado
- [ ] Monitoramento/alertas configurados

---

## Processo de Correção

### 1. Identificar o Erro

```javascript
const result = validate_workflow({workflow});
console.log(result.errors);
```

### 2. Localizar o Problema

- Qual node?
- Qual campo?
- Qual valor atual?

### 3. Aplicar Correção

```javascript
n8n_update_partial_workflow({
  workflowId: "abc",
  updateNodes: [
    {
      name: "Node Problemático",
      parameters: {
        // campo corrigido
      }
    }
  ]
})
```

### 4. Revalidar

```javascript
validate_workflow({workflow: updatedWorkflow})
```

---

## Ferramentas de Validação

### validate_workflow

Valida workflow completo:
```javascript
validate_workflow({
  workflow: workflowObject,
  profile: "ai-friendly"
})
```

### validate_node_operation

Valida node específico:
```javascript
validate_node_operation({
  node: nodeObject
})
```

### validate_workflow_connections

Verifica apenas conexões:
```javascript
validate_workflow_connections({
  connections: connectionsObject
})
```

### validate_workflow_expressions

Verifica apenas expressões:
```javascript
validate_workflow_expressions({
  workflow: workflowObject
})
```

---

## Dicas de Troubleshooting

### Erro Persistente

1. Verifique documentação do node com `get_node_info`
2. Compare com template funcional via `search_templates`
3. Simplifique configuração ao mínimo necessário

### Muitos Erros

1. Comece pelo primeiro erro (pode causar os outros)
2. Valide nodes individualmente
3. Reconstrua workflow gradualmente

### Erro Não Faz Sentido

1. Pode ser falso positivo
2. Verifique se perfil de validação está apropriado
3. Teste o workflow mesmo assim (n8n pode aceitar)

---

## Integração com Outras Skills

- **n8n MCP Tools Expert** - Ferramentas de validação
- **n8n Node Configuration** - Configuração correta de nodes
- **n8n Expression Syntax** - Corrigir erros de expressão
- **n8n Workflow Patterns** - Estruturas válidas de workflow
