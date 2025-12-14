# Checklist de Prontidão para Produção

Orientação detalhada para implantar automação que sobrevive ao contato com a realidade.

---

## A Lacuna da Produção

> A distância entre um workflow que funciona em testes e um que roda confiavelmente em produção é maior do que a maioria das pessoas imagina.

Este documento cobre as preocupações operacionais que separam automação profissional de amadorismo.

---

## 1. Observabilidade

### Por que Importa

Comportamento padrão: **Falha silenciosa**
- Workflow dá erro às 2:47 da manhã
- Ninguém sabe até segunda-feira
- Reclamações de clientes revelam o problema
- Horas de processamento perdidas
- Sem ideia de quantas execuções falharam

### Componentes Necessários

#### A. Workflow de Notificação de Erros

Todo workflow de produção precisa de um tratador de erros.

```
Error Trigger (para workflow principal)
    │
    ▼
┌─────────────────────────────┐
│ Preparar Notificação Erro   │
│ - Nome do workflow          │
│ - Mensagem de erro          │
│ - Timestamp                 │
│ - Link da execução          │
└─────────────────────────────┘
    │
    ▼
┌─────────────────────────────┐
│ Enviar para Canal Monitorado│
│ - Slack                     │
│ - Email                     │
│ - PagerDuty                 │
└─────────────────────────────┘
```

**Implementação:**
```javascript
// No workflow Error Trigger - node Set
{
  "workflow": "{{ $json.workflow.name }}",
  "error": "{{ $json.execution.error.message }}",
  "timestamp": "{{ $now.toISO() }}",
  "execution_url": "{{ $json.execution.url }}",
  "severity": "{{ $json.workflow.name.includes('critical') ? 'high' : 'normal' }}"
}
```

#### B. Log de Execuções

Crie uma tabela dedicada de logging:

```sql
CREATE TABLE workflow_executions (
  id SERIAL PRIMARY KEY,
  workflow_id VARCHAR(255),
  workflow_name VARCHAR(255),
  status VARCHAR(50),  -- success, failure, running
  started_at TIMESTAMP,
  finished_at TIMESTAMP,
  error_message TEXT,
  input_summary JSONB,
  execution_url VARCHAR(500),
  created_at TIMESTAMP DEFAULT NOW()
);
```

**Registre no início e fim do workflow:**
- Início: Insert com status='running'
- Sucesso: Update status='success', set finished_at
- Falha: Update status='failure', set error_message

#### C. Health Checks

Workflow de heartbeat diário para sistemas críticos:

```
Schedule (diário 6 AM)
    │
    ├─► Verificar validade de credenciais
    │   └─► Chamada API de teste para cada serviço
    │
    ├─► Verificar conexões de banco de dados
    │   └─► Query SELECT simples
    │
    ├─► Verificar saúde de APIs externas
    │   └─► Endpoints de status
    │
    └─► Reportar Resultados
        ├─► Tudo saudável → Silêncio (ou Slack opcional)
        └─► Qualquer falha → Alertar imediatamente
```

#### D. Alertas Estruturados

| Severidade | Tempo de Resposta | Canal | Exemplos |
|------------|------------------|-------|----------|
| Crítico | Imediato | PagerDuty/Telefone | Falha de processamento de pagamento |
| Alto | Dentro de 1 hora | Slack + Email | Falha de sync de dados de cliente |
| Médio | Mesmo dia útil | Slack | Falha de relatório interno |
| Baixo | Próximo dia útil | Apenas log | Skip de notificação não-crítica |

---

## 2. Idempotência

### Por que Importa

Triggers do mundo real disparam múltiplas vezes:
- Webhooks tentam novamente em resposta lenta
- Usuários dão duplo clique em submit
- Soluços de rede causam duplicatas

**Consequências de workflows não-idempotentes:**
- Entradas duplicadas no CRM (embaraçoso)
- Faturas duplicadas (erode confiança)
- Cobranças duplicadas (responsabilidade legal)

### Padrões de Implementação

#### A. Identificação Única de Requisição

A maioria dos webhooks inclui um ID único. Armazene e verifique.

