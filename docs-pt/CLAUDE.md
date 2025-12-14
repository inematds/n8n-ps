# n8n Powerhouse - Configuração do Claude

Este projeto é um ambiente de desenvolvimento de automação inteligente que combina ferramentas n8n MCP com skills especializadas para construir workflows prontos para produção.

---

## Arquitetura do Sistema

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         REQUISIÇÃO DO USUÁRIO                            │
│         "Preciso automatizar X com Shopify, HubSpot e IA"               │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                      CAMADA DE PLANEJAMENTO (Use Primeiro)               │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │                    n8n-workflow-architect                          │  │
│  │  • Analisar stack de negócios (Shopify, Zoho, HubSpot, etc.)      │  │
│  │  • Decidir: n8n vs Python vs Híbrido                               │  │
│  │  • Avaliar requisitos de prontidão para produção                   │  │
│  │  • Direcionar para skills de implementação apropriadas             │  │
│  └───────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                    ┌───────────────┼───────────────┐
                    ▼               ▼               ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                    CAMADA DE IMPLEMENTAÇÃO (Use Segundo)                 │
│  ┌─────────────────────┐ ┌─────────────────────┐ ┌───────────────────┐  │
│  │ n8n-workflow-       │ │ n8n-node-           │ │ n8n-mcp-tools-    │  │
│  │ patterns            │ │ configuration       │ │ expert            │  │
│  │                     │ │                     │ │                   │  │
│  │ • Processam. webhook│ │ • Config. de nodes  │ │ • Seleção de tool │  │
│  │ • Integração API    │ │ • Depend. de props  │ │ • Ajuda parâmetros│  │
│  │ • Ops de banco      │ │ • Config. de auth   │ │ • Padrões MCP     │  │
│  │ • Workflows IA      │ │ • Config. operação  │ │                   │  │
│  │ • Tarefas agendadas │ │                     │ │                   │  │
│  └─────────────────────┘ └─────────────────────┘ └───────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                    ┌───────────────┼───────────────┐
                    ▼               ▼               ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                      CAMADA DE CÓDIGO E SINTAXE (Use Conforme Necessário)│
│  ┌─────────────────────┐ ┌─────────────────────┐ ┌───────────────────┐  │
│  │ n8n-code-           │ │ n8n-code-           │ │ n8n-expression-   │  │
│  │ javascript          │ │ python              │ │ syntax            │  │
│  │                     │ │                     │ │                   │  │
│  │ • $input/$json      │ │ • _input/_json      │ │ • sintaxe {{ }}   │  │
│  │ • uso de $helpers   │ │ • Biblioteca padrão │ │ • acesso $json    │  │
│  │ • manejo DateTime   │ │ • Limites Python    │ │ • refs $node      │  │
│  │ • requisições HTTP  │ │                     │ │ • Correção erros  │  │
│  └─────────────────────┘ └─────────────────────┘ └───────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                      CAMADA DE VALIDAÇÃO (Use Antes de Implantar)        │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │                    n8n-validation-expert                           │  │
│  │  • Interpretar erros de validação                                  │  │
│  │  • Corrigir problemas comuns                                       │  │
│  │  • Perfis de validação (minimal, runtime, ai-friendly, strict)    │  │
│  │  • Checklist pré-implantação                                       │  │
│  └───────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                         FERRAMENTAS n8n MCP (Execução)                   │
│                                                                          │
│  Descoberta           │  Gerenc. Workflow     │  Validação & Implantação│
│  ─────────────────    │  ──────────────────   │  ───────────────────────│
│  • search_nodes       │  • n8n_create_        │  • validate_workflow    │
│  • get_node_info      │    workflow           │  • validate_node_       │
│  • list_nodes         │  • n8n_update_        │    operation            │
│  • get_node_          │    partial_workflow   │  • n8n_validate_        │
│    essentials         │  • n8n_get_workflow   │    workflow             │
│  • list_ai_tools      │  • n8n_list_          │  • validate_workflow_   │
│                       │    workflows          │    connections          │
│                       │  • n8n_delete_        │  • validate_workflow_   │
│                       │    workflow           │    expressions          │
│  ─────────────────    │  ──────────────────   │  ───────────────────────│
│  Templates            │  Execução             │  Diagnósticos           │
│  ─────────────────    │  ──────────────────   │  ───────────────────────│
│  • search_templates   │  • n8n_trigger_       │  • n8n_health_check     │
│  • get_template       │    webhook_workflow   │  • n8n_diagnostic       │
│  • list_node_         │  • n8n_list_          │  • get_database_        │
│    templates          │    executions         │    statistics           │
│  • get_templates_     │  • n8n_get_           │                         │
│    for_task           │    execution          │                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Guia de Referência das Skills

