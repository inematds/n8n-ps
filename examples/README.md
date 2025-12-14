# Exemplos de Workflows

Estes são exemplos de workflows que você pode pedir ao Claude para construir. Basta descrever o que você precisa e Claude usará as skills e MCP para criá-los.

---

## Prompts de Exemplo

### Processamento de Webhook

```
Construa um webhook que receba envios de formulário, valide o email,
armazene em um banco de dados e envie uma notificação no Slack.
```

### Integração de API

```
Crie um workflow que busque dados de uma API REST a cada hora,
transforme a resposta e atualize uma planilha do Google Sheets.
```

### Sincronização de Banco de Dados

```
Sincronize novos clientes do Shopify para meu banco Postgres diariamente.
Trate duplicatas verificando o email.
```

### Agente de IA

```
Construa um chatbot que pode pesquisar nossa documentação e responder perguntas.
Use OpenAI para a IA e webhook para receber mensagens.
```

### Relatório Agendado

```
Todos os dias às 9h, busque as vendas de ontem do Shopify,
calcule a receita total e contagem de pedidos, e poste um resumo no Slack.
```

---

## Dicas Pro

### Comece com a Arquitetura

Antes de construir, pergunte ao Claude:
```
Devo usar n8n ou Python para [seu caso de uso]?
```

Claude usará a skill `n8n-workflow-architect` para analisar seus requisitos.

### Obtenha Orientação de Padrões

Peça recomendações de padrões:
```
Qual o melhor padrão para processar webhooks do Stripe?
```

Claude consultará `n8n-workflow-patterns` para abordagens comprovadas.

### Valide Antes de Implantar

Sempre peça ao Claude para validar:
```
Valide o workflow que você acabou de criar.
```

Claude usará as ferramentas de validação MCP para verificar problemas.

---

## Arquivos de Workflow

Se você quiser importar workflows manualmente, pode exportá-los do Claude:

```
Exporte o workflow como JSON para que eu possa importar manualmente.
```

Claude fornecerá o JSON do workflow que você pode importar pela UI do n8n.
