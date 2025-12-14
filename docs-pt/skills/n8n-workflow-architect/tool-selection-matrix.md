# Matriz de Seleção de Ferramenta

Critérios detalhados de decisão para escolher entre n8n, Python ou abordagens híbridas.

---

## A Pergunta Fundamental

Antes de escrever uma única linha de código ou arrastar um único node:

> **Qual ferramenta é apropriada para este problema específico, e quem vai mantê-lo?**

---

## Matriz de Decisão

### Complexidade de Autenticação

| Cenário | Ferramenta Recomendada | Justificativa |
|---------|------------------------|---------------|
| API key simples | Qualquer uma | Trivial de qualquer forma |
| OAuth 2.0 (Google, Slack, HubSpot) | **n8n** | Ciclo de vida de tokens gerenciado, tratamento de refresh |
| Implementação OAuth customizada | Python | Controle total necessário |
| OAuth multi-tenant | Híbrido | n8n por tenant, Python para gerenciamento |

**A Verificação de Realidade do OAuth:**
- URLs de redirect
- Códigos de autorização
- Access tokens
- Refresh tokens
- Expiração de tokens
- Armazenamento seguro
- Tratamento de rotação

O n8n lida com TUDO isso. Implementações Python frequentemente armazenam tokens em texto puro.

---

### Avaliação de Manutenibilidade

| Equipe de Manutenção | Ferramenta Recomendada | Por quê |
|---------------------|------------------------|---------|
| Usuários de negócio não-técnicos | **n8n** | Visual = auto-documentado |
| Misto técnico/não-técnico | **Híbrido** | n8n para UI, Python para lógica |
| Equipe dev dedicada | Qualquer uma | Escolha baseado em outros fatores |
| Dev solo, handoff futuro desconhecido | **n8n** | Menor barreira para sucessor |
| Rotação de terceirizados/offshore | **n8n** | Reduz tempo de ramp-up |

**O Teste do Ônibus:**
> "Se eu for atropelado por um ônibus, alguém consegue entender isso em um dia?"

---

### Duração do Processo

| Duração do Processo | Ferramenta Recomendada | Implementação |
|--------------------|------------------------|---------------|
| Segundos a minutos | Qualquer uma | Execução padrão |
| Horas | **n8n** | Node Wait |
| Dias a semanas | **n8n** | Node Wait com retomada |
| Aprovação humana necessária | **n8n** | Padrão Webhook + Wait |
| Máquina de estados complexa | Híbrido | Orquestração n8n, estado no DB |

**Por que Espera Importa:**

"Espera" em Python:
```python
# Opção A: Servidor fica rodando (caro)
time.sleep(259200)  # 3 dias

# Opção B: Gerenciamento de estado complexo
# - Salvar estado no banco de dados
# - Configurar agendador
# - Reconstruir contexto na retomada
# - Tratar casos de borda
```

Espera em n8n:
```
[Trigger] → [Processar] → [Wait 3 dias] → [Continuar]
```

---

### Limites de Volume de Dados

| Característica de Dados | Limite | Ferramenta Recomendada |
|------------------------|--------|------------------------|
| Registros por execução | < 5.000 | n8n |
| Registros por execução | > 5.000 | **Python** |
| Tamanho de arquivo | < 20 MB | n8n |
| Tamanho de arquivo | > 20 MB | **Python** |
| Processamento em memória | < 500 MB | n8n (com cautela) |
| Processamento em memória | > 500 MB | **Python** |
| Streaming necessário | Sim | **Python** |
| Processamento em lote | Lotes grandes | **Python** |

**Prevenção de Crash de Memória:**

n8n roda em Node.js com limites de memória padrão. Estas operações vão crashar:
- Carregar PDF de 50MB na memória
- Iterar 10.000 linhas mantendo todas em memória
- Processar arquivos de vídeo
- Payloads JSON grandes

Soluções Python:
- `pandas` com leitura em chunks
- Streaming baseado em generators
- Arquivos mapeados em memória
- Garbage collection explícito

---

### Complexidade de Lógica

| Indicador de Complexidade | Limite | Ferramenta Recomendada |
|--------------------------|--------|------------------------|
| Branches condicionais | 1-2 | n8n IF/Switch |
| Branches condicionais | 3-4 | Bloco de código no n8n |
| Branches condicionais | 5+ | **Python** |
| Condicionais aninhados | Qualquer | **Python** |
| Processamento algorítmico | Qualquer | **Python** |
| Matching fuzzy | Complexo | **Python** |
| Transformações matemáticas | Complexas | **Python** |
| Acumulação de estado em loops | Sim | **Python** |

**O Teto de Complexidade Visual:**

Você ultrapassou quando:
- O canvas parece espaguete
- Linhas cruzando por todo lado
- Nodes empilhados incompreensivelmente
- Precisa dar zoom out para ver a estrutura
- Não consegue ler labels dos nodes no zoom utilizável

**O Teste das 20 Linhas:**
> "Essa lógica pode ser expressa em 20 linhas de código legível?"
> Se sim → Use um node Code
> Se precisaria de 50+ nodes → Definitivamente use código

---

### Requisitos de Inovação

| Requisito | Ferramenta Recomendada |
|-----------|------------------------|
| Integrações SaaS padrão | n8n |
| Bibliotecas de IA recentes (< 6 meses) | **Python** |
| Frameworks experimentais | **Python** |
| GraphRAG, padrões RAG avançados | **Python** |
| Modelos ML customizados | **Python** |
| APIs estáveis e bem documentadas | Qualquer uma |

**A Realidade do Atraso:**
A equipe n8n precisa avaliar → integrar → testar → documentar antes de novas bibliotecas aparecerem como nodes.