### 1. n8n-workflow-architect (Comece Aqui)

**Quando Usar:**
- Usuário descreve seu stack de negócios/tecnologia
- Usuário pergunta "devo usar n8n ou Python?"
- Planejando um novo projeto de automação
- Avaliando viabilidade de automação
- Precisa de orientação de prontidão para produção

**Capacidades Principais:**
- Análise de stack (compatibilidade Shopify, Zoho, HubSpot)
- Matriz de seleção de ferramenta (n8n vs Python vs Híbrido)
- Checklist de prontidão para produção
- Estimativa de custos para recursos de IA

**Ativa Modo de Planejamento Quando:**
- 3+ serviços para integrar
- Automação de alto risco (pagamentos, dados de clientes)
- Processos complexos de múltiplas etapas
- Componentes de IA/ML envolvidos

**Arquivos:**
- `SKILL.md` - Framework de decisão
- `tool-selection-matrix.md` - Critérios detalhados
- `business-stack-analysis.md` - Guias de integração SaaS
- `production-readiness.md` - Checklist pré-lançamento

---

### 2. n8n-workflow-patterns (Arquitetura)

**Quando Usar:**
- Construindo um novo workflow
- Escolhendo estrutura do workflow
- Precisa de orientação de padrão para caso de uso específico

**Os 5 Padrões Principais:**
1. **Processamento de Webhook** - Receber HTTP → Processar → Responder
2. **Integração de API HTTP** - Buscar APIs → Transformar → Armazenar
3. **Operações de Banco de Dados** - Ler/Escrever/Sincronizar bancos de dados
4. **Workflow de Agente IA** - IA com ferramentas e memória
5. **Tarefas Agendadas** - Automação recorrente

**Arquivos:**
- `SKILL.md` - Visão geral dos padrões
- `webhook_processing.md`
- `http_api_integration.md`
- `database_operations.md`
- `ai_agent_workflow.md`
- `scheduled_tasks.md`

---

### 3. n8n-node-configuration (Configuração de Nodes)

**Quando Usar:**
- Configurando operações específicas de nodes
- Entendendo dependências de propriedades
- Determinando campos obrigatórios
- Aprendendo padrões específicos de nodes

**Capacidades Principais:**
- Configuração consciente de operação
- Regras de visibilidade de propriedades
- Configuração de autenticação
- Padrões comuns de configuração

---

### 4. n8n-mcp-tools-expert (Orientação MCP)

**Quando Usar:**
- Incerto sobre qual ferramenta MCP usar
- Precisa de ajuda com parâmetros de ferramentas
- Quer entender padrões de ferramentas
- Resolvendo problemas de MCP

**Capacidades Principais:**
- Orientação de seleção de ferramenta
- Ajuda com formato de parâmetros
- Padrões de uso comuns
- Resolução de erros

---

### 5. n8n-code-javascript (Nodes Code JS)

**Quando Usar:**
- Escrevendo JavaScript em nodes Code
- Usando sintaxe `$input`, `$json`, `$node`
- Fazendo requisições HTTP com `$helpers`
- Trabalhando com datas usando DateTime
- Resolvendo erros de node Code

**Sintaxe Principal:**
```javascript
// Acessar dados de entrada
const items = $input.all();
const firstItem = $json;

// Referenciar outros nodes
const prevData = $node["Previous Node"].json;

// Requisições HTTP
const response = await $helpers.httpRequest({
  method: 'GET',
  url: 'https://api.example.com/data'
});

// DateTime
const now = DateTime.now();
```

---

### 6. n8n-code-python (Nodes Code Python)

**Quando Usar:**
- Escrevendo Python em nodes Code
- Usando sintaxe `_input`, `_json`, `_node`
- Trabalhando com biblioteca padrão
- Entendendo limitações do Python no n8n

**Sintaxe Principal:**
```python
# Acessar dados de entrada
items = _input.all()
first_item = _json

# Referenciar outros nodes
prev_data = _node["Previous Node"].json

# Biblioteca padrão disponível
import json
import datetime
```

---

### 7. n8n-expression-syntax (Expressões)

**Quando Usar:**
- Escrevendo expressões n8n
- Usando sintaxe `{{ }}`
- Acessando variáveis `$json`, `$node`
- Resolvendo erros de expressão
- Trabalhando com dados de webhook

**Padrões Comuns:**
```javascript
// Acessar item atual
{{ $json.fieldName }}

// Acessar body do webhook
{{ $json.body.email }}

// Referenciar outros nodes
{{ $node["Node Name"].json.field }}

// Condicional
{{ $json.status === 'active' ? 'Sim' : 'Não' }}
```

---

