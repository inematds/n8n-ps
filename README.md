# n8n Powerhouse

**Transforme o Claude em um especialista em automação n8n.** Construa, valide e implante workflows prontos para produção através de conversas.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![n8n](https://img.shields.io/badge/n8n-Automation-orange)](https://n8n.io)
[![Claude Code](https://img.shields.io/badge/Claude-Code-blue)](https://claude.ai/claude-code)

> 🇬🇧 [English version](README-EN.md)

---

## O Problema

Construir workflows no n8n normalmente requer:
- Aprender as configurações e peculiaridades dos nodes do n8n
- Entender quais nodes existem e como configurá-los
- Conhecer os padrões corretos para webhooks, APIs e bancos de dados
- Debugar erros de sintaxe em expressões
- Descobrir por que seu workflow não está validando

**E se o Claude simplesmente soubesse tudo isso?**

---

## A Solução

O n8n Powerhouse é um **framework de skills** que dá ao Claude expertise profunda em automação n8n. Ele combina:

1. **8 skills especializadas** - Conhecimento profundo sobre padrões, sintaxe e boas práticas do n8n
2. **Integração n8n MCP** - Conexão direta com sua instância n8n para criar/editar/validar workflows
3. **Orientação para produção** - Conhecimento embutido sobre idempotência, tratamento de erros e questões operacionais

---

## Antes e Depois

### Antes (sem o n8n Powerhouse)

```
Você: Construa um workflow que sincronize pedidos do Shopify com meu CRM

Claude: Posso ajudá-lo a desenhar um workflow. Você precisaria de:
1. Um node trigger do Shopify
2. Alguma transformação
3. Um node de CRM

Você precisará configurar isso no n8n...
[Conselho genérico, sem implementação real]
```

### Depois (com o n8n Powerhouse)

```
Você: Construa um workflow que sincronize pedidos do Shopify com meu CRM

Claude: Vou construir isso para você. Deixe-me:
1. Analisar seu stack → Recomendando n8n puro (ambos têm nodes OAuth)
2. Aplicar padrão de processamento de webhook com idempotência
3. Criar o workflow na sua instância n8n...

✅ Workflow criado: "Shopify Order → Zoho CRM + Slack"
   ID: BbRLnEFYH9f4T6Jr
   URL: https://sua-instancia.app.n8n.cloud/workflow/BbRLnEFYH9f4T6Jr

Próximos passos:
1. Adicione suas credenciais do Shopify e Zoho
2. Ative o workflow
```

**O Claude realmente constrói e implanta o workflow.**

---

## O Que São Skills do Claude Code?

[Claude Code](https://claude.ai/claude-code) é a ferramenta CLI da Anthropic para trabalhar com Claude no seu terminal. **Skills** são coleções de arquivos markdown que dão ao Claude conhecimento especializado.

Quando você coloca skills em `.claude/skills/`, o Claude as lê e ganha essa expertise. O n8n Powerhouse fornece 8 skills (~25.000 palavras de conhecimento sobre n8n) cobrindo:

- Decisões de arquitetura
- Padrões de workflow
- Configuração de nodes
- Sintaxe de código
- Debug de expressões
- Validação

---

## O Que é o n8n MCP?

[MCP (Model Context Protocol)](https://modelcontextprotocol.io/) permite que o Claude use ferramentas externas. O [n8n MCP](https://www.npmjs.com/package/n8n-mcp) dá ao Claude ferramentas para:

- **Pesquisar** nodes e templates
- **Criar** workflows na sua instância n8n
- **Atualizar** workflows existentes
- **Validar** configurações
- **Disparar** workflows via webhook
- **Listar** execuções e debugar

Juntos, skills + MCP = Claude pode **conhecer** n8n E **fazer** n8n.

---

## Início Rápido

### 1. Instalar o n8n MCP

```bash
claude mcp add n8n-mcp-api \
  -e MCP_MODE=stdio \
  -e LOG_LEVEL=error \
  -e DISABLE_CONSOLE_OUTPUT=true \
  -e N8N_API_URL=https://SUA-INSTANCIA.app.n8n.cloud \
  -e N8N_API_KEY=sua-chave-api-aqui \
  -- npx -y n8n-mcp
```

**Obtenha sua chave API:** Dashboard do n8n → Settings → API → Create API Key

### 2. Clonar Este Repositório

```bash
git clone https://github.com/inematds/n8n-ps.git
cd n8n-ps
```

### 3. Iniciar o Claude Code

```bash
claude
```

Pronto. O Claude agora tem expertise completa em n8n.

---

## Exemplos do Mundo Real

### Exemplo 1: Automação de E-commerce

```
Você: Eu tenho uma loja Shopify e uso Zoho CRM. Quando alguém faz um pedido,
      quero sincronizar o cliente no Zoho e notificar minha equipe no Slack.

Claude: Vou analisar seu stack e construir isso...

[Claude usa n8n-workflow-architect para analisar]
→ Shopify: OAuth, node trigger nativo ✓
→ Zoho CRM: OAuth, node nativo ✓
→ Slack: OAuth, node nativo ✓
→ Recomendação: n8n puro (todos os serviços suportados)
→ Padrão: Processamento de Webhook com idempotência

[Claude usa n8n MCP para criar workflow]
→ Criado: Shopify Order → Zoho CRM + Slack
→ Inclui: Prevenção de duplicatas (verificar email antes de criar)
→ Inclui: Tratamento de erros
→ Inclui: Notificação Slack com detalhes do pedido

Aqui está seu workflow: [link]
```

### Exemplo 2: Seleção de Ferramenta

```
Você: Devo usar n8n ou Python para processar 50.000 registros de clientes diariamente?

Claude: [Usa n8n-workflow-architect/tool-selection-matrix.md]

Para 50.000 registros, recomendo uma **abordagem híbrida**:

Use n8n para:
- Disparo (schedule trigger)
- Autenticação (se APIs precisarem de OAuth)
- Notificações (alertas no Slack)

Use Python para:
- O processamento real de dados (>5.000 registros é o limite)
- Streaming eficiente de memória
- Transformações complexas

Arquitetura:
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│ n8n         │     │ Python      │     │ n8n         │
│ (Schedule)  │ ──► │ (Process)   │ ──► │ (Notify)    │
└─────────────┘     └─────────────┘     └─────────────┘

Por que não n8n puro?
- n8n roda em Node.js com limites de memória
- 50k registros provavelmente travaria ou daria timeout
- Python pode fazer streaming/chunks eficientemente
```

### Exemplo 3: Debugando Expressões

```
Você: Minha expressão não está funcionando: {{ $json.email }}
      O webhook envia {"body": {"email": "test@example.com"}}

Claude: [Usa n8n-expression-syntax/COMMON_MISTAKES.md]

Encontrei o problema! Dados do webhook estão aninhados em `body`.

❌ Errado: {{ $json.email }}
✅ Correto: {{ $json.body.email }}

O node de webhook encapsula o payload:
{
  "headers": {...},
  "body": {           ← Seus dados estão aqui
    "email": "test@example.com"
  }
}
```

### Exemplo 4: Prontidão para Produção

```
Você: Estou prestes a implantar meu workflow de webhook do Stripe. O que devo verificar?

Claude: [Usa n8n-workflow-architect/production-readiness.md]

Checklist pré-implantação para webhooks de pagamento:

□ Segurança
  - Verificar assinaturas de webhook do Stripe (node Code)
  - Rejeitar assinaturas inválidas imediatamente

□ Idempotência
  - Armazenar IDs de eventos processados
  - Verificar antes de processar: SELECT 1 FROM processed_events WHERE event_id = $1
  - Pular se já processado

□ Tratamento de Erros
  - Workflow de Error Trigger → Slack #alerts
  - Registrar todas as falhas com contexto

□ Tempo de Resposta
  - Usar responseMode: "onReceived" (confirmar para o Stripe imediatamente)
  - Processar de forma assíncrona (Stripe tenta novamente em respostas lentas)

□ Testes
  - Usar modo de teste do Stripe
  - Verificar com Stripe CLI: stripe trigger payment_intent.succeeded

Quer que eu adicione esses padrões ao seu workflow?
```

---

## Arquitetura

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         REQUISIÇÃO DO USUÁRIO                            │
│         "Preciso automatizar X com Shopify, HubSpot e IA"               │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                      CAMADA DE PLANEJAMENTO                              │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │                    n8n-workflow-architect                          │  │
│  │  • Analisar stack de negócios (Shopify, Zoho, HubSpot, etc.)      │  │
│  │  • Decidir: n8n vs Python vs Híbrido                               │  │
│  │  • Avaliar requisitos de prontidão para produção                   │  │
│  └───────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                    ┌───────────────┼───────────────┐
                    ▼               ▼               ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                    CAMADA DE IMPLEMENTAÇÃO                               │
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
│                      CAMADA DE CÓDIGO E SINTAXE                          │
│  ┌─────────────────────┐ ┌─────────────────────┐ ┌───────────────────┐  │
│  │ n8n-code-           │ │ n8n-code-           │ │ n8n-expression-   │  │
│  │ javascript          │ │ python              │ │ syntax            │  │
│  │                     │ │                     │ │                   │  │
│  │ • $input/$json      │ │ • _input/_json      │ │ • sintaxe {{ }}   │  │
│  │ • uso de $helpers   │ │ • Biblioteca padrão │ │ • acesso $json    │  │
│  │ • manejo DateTime   │ │ • Limites Python    │ │ • refs $node      │  │
│  └─────────────────────┘ └─────────────────────┘ └───────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                      CAMADA DE VALIDAÇÃO                                 │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │                    n8n-validation-expert                           │  │
│  │  • Interpretar erros de validação • Corrigir problemas comuns      │  │
│  │  • Perfis de validação • Checklist pré-implantação                 │  │
│  └───────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                         FERRAMENTAS n8n MCP                              │
│                                                                          │
│  Descoberta            Gerenc. Workflow        Validação                 │
│  ─────────────────     ──────────────────     ────────────────────       │
│  • search_nodes        • n8n_create_workflow  • validate_workflow        │
│  • get_node_info       • n8n_update_workflow  • validate_node_operation  │
│  • list_nodes          • n8n_get_workflow     • validate_expressions     │
│  • list_ai_tools       • n8n_list_workflows   • validate_connections     │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## As 8 Skills

| Skill | Propósito | Decisão Chave |
|-------|-----------|---------------|
| **n8n-workflow-architect** | Planejamento estratégico | "Devo usar n8n ou Python?" |
| **n8n-workflow-patterns** | Padrões de implementação | "Qual é o padrão certo para webhooks?" |
| **n8n-node-configuration** | Configuração de nodes | "Quais campos esse node precisa?" |
| **n8n-mcp-tools-expert** | Orientação MCP | "Qual ferramenta MCP devo usar?" |
| **n8n-code-javascript** | Nodes Code em JS | "Como acesso $json no Code?" |
| **n8n-code-python** | Nodes Code em Python | "Quais módulos Python estão disponíveis?" |
| **n8n-expression-syntax** | Debug de expressões | "Por que minha expressão não funciona?" |
| **n8n-validation-expert** | Correção de erros | "O que esse erro de validação significa?" |

### Detalhes das Skills

<details>
<summary><strong>n8n-workflow-architect</strong> - Planejamento estratégico</summary>

**Quando o Claude usa isso:**
- Iniciando um novo projeto de automação
- Decidindo entre n8n, Python ou híbrido
- Avaliando prontidão para produção

**Arquivos principais:**
- `SKILL.md` - Framework de decisão
- `tool-selection-matrix.md` - Critérios n8n vs Python (OAuth, volume, complexidade)
- `business-stack-analysis.md` - Guias de compatibilidade SaaS
- `production-readiness.md` - Checklist pré-lançamento

**Critérios de decisão:**
| Use n8n quando | Use Python quando |
|----------------|-------------------|
| OAuth necessário | >5.000 registros |
| Mantenedores não-técnicos | >20MB arquivos |
| Esperas de vários dias | Algoritmos complexos |
| Integrações padrão | IA de ponta |

</details>

<details>
<summary><strong>n8n-workflow-patterns</strong> - Padrões de implementação</summary>

**Os 5 padrões principais:**

1. **Processamento de Webhook** - Receber HTTP → Processar → Responder
   - Webhooks do Stripe, envios de formulário, integrações de chat

2. **Integração de API HTTP** - Buscar APIs → Transformar → Armazenar
   - Sincronização de dados, agregação de API, enriquecimento

3. **Operações de Banco de Dados** - Ler/Escrever/Sincronizar bancos
   - ETL, migrações, backups

4. **Workflow de Agente IA** - IA com ferramentas e memória
   - Chatbots, geração de conteúdo, análise

5. **Tarefas Agendadas** - Automação recorrente
   - Relatórios diários, limpeza, monitoramento

</details>

<details>
<summary><strong>n8n-node-configuration</strong> - Configuração de nodes</summary>

**Conceitos principais:**
- **Consciente de operação:** Operações diferentes precisam de campos diferentes
- **Dependências de propriedade:** Campos mostram/escondem baseado em outros valores
- **Descoberta progressiva:** Comece com essenciais, adicione complexidade

**Exemplo:** Node do Slack
```javascript
// Para operation='post' (enviar mensagem)
{ resource: "message", operation: "post", channel: "#general", text: "Hello!" }

// Para operation='update' (editar mensagem) - campos diferentes!
{ resource: "message", operation: "update", messageId: "123", text: "Updated!" }
```

</details>

<details>
<summary><strong>n8n-code-javascript</strong> - Nodes Code em JS</summary>

**Sintaxe principal:**
```javascript
// Acessar dados de entrada
const items = $input.all();
const firstItem = $json;

// Referenciar outros nodes
const prevData = $node["Previous Node"].json;

// Requisições HTTP (embutido)
const response = await $helpers.httpRequest({
  method: 'GET',
  url: 'https://api.example.com/data'
});

// DateTime (Luxon)
const now = DateTime.now();
const formatted = now.toFormat('yyyy-MM-dd');
```

</details>

<details>
<summary><strong>n8n-expression-syntax</strong> - Debug de expressões</summary>

**Erros comuns:**

| Errado | Correto | Por quê |
|--------|---------|---------|
| `{{ $json.email }}` | `{{ $json.body.email }}` | Dados do webhook estão em `.body` |
| `{{ json.field }}` | `{{ $json.field }}` | Faltando prefixo `$` |
| `$json.field` | `{{ $json.field }}` | Faltando `{{ }}` |

**Padrões úteis:**
```javascript
// Condicional
{{ $json.status === 'active' ? 'Sim' : 'Não' }}

// Valor padrão
{{ $json.name || 'Desconhecido' }}

// Referenciar outros nodes
{{ $node["Extract Data"].json.email }}
```

</details>

---

## Estrutura de Arquivos

```
n8n-ps/
├── README.md                          # Este arquivo (português)
├── README-EN.md                       # Versão em inglês
├── INSTALL.md                         # Guia de instalação (português)
├── INSTALL-EN.md                      # Guia de instalação (inglês)
├── docs-pt/                           # Documentação detalhada em português
├── examples/
│   └── README.md                      # Prompts de exemplo
└── .claude/
    ├── CLAUDE.md                      # Configuração principal
    └── skills/
        ├── n8n-workflow-architect/    # 4 arquivos - Planejamento
        ├── n8n-workflow-patterns/     # 6 arquivos - Padrões
        ├── n8n-node-configuration/    # 4 arquivos - Config. de nodes
        ├── n8n-mcp-tools-expert/      # 5 arquivos - Orientação MCP
        ├── n8n-code-javascript/       # 6 arquivos - Código JS
        ├── n8n-code-python/           # 6 arquivos - Código Python
        ├── n8n-expression-syntax/     # 4 arquivos - Expressões
        └── n8n-validation-expert/     # 4 arquivos - Validação
```

**Total:** 46 arquivos, ~25.000 palavras de expertise n8n

---

## Perguntas Frequentes

### Preciso do n8n Cloud ou posso usar self-hosted?

Ambos funcionam. Apenas mude `N8N_API_URL` para a URL da sua instância.

### Isso funciona com a Edição Community do n8n?

Sim! Você precisa ter acesso à API habilitado. Nas variáveis de ambiente do seu n8n:
```
N8N_PUBLIC_API_ENABLED=true
```

### O Claude pode ativar workflows?

Não. A API do n8n não suporta ativação. O Claude vai criar o workflow e dizer para você ativá-lo na UI.

### E se o Claude cometer um erro?

Workflows são criados inativos. Sempre revise antes de ativar. O Claude também valida antes de criar.

### Como isso é diferente dos recursos de IA do n8n?

O n8n tem IA embutida para gerar workflows. O n8n Powerhouse dá ao Claude **expertise profunda** mais **acesso direto à API**. O Claude pode:
- Tomar decisões de arquitetura (n8n vs Python)
- Aplicar padrões de produção (idempotência, tratamento de erros)
- Debugar problemas complexos
- Criar workflows que seguem boas práticas

### Posso adicionar minhas próprias skills?

Sim! Adicione arquivos `.md` em `.claude/skills/nome-da-sua-skill/` e atualize `.claude/CLAUDE.md`.

---

## Referência das Ferramentas MCP

<details>
<summary><strong>Ferramentas de Descoberta</strong></summary>

```bash
search_nodes          # Encontrar nodes por palavra-chave
get_node_essentials   # Visão rápida do node (comece aqui)
get_node_info         # Documentação completa
list_nodes            # Listar por categoria/pacote
list_ai_tools         # Nodes com capacidade de IA
```

</details>

<details>
<summary><strong>Gerenciamento de Workflow</strong></summary>

```bash
n8n_create_workflow          # Criar novo workflow
n8n_get_workflow             # Obter por ID
n8n_update_partial_workflow  # Atualizações incrementais (add/remove nodes)
n8n_update_full_workflow     # Substituição completa
n8n_list_workflows           # Listar todos workflows
n8n_delete_workflow          # Deletar workflow
```

</details>

<details>
<summary><strong>Ferramentas de Validação</strong></summary>

```bash
validate_workflow             # Validação completa do workflow
validate_node_operation       # Validação de node único
validate_workflow_connections # Verificar apenas conexões
validate_workflow_expressions # Verificar apenas expressões
n8n_validate_workflow         # Validar por ID do workflow
```

</details>

<details>
<summary><strong>Ferramentas de Template</strong></summary>

```bash
search_templates       # Pesquisar por palavra-chave
list_node_templates    # Encontrar templates usando nodes específicos
get_template           # Obter JSON completo do workflow
get_templates_for_task # Templates curados por tipo de tarefa
```

</details>

<details>
<summary><strong>Ferramentas de Execução</strong></summary>

```bash
n8n_trigger_webhook_workflow  # Disparar via webhook
n8n_list_executions           # Histórico de execuções
n8n_get_execution             # Detalhes da execução
n8n_health_check              # Verificar conectividade do n8n
```

</details>

---

## Prontidão para Produção

As skills incluem orientação de produção para:

| Área | Cobertura |
|------|-----------|
| **Observabilidade** | Workflows de erro, log de execução, health checks |
| **Idempotência** | Tratamento de duplicatas, verificar-antes-criar, chaves de idempotência |
| **Gestão de Custos** | Custos de API de IA, cache, seleção de modelo |
| **Controle Operacional** | Kill switches, filas de aprovação, trilhas de auditoria |
| **Segurança** | Assinaturas de webhook, tratamento de credenciais, validação de entrada |

Veja [production-readiness.md](.claude/skills/n8n-workflow-architect/production-readiness.md) para o checklist completo.

---

## Contribuindo

1. Faça fork deste repositório
2. Adicione/edite arquivos em `.claude/skills/`
3. Atualize descrições de skills em `.claude/CLAUDE.md`
4. Envie um PR

### Estrutura de Skill

Cada skill precisa de:
- `SKILL.md` - Arquivo principal (carregado quando a skill é invocada)
- `README.md` - Visão geral legível por humanos
- Arquivos `.md` adicionais para tópicos detalhados

---

## Links

- [Documentação do n8n](https://docs.n8n.io/)
- [n8n MCP no npm](https://www.npmjs.com/package/n8n-mcp)
- [Claude Code](https://claude.ai/claude-code)
- [Documentação de Skills do Claude Code](https://docs.anthropic.com/en/docs/claude-code/skills)
- [Model Context Protocol](https://modelcontextprotocol.io/)

---

## Licença

Licença MIT - Use livremente para seus projetos de automação.

---

<p align="center">
  <strong>Construído com Claude Code + n8n MCP</strong><br>
  <em>Transforme conversas em automação pronta para produção</em>
</p>