Se você precisa de algo lançado no mês passado → use Python.

---

## Padrões de Arquitetura Híbrida

### Padrão 1: Orquestração n8n + Microsserviço Python

```
┌──────────────────────────────────────────────────┐
│                    Camada n8n                     │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐        │
│  │ Webhook │ → │ HTTP    │ → │ Slack   │        │
│  │ Trigger │   │ Request │   │ Notify  │        │
│  └─────────┘   └────┬────┘   └─────────┘        │
│                     │                            │
└─────────────────────┼────────────────────────────┘
                      │ Chamada API
                      ▼
┌──────────────────────────────────────────────────┐
│               Microsserviço Python                │
│  ┌─────────────────────────────────────────────┐ │
│  │ • Processamento de dados complexo           │ │
│  │ • Operações de IA/ML                        │ │
│  │ • Computação pesada                         │ │
│  │ • Retorna resultado JSON                    │ │
│  └─────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────┘
```

**Use quando**: Precisa de capacidades Python mas quer integrações do n8n

### Padrão 2: Nodes Code no n8n

```
┌──────────────────────────────────────────────────┐
│                    Workflow n8n                   │
│                                                   │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐        │
│  │ Trigger │ → │ Code    │ → │ Output  │        │
│  │         │   │ (JS/Py) │   │ Node    │        │
│  └─────────┘   └─────────┘   └─────────┘        │
│                                                   │
└──────────────────────────────────────────────────┘
```

**Use quando**: Lógica é complexa mas contida, sem serviço externo necessário

### Padrão 3: Handoff Orientado a Eventos

```
Workflow n8n A             Serviço Python            Workflow n8n B
┌───────────────┐          ┌───────────────┐          ┌───────────────┐
│ Trigger       │          │               │          │ Webhook       │
│ → Validar     │  ──────► │ Processar     │ ──────►  │ → Transformar │
│ → Enfileirar  │          │ → Computar    │          │ → Notificar   │
└───────────────┘          │ → Callback    │          └───────────────┘
                           └───────────────┘
```

**Use quando**: Processos Python de longa duração, padrões assíncronos

---

## Fluxograma de Decisão

```
                    INÍCIO
                      │
                      ▼
              ┌───────────────┐
              │ OAuth necess.?│
              └───────┬───────┘
                 Sim  │  Não
          ┌───────────┴───────────┐
          ▼                       ▼
    Use n8n para auth      ┌───────────────┐
          │               │ > 5000 registros│
          │               │ ou > 20MB arq.? │
          │               └───────┬───────┘
          │                  Sim  │  Não
          │           ┌───────────┴───────────┐
          │           ▼                       ▼
          │     Use Python para          ┌───────────────┐
          │     processamento            │ > 20 nodes de │
          │           │                  │ lógica negócio│
          │           │                  └───────┬───────┘
          │           │                    Sim  │  Não
          │           │             ┌───────────┴───────────┐
          │           │             ▼                       ▼
          │           │       Use nodes Code          ┌───────────────┐
          │           │       no n8n                  │ Mantenedores  │
          │           │             │                 │ não-técnicos? │
          │           │             │                 └───────┬───────┘
          │           │             │                    Sim  │  Não
          │           │             │             ┌───────────┴───────────┐
          │           │             │             ▼                       ▼
          │           │             │        Use n8n               Use qualquer
          │           │             │             │                 (preferência)
          │           │             │             │                       │
          └───────────┴─────────────┴─────────────┴───────────────────────┘
                                          │
                                          ▼
                                   IMPLEMENTAÇÃO
```

---

## Anti-Padrões a Evitar

### 1. "n8n Pode Fazer Tudo"
**Realidade**: Workflows visuais têm limites. Forçar lógica complexa cria espaguete impossível de manter.

### 2. "Python É Sempre Melhor"
**Realidade**: Python requer mais infraestrutura, handoff mais difícil, reinventa rodas de OAuth.

### 3. "Vamos Otimizar Depois"
**Realidade**: Decisões de arquitetura se acumulam. Escolha errada de ferramenta no início = migração dolorosa depois.

### 4. "Funciona nos Testes"
**Realidade**: Produção tem volume, casos de borda, falhas. Teste com dados realistas.

### 5. "A Demo Ficou Ótima"
**Realidade**: Demos mostram caminhos felizes. Produção precisa de tratamento de erros, monitoramento, recuperação.

---

## Cartão de Referência Rápida

| Fator | n8n | Python | Híbrido |
|-------|-----|--------|---------|
| Serviços OAuth | ✅ | ⚠️ | ✅ |
| Mantenedores não-técnicos | ✅ | ❌ | ⚠️ |
| Esperas de vários dias | ✅ | ⚠️ | ✅ |
| Dados grandes (5k+ linhas) | ❌ | ✅ | ✅ |
| Arquivos grandes (20MB+) | ❌ | ✅ | ✅ |
| Algoritmos complexos | ⚠️ | ✅ | ✅ |
| IA de ponta | ❌ | ✅ | ✅ |
| Integrações padrão | ✅ | ⚠️ | ✅ |
| Prototipagem rápida | ✅ | ⚠️ | ⚠️ |
| Manutenção longo prazo | ✅ | ⚠️ | ✅ |

**Legenda**: ✅ Recomendado | ⚠️ Possível com ressalvas | ❌ Não recomendado

---

## Skills Relacionadas

- **n8n-code-javascript** - Quando nodes Code são a resposta
- **n8n-code-python** - Para nodes Code Python
- **n8n-workflow-patterns** - Uma vez escolhida a ferramenta, escolha o padrão certo