### 8. n8n-validation-expert (Qualidade)

**Quando Usar:**
- Encontrando erros de validação
- Antes de implantar workflows
- Entendendo avisos de validação
- Corrigindo problemas de estrutura de operadores

**Perfis de Validação:**
- `minimal` - Apenas campos obrigatórios
- `runtime` - O que o n8n realmente verifica
- `ai-friendly` - Balanceado para uso com IA
- `strict` - Tudo validado

---

## Referência Rápida das Ferramentas MCP

### Descoberta de Nodes
| Ferramenta | Caso de Uso |
|------------|-------------|
| `search_nodes` | Encontrar nodes por palavra-chave ("webhook", "slack") |
| `list_nodes` | Listar por categoria/pacote |
| `get_node_essentials` | Visão rápida do node |
| `get_node_info` | Documentação completa do node |
| `get_node_documentation` | Docs legíveis com exemplos |
| `list_ai_tools` | Listar nodes com capacidade de IA |

### Gerenciamento de Workflow
| Ferramenta | Caso de Uso |
|------------|-------------|
| `n8n_create_workflow` | Criar novo workflow |
| `n8n_get_workflow` | Recuperar workflow por ID |
| `n8n_update_partial_workflow` | Atualizações incrementais (add/remove nodes) |
| `n8n_update_full_workflow` | Substituição completa do workflow |
| `n8n_list_workflows` | Listar todos os workflows |
| `n8n_delete_workflow` | Deletar um workflow |

### Validação
| Ferramenta | Caso de Uso |
|------------|-------------|
| `validate_workflow` | Validação completa do workflow |
| `validate_node_operation` | Validação de node único |
| `validate_workflow_connections` | Verificar apenas conexões |
| `validate_workflow_expressions` | Verificar apenas expressões |
| `n8n_validate_workflow` | Validar por ID do workflow |

### Templates
| Ferramenta | Caso de Uso |
|------------|-------------|
| `search_templates` | Pesquisar por palavra-chave |
| `list_node_templates` | Encontrar templates usando nodes específicos |
| `get_template` | Obter JSON completo do workflow |
| `get_templates_for_task` | Templates curados por tipo de tarefa |

### Execução
| Ferramenta | Caso de Uso |
|------------|-------------|
| `n8n_trigger_webhook_workflow` | Disparar via webhook |
| `n8n_list_executions` | Listar histórico de execuções |
| `n8n_get_execution` | Obter detalhes da execução |

---

## Workflow: Da Ideia à Produção

### Fase 1: Planejamento
```
Requisição do Usuário
    │
    ▼
┌─────────────────────────────────────┐
│ Invocar: n8n-workflow-architect     │
│                                     │
│ 1. Analisar compatibilidade de stack│
│ 2. Decidir n8n vs Python vs Híbrido │
│ 3. Identificar tipo de padrão       │
│ 4. Notar requisitos de produção     │
└─────────────────────────────────────┘
```

### Fase 2: Design
```
Decisão de Arquitetura
    │
    ▼
┌─────────────────────────────────────┐
│ Invocar: n8n-workflow-patterns      │
│                                     │
│ 1. Selecionar padrão apropriado     │
│ 2. Planejar sequência de nodes      │
│ 3. Desenhar tratamento de erros     │
│ 4. Mapear fluxo de dados            │
└─────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────┐
│ Usar MCP: search_nodes, get_node_   │
│           essentials, list_ai_tools │
│                                     │
│ Encontrar e entender nodes          │
│ necessários                         │
└─────────────────────────────────────┘
```

### Fase 3: Implementação
```
Design Completo
    │
    ▼
┌─────────────────────────────────────┐
│ Invocar: n8n-node-configuration     │
│                                     │
│ Configurar cada node corretamente   │
└─────────────────────────────────────┘
    │
    ├─── Precisa código? ───┐
    │                       ▼
    │    ┌─────────────────────────────┐
    │    │ Invocar: n8n-code-javascript│
    │    │      ou: n8n-code-python    │
    │    └─────────────────────────────┘
    │                       │
    ├───────────────────────┘
    │
    ├─── Precisa expressões? ───┐
    │                           ▼
    │    ┌─────────────────────────────┐
    │    │ Invocar: n8n-expression-    │
    │    │          syntax             │
    │    └─────────────────────────────┘
    │                           │
    ├───────────────────────────┘
    │
    ▼
┌─────────────────────────────────────┐
│ Usar MCP: n8n_create_workflow       │
│           n8n_update_partial_       │
│           workflow                  │
│                                     │
│ Construir o workflow real           │
└─────────────────────────────────────┘
```