```javascript
// No início do workflow
const requestId = $json.body.request_id || $json.headers['x-request-id'];

// Verificar se já foi processado
const existing = await $getWorkflowStaticData('global');
if (existing.processedIds?.includes(requestId)) {
  return []; // Pular - já processado
}

// Após processamento bem-sucedido
const staticData = await $getWorkflowStaticData('global');
staticData.processedIds = staticData.processedIds || [];
staticData.processedIds.push(requestId);
// Manter apenas últimos 1000 para prevenir bloat de memória
if (staticData.processedIds.length > 1000) {
  staticData.processedIds = staticData.processedIds.slice(-1000);
}
```

Para tracking persistente (recomendado para produção):

```sql
CREATE TABLE processed_requests (
  request_id VARCHAR(255) PRIMARY KEY,
  workflow_name VARCHAR(255),
  processed_at TIMESTAMP DEFAULT NOW(),
  result_summary JSONB
);

-- Antes de processar
SELECT 1 FROM processed_requests WHERE request_id = $1;
-- Se existe, retornar resultado em cache ou pular

-- Após processar
INSERT INTO processed_requests (request_id, workflow_name, result_summary)
VALUES ($1, $2, $3)
ON CONFLICT (request_id) DO NOTHING;
```

#### B. Verificar-Antes-Criar

```javascript
// Antes de criar um contato no CRM
const existingContact = await hubspotApi.searchContacts({
  email: newLead.email
});

if (existingContact.results.length > 0) {
  // Atualizar existente ao invés de criar duplicata
  return await hubspotApi.updateContact(existingContact.results[0].id, newLead);
} else {
  return await hubspotApi.createContact(newLead);
}
```

#### C. Operações Upsert

Quando disponível, use "update or create":

```sql
-- PostgreSQL
INSERT INTO customers (email, name, updated_at)
VALUES ($1, $2, NOW())
ON CONFLICT (email)
DO UPDATE SET name = $2, updated_at = NOW();
```

```javascript
// Airtable
// Use Update Record com "Upsert" habilitado
```

#### D. Chaves de Idempotência para APIs

APIs de pagamento especialmente suportam isso:

```javascript
// Exemplo Stripe
const paymentIntent = await stripe.paymentIntents.create({
  amount: 2000,
  currency: 'usd',
  idempotency_key: `order_${orderId}_payment`
});
// Mesma chave = mesmo resultado, sem cobrança duplicada
```

### O Teste de Idempotência

> "Se este webhook disparar três vezes seguidas com dados idênticos, o que acontece?"

- ✅ Mesmo resultado, registro único, cobrança única
- ❌ Três registros, três cobranças

---

## 3. Gestão de Custos

### O Imposto da IA

Chamada única GPT-4: ~$0.03
10.000 envios de formulário/mês: $300/mês contínuo

Isso não é um argumento contra IA. É um argumento para tratar custos de IA como preocupações arquiteturais de primeira classe.

### Estratégias de Controle de Custos

#### A. Calcular Antes de Construir

```markdown
## Projeção de Custos IA: Qualificação de Leads

Leads mensais: 5.000
Processamento por lead:
- Classificação: 1 chamada @ $0.01 (GPT-3.5)
- Análise de pontuação: 1 chamada @ $0.03 (GPT-4)
- Geração de resumo: 1 chamada @ $0.03 (GPT-4)

Custo IA mensal: 5.000 × $0.07 = $350/mês

Alternativa com cache (60% taxa de acerto):
Chamadas reais: 5.000 × 0.4 = 2.000
Custo IA mensal: 2.000 × $0.07 = $140/mês
```

Compartilhe projeções com stakeholders ANTES de construir.

#### B. Questionar a Necessidade

| Tarefa | IA Necessária? | Alternativa |
|--------|----------------|-------------|
| Classificação por palavra-chave | Talvez | Matching de palavras, tabela de lookup |
| Categorização de email | Talvez | Roteamento baseado em regras |
| Validação de formato de telefone | Não | Regex |
| Análise de sentimento | Geralmente | Pontuação simples ou pular |
| Sumarização de conteúdo | Sim | - |
| Raciocínio complexo | Sim | - |

#### C. Cache Agressivo

