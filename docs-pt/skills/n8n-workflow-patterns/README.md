# Padrões de Workflow n8n

Padrões arquiteturais comprovados para construir workflows n8n.

---

## Propósito

Ensina padrões arquiteturais para construir workflows n8n. Fornece estrutura, boas práticas e abordagens comprovadas para casos de uso comuns.

## Quando Usar

- Construir workflow
- Padrão de workflow
- Arquitetura de workflow
- Estrutura de workflow
- Processamento de webhook
- Integração de API HTTP
- Sync de banco de dados
- Agente de IA
- Chatbot
- Tarefa agendada
- Padrão de automação

---

## Os 5 Padrões Principais

### 1. Processamento de Webhook (Mais Comum - 35% dos workflows)
**Caso de Uso**: Receber requisições HTTP de sistemas externos e processá-las instantaneamente.

```
Webhook → [Validar] → [Transformar] → [Ação] → [Resposta/Notificar]
```

**Gotcha Crítico**: Dados estão aninhados em `$json.body`
```javascript
❌ {{$json.email}}
✅ {{$json.body.email}}
```

**Exemplos**:
- Envios de formulário
- Webhooks de pagamento (Stripe)
- Comandos de chat (Slack)
- Webhooks do GitHub

---

### 2. Integração de API HTTP
**Caso de Uso**: Buscar dados de APIs REST, transformar e armazenar/usar.

```
Trigger → HTTP Request → Transformar → Ação → Error Handler
```

**Conceitos Chave**:
- Autenticação via credenciais (nunca hardcode!)
- Paginação para datasets grandes
- Rate limiting para prevenir bloqueios
- `continueOnFail: true` para tratamento de erros

---

### 3. Operações de Banco de Dados
**Caso de Uso**: Ler/Escrever/Sincronizar dados de banco de dados.

```
Schedule → Query → Transformar → Escrever → Verificar
```

**Boas Práticas**:
- Sempre use queries parametrizadas (prevenção de SQL injection)
- Processamento em lote para datasets grandes
- Acesso read-only para ferramentas de IA
- Tratamento de transações para operações multi-etapas

---

### 4. Workflow de Agente IA
**Caso de Uso**: Agentes de IA com acesso a ferramentas e memória.

```
Trigger → AI Agent (Modelo + Ferramentas + Memória) → Output
```

**8 Tipos de Conexão IA**:
- `ai_languageModel` - Modelos de linguagem (obrigatório)
- `ai_tool` - Ferramentas que o agente pode usar
- `ai_memory` - Memória de conversação
- `ai_outputParser` - Parser de saída
- `ai_retriever` - Recuperação de documentos (RAG)
- `ai_embedding` - Embeddings
- `ai_document` - Loaders de documentos
- `ai_textSplitter` - Divisores de texto

**Importante**: QUALQUER node pode ser uma ferramenta de IA (conecte à porta ai_tool)

---

### 5. Tarefas Agendadas (28% dos workflows)
**Caso de Uso**: Automação recorrente.

```
Schedule → Buscar → Processar → Entregar → Logar
```

**Conceitos Chave**:
- Defina timezone do workflow explicitamente (tratamento de DST)
- Previna execuções sobrepostas (use locks)
- Workflow Error Trigger para alertas
- Processamento em lote para dados grandes

---

## Padrões de Fluxo de Dados

### Fluxo Linear
```
Trigger → Transformar → Ação → Fim
```
**Use quando**: Workflows simples com caminho único

### Fluxo com Branches
```
Trigger → IF → [Caminho True]
            └→ [Caminho False]
```
**Use quando**: Ações diferentes baseadas em condições

### Processamento Paralelo
```
Trigger → [Branch 1] → Merge
       └→ [Branch 2] ↗
```
**Use quando**: Operações independentes que podem rodar simultaneamente

### Padrão de Loop
```
Trigger → Split in Batches → Processar → Loop (até terminar)
```
**Use quando**: Processando datasets grandes em chunks

### Padrão Error Handler
```
Fluxo Principal → [Caminho Sucesso]
              └→ [Error Trigger → Error Handler]
```
**Use quando**: Precisa de workflow separado para tratamento de erros

---

## Checklist de Criação de Workflow