### Fase 4: Validação
```
Workflow Construído
    │
    ▼
┌─────────────────────────────────────┐
│ Usar MCP: validate_workflow         │
│           validate_node_operation   │
│                                     │
│ Verificar erros                     │
└─────────────────────────────────────┘
    │
    ├─── Erros encontrados? ───┐
    │                          ▼
    │    ┌─────────────────────────────┐
    │    │ Invocar: n8n-validation-    │
    │    │          expert             │
    │    │                             │
    │    │ Interpretar e corrigir erros│
    │    └─────────────────────────────┘
    │                          │
    ├──────────────────────────┘
    │
    ▼
┌─────────────────────────────────────┐
│ Revisar: n8n-workflow-architect     │
│          production-readiness.md    │
│                                     │
│ Checklist final pré-lançamento      │
└─────────────────────────────────────┘
```

### Fase 5: Implantar & Monitorar
```
Validação Aprovada
    │
    ▼
┌─────────────────────────────────────┐
│ Usar MCP: n8n_trigger_webhook_      │
│           workflow (para testes)    │
│           n8n_list_executions       │
│           n8n_get_execution         │
│                                     │
│ Testar e monitorar                  │
└─────────────────────────────────────┘
```

---

## Cenários Comuns

### Cenário 1: "Quero automatizar notificações de pedidos"
```
1. n8n-workflow-architect → Analisar stack Shopify → Recomendar n8n puro
2. n8n-workflow-patterns → Selecionar padrão webhook_processing
3. MCP: search_nodes → Encontrar nodes Shopify, Slack
4. n8n-node-configuration → Configurar trigger Shopify
5. MCP: n8n_create_workflow → Construir workflow
6. MCP: validate_workflow → Verificar erros
7. Implantar
```

### Cenário 2: "Construir um qualificador de leads com IA"
```
1. n8n-workflow-architect → Recomendar híbrido (n8n + node Code para IA)
2. n8n-workflow-patterns → Selecionar padrão ai_agent_workflow
3. MCP: list_ai_tools → Encontrar nodes de IA
4. n8n-code-javascript → Escrever lógica de prompt de IA
5. n8n-expression-syntax → Lidar com dados dinâmicos
6. MCP: n8n_create_workflow → Construir workflow
7. n8n-validation-expert → Corrigir quaisquer problemas
8. Revisar production-readiness.md para gestão de custos de IA
```

### Cenário 3: "Sincronizar 50.000 registros diariamente"
```
1. n8n-workflow-architect → Volume excede limites → Recomendar Python
2. Design: trigger n8n → serviço Python → notificação n8n
3. n8n-workflow-patterns → Selecionar padrão scheduled_tasks
4. Python lida com processamento em lote externamente
5. n8n lida apenas com agendamento e notificações
```

---

## Princípios Chave

### 1. Viabilidade Sobre Possibilidade
Construa sistemas que sobrevivam em produção, não apenas funcionem em demos.

### 2. Ferramenta Certa para o Trabalho
- **n8n**: OAuth, mantenedores não-técnicos, esperas, integrações padrão
- **Python**: Grandes volumes de dados, algoritmos complexos, IA de ponta
- **Híbrido**: O melhor dos dois quando necessário

### 3. Produção Primeiro
Sempre considere: observabilidade, idempotência, gestão de custos, rate limits, controles manuais.

### 4. Validar Antes de Implantar
Nunca pule a validação. Use `n8n-validation-expert` e ferramentas de validação MCP.

### 5. Skills se Complementam
Nenhuma skill funciona isoladamente. Elas formam um pipeline do planejamento à implantação.

---

## Estrutura de Arquivos

```
.claude/
├── CLAUDE.md (este arquivo - versão original em inglês)
└── skills/
    ├── n8n-workflow-architect/   # Planejamento & arquitetura
    ├── n8n-workflow-patterns/    # Padrões de implementação
    ├── n8n-node-configuration/   # Configuração de nodes
    ├── n8n-mcp-tools-expert/     # Orientação MCP
    ├── n8n-code-javascript/      # Nodes Code JS
    ├── n8n-code-python/          # Nodes Code Python
    ├── n8n-expression-syntax/    # Sintaxe de expressões
    └── n8n-validation-expert/    # Validação & QA
```

---

## Início Rápido

Quando um usuário perguntar sobre automação:

1. **Comece com `n8n-workflow-architect`** para entender suas necessidades
2. **Use ferramentas MCP** para descobrir nodes disponíveis
3. **Siga o padrão apropriado** de `n8n-workflow-patterns`
4. **Configure nodes** usando `n8n-node-configuration`
5. **Escreva código/expressões** usando skills específicas de linguagem
6. **Valide tudo** com `n8n-validation-expert` + MCP
7. **Implante com confiança**