```javascript
// Sumarização de documento com cache
const documentHash = crypto.createHash('md5')
  .update(document.content)
  .digest('hex');

// Verificar cache
const cached = await db.query(
  'SELECT summary FROM document_cache WHERE hash = $1',
  [documentHash]
);

if (cached.rows.length > 0) {
  return cached.rows[0].summary;
}

// Gerar e cachear
const summary = await openai.summarize(document.content);
await db.query(
  'INSERT INTO document_cache (hash, summary) VALUES ($1, $2)',
  [documentHash, summary]
);
return summary;
```

#### D. Dimensionar Modelos Corretamente

| Tarefa | Modelo | Custo Aprox. |
|--------|--------|--------------|
| Classificação simples | GPT-3.5 / Haiku | ~$0.001 |
| Análise padrão | GPT-4o-mini / Sonnet | ~$0.01 |
| Raciocínio complexo | GPT-4 / Opus | ~$0.05 |

Não use Opus para tarefas que Haiku consegue lidar.

#### E. Monitorar Gastos

Configure alertas de custo:
- Tracking de uso diário
- Notificações de limite (80%, 100%, 120% do orçamento)
- Atribuição de custo por workflow

---

## 4. Tratamento de Rate Limits

### O Problema

5.000 leads para enriquecer, API permite 100/minuto.

**Abordagem ingênua**: Processar todos imediatamente
**Resultado**: Milhares de erros 429, workflow crashou, estado desconhecido

### Soluções

#### A. Conheça Seus Limites

Antes de integrar, documente:

| API | Rate Limit | Burst | Consequência |
|-----|------------|-------|--------------|
| HubSpot | 100/10seg | 110 | 429 + header retry-after |
| Shopify | 40/min sustentado | 2/seg burst | Leaky bucket |
| OpenAI | Varia por tier | - | 429 + backoff exponencial |
| Stripe | 100/seg (maioria) | - | 429 |

#### B. Lotes com Delays

```javascript
// Configuração n8n Split In Batches
{
  "batchSize": 80,  // Abaixo do limite de 100/min
  "options": {
    "reset": false
  }
}

// Adicionar node Wait após lote
// Wait: 65 segundos (buffer sobre 60)
```

#### C. Implementar Lógica de Retry

```javascript
// No node Code - wrapper de retry
async function withRetry(fn, maxRetries = 3) {
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      return await fn();
    } catch (error) {
      if (error.status === 429 && attempt < maxRetries) {
        const waitTime = Math.pow(2, attempt) * 1000; // Backoff exponencial
        await new Promise(resolve => setTimeout(resolve, waitTime));
        continue;
      }
      throw error;
    }
  }
}
```

#### D. Rastrear Progresso para Resumabilidade

```sql
CREATE TABLE bulk_job_progress (
  job_id UUID PRIMARY KEY,
  workflow_name VARCHAR(255),
  total_items INT,
  processed_items INT DEFAULT 0,
  last_processed_id VARCHAR(255),
  status VARCHAR(50) DEFAULT 'running',
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- Atualizar após cada lote
UPDATE bulk_job_progress
SET processed_items = processed_items + $1,
    last_processed_id = $2,
    updated_at = NOW()
WHERE job_id = $3;
```

Se workflow falhar no item 3.000 de 5.000, retome do 3.001.

---

## 5. Controles Manuais

### O Problema

Automação roda sem intervenção. Isso é uma fraqueza quando:
- Requisitos mudam de repente
- Crise de PR requer pausa
- Retenção legal nas comunicações
- Bug descoberto em produção

### Soluções

#### A. Kill Switches

Toggle simples acessível para equipe não-técnica:

```
Opção 1: Toggle Airtable/Notion
- Criar tabela "Controles do Sistema"
- Linha: { workflow: "sequencia-email", enabled: true }
- Workflow verifica no início: se não habilitado, sair

Opção 2: Slash Command Slack
- /pausar-emails
- Atualiza toggle no banco de dados
- Confirma no canal

Opção 3: Página Admin Simples
- Página web protegida por senha
- Botões toggle para cada workflow
- Log de auditoria de mudanças
```

**Verificação no Workflow:**
```javascript
// Primeiro node após trigger
const controls = await airtable.getRecord('Controles do Sistema', 'sequencia-email');
if (!controls.fields.enabled) {
  // Logar o skip
  await logExecution('skipped', 'Workflow desabilitado via kill switch');
  return []; // Vazio = parar execução
}
```