### Fase de Planejamento
- [ ] Identificar o padrão (webhook, API, banco, IA, agendado)
- [ ] Listar nodes necessários (use search_nodes)
- [ ] Entender fluxo de dados (entrada → transformar → saída)
- [ ] Planejar estratégia de tratamento de erros

### Fase de Implementação
- [ ] Criar workflow com trigger apropriado
- [ ] Adicionar nodes de fonte de dados
- [ ] Configurar autenticação/credenciais
- [ ] Adicionar nodes de transformação (Set, Code, IF)
- [ ] Adicionar nodes de output/ação
- [ ] Configurar tratamento de erros

### Fase de Validação
- [ ] Validar configuração de cada node (validate_node_operation)
- [ ] Validar workflow completo (validate_workflow)
- [ ] Testar com dados de amostra
- [ ] Tratar casos de borda (dados vazios, erros)

### Fase de Deploy
- [ ] Revisar configurações do workflow
- [ ] Ativar workflow ⚠️ **Ativação manual necessária na UI do n8n** (API/MCP não pode ativar)
- [ ] Monitorar primeiras execuções
- [ ] Documentar propósito e fluxo de dados do workflow

---

## Estatísticas de Padrões

**Distribuição de Triggers**:
- Webhook: 35% (mais comum)
- Schedule: 28%
- Manual: 22%
- Service triggers: 15%

**Nodes de Transformação**:
- Set: 68%
- Code: 42%
- IF: 38%
- Switch: 18%

**Canais de Saída**:
- HTTP Request: 45%
- Slack: 32%
- Database: 28%
- Email: 24%

**Complexidade**:
- Simples (3-5 nodes): 42%
- Médio (6-10 nodes): 38%
- Complexo (11+ nodes): 20%

---

## Exemplos Rápidos

### Webhook → Slack
```
1. Webhook (path: "form-submit", POST)
2. Set (mapear campos do formulário)
3. Slack (postar mensagem em #notifications)
```

### Relatório Agendado
```
1. Schedule (diário às 9 AM)
2. HTTP Request (buscar analytics)
3. Code (agregar dados)
4. Email (enviar relatório formatado)
5. Error Trigger → Slack (notificar em falha)
```

### Sync de Banco de Dados
```
1. Schedule (a cada 15 minutos)
2. Postgres (query novos registros)
3. IF (verificar se existem registros)
4. MySQL (inserir registros)
5. Postgres (atualizar timestamp de sync)
```

### Assistente IA
```
1. Webhook (receber mensagem de chat)
2. AI Agent
   ├─ OpenAI Chat Model (ai_languageModel)
   ├─ HTTP Request Tool (ai_tool)
   ├─ Database Tool (ai_tool)
   └─ Window Buffer Memory (ai_memory)
3. Webhook Response (enviar resposta da IA)
```

---

## Boas Práticas

### ✅ Faça

- Comece com o padrão mais simples que resolve seu problema
- Planeje a estrutura do workflow antes de construir
- Use tratamento de erros em todos os workflows
- Teste com dados de amostra antes de ativar
- Use nomes descritivos nos nodes
- Documente workflows complexos (campo notes)
- Monitore execuções após deploy

### ❌ Não Faça

- Construir workflows de uma vez (itere! média de 56s entre edições)
- Pular validação antes de ativar
- Ignorar cenários de erro
- Usar padrões complexos quando simples bastam
- Hardcodar credenciais em parâmetros
- Esquecer de tratar casos de dados vazios
- Misturar múltiplos padrões sem limites claros
- Deploy sem testar

---

## Arquivos Detalhados

- `webhook_processing.md` - Padrões de webhook, estrutura de dados, tratamento de resposta
- `http_api_integration.md` - APIs REST, autenticação, paginação, retries
- `database_operations.md` - Queries, sync, transações, processamento em lote
- `ai_agent_workflow.md` - Agentes IA, ferramentas, memória, nodes langchain
- `scheduled_tasks.md` - Schedules cron, timezone, monitoramento

---

## Integração com Outras Skills

- **n8n MCP Tools Expert** - Encontrar e configurar nodes
- **n8n Expression Syntax** - Escrever expressões corretamente
- **n8n Validation Expert** - Validar e corrigir erros
- **n8n Node Configuration** - Configurar operações específicas
