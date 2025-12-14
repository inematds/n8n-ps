# Código JavaScript n8n

Escreva JavaScript em nodes Code do n8n.

---

## Propósito

Esta skill ajuda a escrever código JavaScript correto em nodes Code do n8n, usando a sintaxe específica do n8n para acesso a dados, requisições HTTP e manipulação de datas.

---

## Quando Usar

- Escrevendo JavaScript em nodes Code
- Usando sintaxe `$input`, `$json`, `$node`
- Fazendo requisições HTTP com `$helpers`
- Trabalhando com datas usando DateTime
- Resolvendo erros de node Code

---

## Acesso a Dados

### Dados de Entrada

```javascript
// Todos os itens de entrada
const items = $input.all();

// Primeiro item (atual no loop)
const firstItem = $json;

// Item específico por índice
const thirdItem = $input.item(2);

// Todos os dados como array
const allData = $input.all().map(item => item.json);
```

### Dados de Outros Nodes

```javascript
// Dados do node anterior
const prevData = $node["Nome do Node Anterior"].json;

// Campo específico de outro node
const email = $node["Webhook"].json.body.email;

// Todos os itens de outro node
const allItems = $node["HTTP Request"].all();
```

### Dados de Execução

```javascript
// ID da execução atual
const executionId = $executionId;

// ID do workflow
const workflowId = $workflow.id;

// Nome do workflow
const workflowName = $workflow.name;

// Timestamp atual
const now = $now;
```

---

## Retornando Dados

### Item Único

```javascript
return {
  json: {
    name: "João",
    email: "joao@exemplo.com"
  }
};
```

### Múltiplos Itens

```javascript
return [
  { json: { id: 1, name: "Item 1" } },
  { json: { id: 2, name: "Item 2" } },
  { json: { id: 3, name: "Item 3" } }
];
```

### Preservar Dados de Entrada

```javascript
return {
  json: {
    ...$json,  // Spread dos dados originais
    processedAt: new Date().toISOString(),
    status: "processed"
  }
};
```

---

## Requisições HTTP

### GET Simples

```javascript
const response = await $helpers.httpRequest({
  method: 'GET',
  url: 'https://api.exemplo.com/dados'
});

return { json: response };
```

### POST com Body

```javascript
const response = await $helpers.httpRequest({
  method: 'POST',
  url: 'https://api.exemplo.com/users',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer ' + $credentials.apiToken
  },
  body: {
    name: $json.name,
    email: $json.email
  }
});

return { json: response };
```

### Com Tratamento de Erro

```javascript
try {
  const response = await $helpers.httpRequest({
    method: 'GET',
    url: 'https://api.exemplo.com/dados'
  });
  return { json: { success: true, data: response } };
} catch (error) {
  return { json: { success: false, error: error.message } };
}
```

---

## Manipulação de Datas (Luxon)

O n8n usa Luxon para datas. `DateTime` está disponível globalmente.

### Data Atual

```javascript
const now = DateTime.now();
const formatted = now.toFormat('yyyy-MM-dd HH:mm:ss');
```

### Parsing de Datas

```javascript
// De ISO string
const date = DateTime.fromISO('2024-01-15T10:30:00Z');

// De formato específico
const date2 = DateTime.fromFormat('15/01/2024', 'dd/MM/yyyy');

// De timestamp
const date3 = DateTime.fromMillis(1705315800000);
```

### Formatação

```javascript
const date = DateTime.now();

// Formatos comuns
date.toFormat('yyyy-MM-dd');          // 2024-01-15
date.toFormat('dd/MM/yyyy');          // 15/01/2024
date.toFormat('HH:mm:ss');            // 10:30:00
date.toFormat('yyyy-MM-dd HH:mm:ss'); // 2024-01-15 10:30:00
date.toISO();                         // 2024-01-15T10:30:00.000Z
```

### Operações

```javascript
const now = DateTime.now();

// Adicionar/subtrair
const tomorrow = now.plus({ days: 1 });
const lastWeek = now.minus({ weeks: 1 });
const nextMonth = now.plus({ months: 1 });

// Comparação
const isAfter = now > someDate;
const diff = now.diff(someDate, 'days').days;

// Início/fim de período
const startOfDay = now.startOf('day');
const endOfMonth = now.endOf('month');
```

### Timezone

```javascript
// Converter timezone
const utc = DateTime.now().setZone('UTC');
const sp = DateTime.now().setZone('America/Sao_Paulo');

// Criar com timezone específica
const date = DateTime.now().setZone('America/New_York');
```

---

## Padrões Comuns

### Processar Todos os Itens

```javascript
const results = [];

for (const item of $input.all()) {
  const processed = {
    ...item.json,
    processed: true,
    timestamp: DateTime.now().toISO()
  };
  results.push({ json: processed });
}

return results;
```

### Filtrar Itens

```javascript
const filtered = $input.all()
  .filter(item => item.json.status === 'active')
  .map(item => ({
    json: {
      id: item.json.id,
      name: item.json.name
    }
  }));

return filtered;
```

### Agrupar Dados

```javascript
const items = $input.all().map(i => i.json);

const grouped = items.reduce((acc, item) => {
  const key = item.category;
  if (!acc[key]) acc[key] = [];
  acc[key].push(item);
  return acc;
}, {});

return { json: grouped };
```

### Validação de Dados

```javascript
const data = $json;

if (!data.email || !data.email.includes('@')) {
  throw new Error('Email inválido');
}

if (!data.name || data.name.length < 2) {
  throw new Error('Nome deve ter pelo menos 2 caracteres');
}

return { json: { valid: true, data } };
```

---

## Erros Comuns

### ❌ Esquecendo $json

```javascript
// ERRADO
const email = email;

// CORRETO
const email = $json.email;
```

### ❌ Não retornando formato correto

```javascript
// ERRADO - retornando objeto simples
return { name: "João" };

// CORRETO - retornando com json wrapper
return { json: { name: "João" } };
```

### ❌ Async sem await

```javascript
// ERRADO
const response = $helpers.httpRequest({...});

// CORRETO
const response = await $helpers.httpRequest({...});
```

### ❌ Acessando node inexistente

```javascript
// Causa erro se node "Webhook" não existir
const data = $node["Webhook"].json;

// Mais seguro
const webhookNode = $node["Webhook"];
const data = webhookNode ? webhookNode.json : null;
```

---

## Modo de Execução

### Run Once for All Items (Padrão)

Código executa uma vez com acesso a todos os itens via `$input.all()`.

### Run Once for Each Item

Código executa para cada item separadamente. `$json` contém o item atual.

---

## Funções Embutidas

```javascript
// Gerar UUID
const id = $helpers.generateUuid();

// Pausar execução (ms)
await $helpers.sleep(1000);

// Acessar credenciais
const token = $credentials.apiToken;

// Dados estáticos do workflow (persistem entre execuções)
const data = await $getWorkflowStaticData('global');
data.counter = (data.counter || 0) + 1;
```

---

## Integração com Outras Skills

- **n8n Expression Syntax** - Expressões em campos de parâmetros
- **n8n Node Configuration** - Quando usar Code vs outros nodes
- **n8n Workflow Patterns** - Onde Code nodes se encaixam em fluxos