#### B. Filas de Aprovação

Para ações de alto risco:

```
Trigger
    │
    ▼
Processar & Preparar Ação
    │
    ▼
Inserir em Fila Pendente (banco de dados)
    │
    ▼
Notificar Aprovador (Slack com botões aprovar/rejeitar)
    │
    ▼
Aguardar Webhook (resposta de aprovação)
    │
    ├─► Aprovado → Executar Ação
    └─► Rejeitado → Logar & Notificar
```

#### C. Trilhas de Auditoria Abrangentes

Registre O QUE aconteceu, não apenas QUE aconteceu:

```sql
CREATE TABLE audit_log (
  id SERIAL PRIMARY KEY,
  workflow_name VARCHAR(255),
  action_type VARCHAR(100),
  action_details JSONB,
  affected_entity_type VARCHAR(100),
  affected_entity_id VARCHAR(255),
  performed_at TIMESTAMP DEFAULT NOW(),
  execution_id VARCHAR(255)
);

-- Exemplo de entrada
{
  "workflow_name": "emails-clientes",
  "action_type": "email_enviado",
  "action_details": {
    "to": "cliente@exemplo.com",
    "subject": "Confirmação do Seu Pedido",
    "template": "confirmar_pedido_v2"
  },
  "affected_entity_type": "cliente",
  "affected_entity_id": "cust_12345",
  "performed_at": "2024-01-15T10:32:00Z"
}
```

#### D. Externalização de Configuração

Não codifique valores que podem mudar:

```javascript
// Ruim: Hardcoded
const notifyEmail = "vendas@empresa.com";

// Bom: Configuração externa
const config = await airtable.getRecord('Config', 'configuracoes-notificacao');
const notifyEmail = config.fields.email_notificacao_vendas;
```

Tabela de configuração:
| Chave | Valor | Última Atualização | Atualizado Por |
|-------|-------|-------------------|----------------|
| email_notificacao_vendas | vendas@empresa.com | 2024-01-15 | Admin |
| destinatarios_relatorio_diario | equipe@empresa.com,ceo@empresa.com | 2024-01-10 | Admin |
| max_retentativas | 3 | 2024-01-01 | Sistema |

---

## Resumo do Checklist Pré-Lançamento

### Seleção de Ferramenta
- [ ] Autenticação OAuth → Usando n8n
- [ ] > 5.000 registros ou > 20MB arquivos → Usando Python
- [ ] > 20 nodes de lógica → Consolidado em blocos de código
- [ ] Mantenedores não-técnicos → Usando n8n com documentação

### Design do Sistema
- [ ] Toda entrada validada antes do processamento
- [ ] Workflow entrega valor de negócio (não apenas processa dados)
- [ ] Gerenciamento de estado planejado para memória entre execuções

### Prontidão para Produção
- [ ] Workflow de notificação de erros configurado
- [ ] Log de execuções para banco de dados
- [ ] Idempotência implementada (tratamento de duplicatas)
- [ ] Custos de IA/API calculados e aprovados
- [ ] Rate limits respeitados (batching, delays, retries)
- [ ] Kill switch acessível para equipe de operações
- [ ] Log de trilha de auditoria para todas as ações
- [ ] Configuração externalizada para atualizações fáceis

---

## A Mentalidade de Produção

> Os atalhos que economizam uma hora hoje criam as emergências que custam dias depois.

Automação profissional não é sobre construir coisas rapidamente. É sobre construir coisas que duram.

Quando tentado a pular tratamento de erros porque "provavelmente não vai falhar" ou ignorar rate limits porque "vamos arrumar depois" - lembre-se que produção tem:

- Mais volume que testes
- Casos de borda que você não imaginou
- Falhas nos piores momentos possíveis
- Usuários que não leem instruções
- Integrações que mudam sem aviso

Planeje para tudo isso.

---

## Skills Relacionadas

- **n8n-validation-expert** - Detectar problemas antes da implantação
- **n8n-workflow-patterns** - Padrões de tratamento de erros
- **n8n-expression-syntax** - Acesso correto a dados em tratadores de erro
