# n8n Workflow Architect

Consultor estratégico de arquitetura de automação baseado no Protocolo IAA (Intelligent Automation Architect).

## Propósito

Esta skill ajuda usuários a tomar decisões informadas sobre arquitetura de automação **antes** de construir. Ela responde às perguntas fundamentais:

- Devo usar n8n, Python ou uma abordagem híbrida?
- Dado meu stack de negócios, qual é o caminho de menor resistência?
- Como faço para deixar isso pronto para produção?

## Quando o Claude Deve Usar Esta Skill

Invoque esta skill quando usuários:

1. **Planejam um projeto de automação** - "Preciso automatizar..."
2. **Descrevem seu stack técnico** - "Eu uso Shopify, Zoho, HubSpot..."
3. **Fazem perguntas de arquitetura** - "Devo usar n8n ou Python?"
4. **Avaliam viabilidade** - "Posso automatizar X?"
5. **Precisam de orientação para produção** - "Como faço isso confiável?"

## Conteúdo

| Arquivo | Propósito |
|---------|-----------|
| [SKILL.md](SKILL.md) | Ponto de entrada principal - framework de decisão, referências rápidas |
| [tool-selection-matrix.md](tool-selection-matrix.md) | Critérios detalhados de decisão n8n vs Python |
| [business-stack-analysis.md](business-stack-analysis.md) | Plataformas SaaS comuns e padrões de integração |
| [production-readiness.md](production-readiness.md) | Checklist pré-lançamento e preocupações operacionais |

## Integração com Outras Skills

Esta skill serve como **camada de planejamento** que coordena com:

```
                 n8n-workflow-architect
                 (Planejamento Estratégico)
                         │
    ┌────────────────────┼────────────────────┐
    ▼                    ▼                    ▼
n8n-workflow-      n8n-node-           n8n-validation-
patterns           configuration        expert
(Arquitetura)      (Config. Nodes)      (Qualidade)
    │                    │                    │
    ▼                    ▼                    ▼
n8n-code-          n8n-code-           n8n-expression-
javascript         python              syntax
(Lógica JS)        (Lógica Python)     (Expressões)
    │                    │                    │
    └────────────────────┼────────────────────┘
                         ▼
                   Ferramentas n8n MCP
              (Criar, Validar, Implantar)
```

### Fluxo de Handoff entre Skills

1. **Usuário descreve necessidade** → `n8n-workflow-architect` analisa
2. **Arquitetura decidida** → Passa para `n8n-workflow-patterns`
3. **Nodes identificados** → Passa para `n8n-node-configuration`
4. **Código necessário** → Passa para `n8n-code-javascript` ou `n8n-code-python`
5. **Expressões necessárias** → Passa para `n8n-expression-syntax`
6. **Pronto para validar** → Passa para `n8n-validation-expert`
7. **Construir workflow** → Usa ferramentas n8n MCP

## Conceitos Chave

### A Filosofia Central

> **Viabilidade sobre Possibilidade**

A distância entre o que é tecnicamente possível e o que é realmente viável em produção é enorme. Construa sistemas que:

- Não quebram às 3 da manhã de um sábado
- Não requerem um PhD para manter
- Entregam valor real para o negócio

### Regras Rápidas de Decisão

| Use n8n Quando | Use Python Quando |
|----------------|-------------------|
| Autenticação OAuth necessária | Processando > 5.000 registros |
| Mantenedores não-técnicos | Arquivos > 20MB |
| Processos de espera de vários dias | Algoritmos complexos |
| Integrações SaaS padrão | IA/ML de ponta |

### Arquitetura Híbrida

Para sistemas complexos, combine ambos:

```
Camada n8n (Orquestração)
├── Webhooks, triggers, OAuth
├── Integrações voltadas ao usuário
└── Coordenação de fluxo

Camada Python (Processamento)
├── Computação pesada
├── Lógica complexa
└── Operações de IA/ML
```

## Exemplos de Uso

### Exemplo 1: Análise de Stack E-commerce

**Usuário**: "Eu uso Shopify, Klaviyo e Slack. Como devo automatizar notificações de pedidos?"

**Resposta do Architect**:
- Todos os serviços têm nodes nativos no n8n
- OAuth gerenciado automaticamente
- Padrões de webhook padrão se aplicam
- **Recomendação**: n8n puro
- **Passa para**: `n8n-workflow-patterns` → webhook_processing

### Exemplo 2: Pontuação de Leads com IA

**Usuário**: "Preciso que IA pontue leads do Typeform e atualize o HubSpot"

**Resposta do Architect**:
- Typeform/HubSpot via n8n (OAuth)
- Lógica de pontuação com IA é complexa
- **Recomendação**: Híbrido
- n8n para integrações, node Code para lógica de IA
- **Passa para**: `n8n-workflow-patterns` → ai_agent_workflow

### Exemplo 3: Pipeline de Dados Grande

**Usuário**: "Sincronização diária de 50.000 registros do Postgres para BigQuery"

**Resposta do Architect**:
- Volume excede limites de memória do n8n
- **Recomendação**: Python com trigger n8n
- n8n para agendamento e notificações
- Python para processamento em lote
- **Aviso**: Rate limits, resumabilidade necessária

## Créditos

Baseado no Protocolo IAA (Intelligent Automation Architect) - um guia abrangente para construir sistemas de automação que sobrevivem em produção.
