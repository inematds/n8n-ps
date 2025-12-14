---
name: n8n-workflow-architect
description: Consultor estratégico de arquitetura de automação. Use quando usuários quiserem planejar soluções de automação, avaliar seu stack técnico (Shopify, Zoho, HubSpot, etc.), decidir entre n8n vs Python/Claude Code, ou precisarem de orientação sobre design de automação pronto para produção. Invoca modo de planejamento para decisões arquiteturais complexas.
---

# n8n Workflow Architect

O Arquiteto de Automação Inteligente (IAA) - Orientação estratégica para construir sistemas de automação que sobrevivem em produção.

---

## Quando Usar Esta Skill

Invoque esta skill quando usuários:

1. **Querem planejar um projeto de automação** - "Preciso automatizar meu pipeline de vendas"
2. **Têm múltiplos serviços para integrar** - "Eu uso Shopify, Klaviyo e Notion"
3. **Precisam de decisões de arquitetura** - "Devo usar n8n ou Python para isso?"
4. **Estão avaliando viabilidade** - "Posso automatizar X com meu stack atual?"
5. **Querem orientação para produção** - "Como faço isso confiável?"

---

## A Filosofia Central

> **Viabilidade sobre Possibilidade**

A distância entre o que é tecnicamente possível e o que é realmente viável em produção é enorme. Esta skill ajuda usuários a construir sistemas que:

- Não quebram às 3 da manhã de um sábado
- Não requerem um PhD para manter
- Respeitam segurança de dados, escala e gerenciamento de estado
- Entregam valor real para o negócio, não apenas esperteza técnica

---

## Framework de Decisão de Arquitetura

### Passo 1: Análise de Stack

Quando um usuário menciona suas ferramentas, avalie cada uma para:

| Categoria | Exemplos Comuns | Suporte Nativo n8n | Complexidade de Auth |
|-----------|-----------------|-------------------|---------------------|
| E-commerce | Shopify, WooCommerce, BigCommerce | Sim | OAuth |
| CRM | HubSpot, Salesforce, Zoho CRM | Sim | OAuth |
| Marketing | Klaviyo, Mailchimp, ActiveCampaign | Sim | API Key/OAuth |
| Produtividade | Notion, Airtable, Google Sheets | Sim | OAuth |
| Comunicação | Slack, Discord, Teams | Sim | OAuth |
| Pagamentos | Stripe, PayPal, Square | Sim | API Key |
| Suporte | Zendesk, Intercom, Freshdesk | Sim | API Key/OAuth |

**Ação**: Use `search_nodes` do n8n MCP para verificar disponibilidade de nodes.

### Passo 2: Matriz de Seleção de Ferramenta

Aplique estas regras de decisão:

#### Use n8n Quando:

| Condição | Por quê |
|----------|---------|
| Autenticação OAuth necessária | n8n gerencia ciclo de vida de tokens automaticamente |
| Mantenedores não-técnicos | Workflows visuais são auto-documentados |
| Processos de vários dias com esperas | Node Wait embutido lida com suspensão |
| Integrações SaaS padrão | Nodes pré-construídos eliminam boilerplate |
| < 5.000 registros por execução | Dentro dos limites de memória |
| < 20 nodes de lógica de negócios | Mantém clareza visual |

#### Use Python/Claude Code Quando:

| Condição | Por quê |
|----------|---------|
| > 5.000 registros para processar | Processamento em streaming, gerenciamento de memória |
| > 20MB de arquivos | Capacidades de processamento em chunks |
| Algoritmos complexos | Código é mais manutenível que 50+ nodes |
| Bibliotecas de IA de ponta | Acesso aos pacotes mais recentes |
| Transformação pesada de dados | Otimização com Pandas, NumPy |
| Modelos ML customizados | Acesso total ao ecossistema Python |

#### Use Híbrido (Recomendado para Sistemas Complexos):

```
n8n (Camada de Orquestração)
├── Webhooks & triggers
├── Autenticação OAuth
├── Integrações voltadas ao usuário
├── Coordenação de fluxo
│
└── Chama Serviço Python (Camada de Processamento)
    ├── Computação pesada
    ├── Lógica complexa
    ├── Operações de IA/ML
    └── Retorna resultados para n8n
```

---

## Avaliação Rápida de Stack de Negócios

Quando o usuário descrever seu stack, responda com esta análise:

### Template de Resposta:

```markdown
## Análise de Stack: [Tipo de Negócio do Usuário]

### Serviços Identificados:
1. **[Serviço 1]** - [Categoria] - Suporte n8n: [Sim/Parcial/Não]
2. **[Serviço 2]** - [Categoria] - Suporte n8n: [Sim/Parcial/Não]
...

### Abordagem Recomendada: [n8n / Python / Híbrido]

**Justificativa:**
- [Fator de decisão chave 1]
- [Fator de decisão chave 2]
- [Fator de decisão chave 3]

### Complexidade de Integração: [Baixa/Média/Alta]
- Complexidade de auth: [API keys simples / OAuth necessário]
- Volume de dados: [Estimativa baseada no caso de uso]
- Necessidades de processamento: [Transformações simples / Lógica complexa]

### Próximos Passos:
1. [Ação específica usando outras skills n8n]
2. [Padrão a seguir de n8n-workflow-patterns]
3. [Abordagem de validação de n8n-validation-expert]
```

---

## Cenários de Negócio Comuns

### Cenário 1: Automação de E-commerce
**Stack**: Shopify + Klaviyo + Slack + Google Sheets

**Veredicto**: n8n Puro
- Todos os serviços têm nodes nativos
- OAuth gerenciado automaticamente
- Padrões de webhook padrão
- Use: `n8n-workflow-patterns` → webhook_processing

### Cenário 2: Qualificação de Leads com IA
**Stack**: Typeform + HubSpot + OpenAI + Pontuação Customizada

**Veredicto**: Híbrido
- n8n: webhook Typeform, sync HubSpot, notificações
- Python/Node Code: Algoritmo de pontuação complexo, prompts de IA
- Use: `n8n-workflow-patterns` → ai_agent_workflow

### Cenário 3: Pipeline de Dados / ETL
**Stack**: PostgreSQL + BigQuery + 50k+ registros diários

**Veredicto**: Python com Trigger n8n
- n8n: Schedule trigger, notificações de sucesso/falha
- Python: Processamento em lote, streaming, transformações
- Razão: Limites de memória no n8n para datasets grandes

### Cenário 4: Workflow de Aprovação Multi-Etapas
**Stack**: Slack + Notion + Email + períodos de espera de 3 dias

**Veredicto**: n8n Puro
- Node Wait embutido para delays
- Integrações nativas Slack/Notion
- Padrões de aprovação humana embutidos
- Use: `n8n-workflow-patterns` → scheduled_tasks

---

## Checklist de Prontidão para Produção

Antes de qualquer automação ir ao ar, verifique:

### Observabilidade
- [ ] Workflow de notificação de erros existe
- [ ] Log de execuções para banco de dados
- [ ] Workflow de health check para caminhos críticos
- [ ] Alertas estruturados por severidade

### Idempotência
- [ ] Tratamento de webhooks duplicados
- [ ] Padrões verificar-antes-criar
- [ ] Chaves de idempotência para pagamentos
- [ ] Capacidade de re-execução segura

### Consciência de Custos
- [ ] Custos de API de IA calculados e aprovados
- [ ] Rate limits documentados
- [ ] Estratégia de cache para chamadas repetidas
- [ ] Dimensionamento correto de modelo (Haiku vs Sonnet vs Opus)

### Controle Operacional
- [ ] Kill switch acessível para equipe não-técnica
- [ ] Filas de aprovação para ações de alto risco
- [ ] Trilha de auditoria para todas as ações
- [ ] Configuração externalizada

Use a skill `n8n-validation-expert` para validar workflows antes da implantação.

---

## Integração com Outras Skills n8n

Esta skill funciona como a **camada de planejamento** que coordena outras skills:

```
┌─────────────────────────────────────────────────────────────┐
│                  n8n-workflow-architect                      │
│            (Decisões Estratégicas & Planejamento)           │
└─────────────────────────────────────────────────────────────┘
                              │
         ┌────────────────────┼────────────────────┐
         ▼                    ▼                    ▼
┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│ n8n-workflow-   │  │ n8n-node-       │  │ n8n-validation- │
│ patterns        │  │ configuration   │  │ expert          │
│ (Arquitetura)   │  │ (Config. Nodes) │  │ (Qualidade)     │
└─────────────────┘  └─────────────────┘  └─────────────────┘
         │                    │                    │
         └────────────────────┼────────────────────┘
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                     Ferramentas n8n MCP                      │
│    (search_nodes, validate_workflow, create_workflow, etc.) │
└─────────────────────────────────────────────────────────────┘
```

### Guia de Handoff entre Skills:

| Depois que Architect Decide... | Passa Para |
|--------------------------------|------------|
| Tipo de padrão identificado | `n8n-workflow-patterns` para estrutura detalhada |
| Nodes específicos necessários | `n8n-node-configuration` para configuração |
| Node Code necessário | `n8n-code-javascript` ou `n8n-code-python` |
| Expressões necessárias | `n8n-expression-syntax` para sintaxe correta |
| Pronto para validar | `n8n-validation-expert` para verificações pré-deploy |
| Precisa info de node | n8n MCP → `get_node_essentials`, `search_nodes` |

---

## Ativação do Modo de Planejamento

Para decisões arquiteturais complexas, entre no modo de planejamento para:

1. **Analisar o contexto completo de negócio**
2. **Avaliar todos os pontos de integração**
3. **Desenhar a arquitetura de fluxo de dados**
4. **Identificar modos de falha e mitigações**
5. **Criar roadmap de implementação**

### Ative o Modo de Planejamento Quando:

- Usuário tem 3+ serviços para integrar
- Incerto se n8n ou Python é melhor
- Automação de alto risco (pagamentos, dados de clientes)
- Processos complexos multi-etapas
- Componentes de IA/ML envolvidos

### Estrutura de Saída do Modo de Planejamento:

```markdown
## Plano de Arquitetura de Automação

### 1. Contexto de Negócio
[Qual problema estamos resolvendo?]

### 2. Análise de Stack
[Cada serviço, seu papel, complexidade de integração]

### 3. Arquitetura Recomendada
[n8n / Python / Híbrido com justificativa]

### 4. Design de Fluxo de Dados
[Representação visual do fluxo]

### 5. Fases de Implementação
Fase 1: [Workflow principal]
Fase 2: [Tratamento de erros & observabilidade]
Fase 3: [Otimização & escala]

### 6. Avaliação de Riscos
[O que pode dar errado, como prevenimos]

### 7. Plano de Manutenção
[Quem mantém, quais skills necessárias]
```

---

## Árvore de Decisão Rápida

```
INÍCIO: Usuário quer automatizar algo
  │
  ├─► Envolve OAuth? ───────────────────────► Use n8n
  │
  ├─► Não-desenvolvedores vão manter? ──────► Use n8n
  │
  ├─► Precisa esperar dias/semanas? ────────► Use n8n
  │
  ├─► Processando > 5000 registros? ────────► Use Python
  │
  ├─► Arquivos > 20MB? ─────────────────────► Use Python
  │
  ├─► IA/ML de ponta? ──────────────────────► Use Python
  │
  ├─► Algoritmo complexo (precisaria 20+ nodes)? ─► Use Python
  │
  └─► Mistura dos anteriores? ──────────────► Use Híbrido
```

---

## Integração com Ferramentas MCP

Use estas ferramentas n8n MCP durante o planejamento de arquitetura:

| Fase de Planejamento | Ferramentas MCP a Usar |
|---------------------|------------------------|
| Análise de stack | `search_nodes` - verificar disponibilidade de nodes |
| Seleção de padrão | `list_node_templates` - encontrar workflows similares |
| Verificação de viabilidade | `get_node_essentials` - entender capacidades |
| Estimativa de complexidade | `get_node_documentation` - necessidades de auth & config |
| Referência de template | `get_template` - estudar padrões existentes |

---

## Sinais de Alerta para Observar

Avise usuários quando você ver estes padrões:

| Sinal de Alerta | Risco | Recomendação |
|-----------------|-------|--------------|
| "Quero que IA faça tudo" | Explosão de custos, imprevisibilidade | Escope IA para tarefas específicas, use cache |
| "Precisa processar milhões de linhas" | Crashes de memória | Python com streaming, não loops n8n |
| "O workflow tem 50 nodes" | Impossível manter | Consolide em blocos de código ou divida workflows |
| "Vamos adicionar tratamento de erros depois" | Falhas silenciosas | Construa tratamento de erros desde o dia um |
| "Deve funcionar com qualquer entrada" | Sistema frágil | Defina e valide entradas esperadas |
| "O estagiário vai manter" | Ponto único de falha | Use n8n para clareza visual, documente bem |

---

## Resumo

**Esta skill responde**: "Dado meu stack de negócios e requisitos, qual é a forma mais inteligente de construir esta automação?"

**Saídas principais**:
1. Análise de compatibilidade de stack
2. Recomendação n8n vs Python vs Híbrido
3. Handoffs de padrões e skills
4. Orientação de prontidão para produção
5. Roadmap de implementação via modo de planejamento

**Funciona com**:
- Todas as skills n8n-* para detalhes de implementação
- Ferramentas n8n MCP para descoberta de nodes e criação de workflows
- Modo de planejamento para decisões arquiteturais complexas

---

## Arquivos Relacionados

- [tool-selection-matrix.md](tool-selection-matrix.md) - Critérios detalhados de decisão
- [business-stack-analysis.md](business-stack-analysis.md) - Guias de integração SaaS comuns
- [production-readiness.md](production-readiness.md) - Detalhes do checklist pré-lançamento
