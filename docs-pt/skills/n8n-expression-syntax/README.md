# Sintaxe de Expressões n8n

Valide a sintaxe de expressões n8n e corrija erros comuns.

---

## Propósito

Esta skill ajuda a escrever expressões n8n corretas, usando a sintaxe `{{ }}`, acessando variáveis como `$json`, `$node`, e resolvendo erros comuns de expressão.

---

## Quando Usar

- Escrevendo expressões n8n
- Usando sintaxe `{{ }}`
- Acessando variáveis `$json`, `$node`
- Resolvendo erros de expressão
- Trabalhando com dados de webhook

---

## Sintaxe Básica

### Formato

Expressões n8n são envolvidas em `{{ }}`:

```javascript
{{ expressão_aqui }}
```

### Exemplos Básicos

```javascript
// Acessar campo do item atual
{{ $json.email }}

// Acessar campo aninhado
{{ $json.user.name }}

// Acessar array
{{ $json.items[0].name }}

// Operações
{{ $json.price * 1.1 }}

// Condicionais
{{ $json.status === 'active' ? 'Sim' : 'Não' }}
```

---

## Acessando Dados

### Dados do Item Atual ($json)

```javascript
// Campo simples
{{ $json.email }}

// Campo aninhado
{{ $json.user.address.city }}

// Item de array
{{ $json.items[0].id }}

// Com valor padrão (se nulo/undefined)
{{ $json.name || 'Sem nome' }}
```

### Dados de Webhook

**IMPORTANTE**: Dados de webhook estão aninhados em `.body`!

```javascript
// ❌ ERRADO
{{ $json.email }}

// ✅ CORRETO
{{ $json.body.email }}
```

Estrutura completa do webhook:
```javascript
{{ $json.headers['content-type'] }}  // Headers
{{ $json.params.id }}                 // Parâmetros de URL
{{ $json.query.token }}               // Query string
{{ $json.body.email }}                // Corpo da requisição
```

### Dados de Outros Nodes

```javascript
// Dados de um node específico
{{ $node["Nome do Node"].json.campo }}

// Exemplo prático
{{ $node["Webhook"].json.body.email }}
{{ $node["HTTP Request"].json.data.user }}
{{ $node["Postgres"].json.name }}
```

### Dados de Execução

```javascript
{{ $executionId }}                    // ID da execução
{{ $workflow.id }}                    // ID do workflow
{{ $workflow.name }}                  // Nome do workflow
{{ $now }}                            // Timestamp atual (ISO)
{{ $today }}                          // Data de hoje
```

---

## Operações Comuns

### Strings

```javascript
// Concatenação
{{ $json.firstName + ' ' + $json.lastName }}

// Template literal
{{ `Olá, ${$json.name}!` }}

// Maiúsculas/minúsculas
{{ $json.name.toUpperCase() }}
{{ $json.name.toLowerCase() }}

// Substring
{{ $json.text.substring(0, 100) }}

// Replace
{{ $json.text.replace('antigo', 'novo') }}

// Split
{{ $json.tags.split(',')[0] }}
```

### Números

```javascript
// Operações matemáticas
{{ $json.price * 1.1 }}              // Multiplicação
{{ $json.total + $json.shipping }}    // Soma
{{ Math.round($json.price) }}         // Arredondamento
{{ $json.price.toFixed(2) }}          // Decimal fixo
```

### Arrays

```javascript
// Primeiro/último item
{{ $json.items[0] }}
{{ $json.items[$json.items.length - 1] }}

// Tamanho
{{ $json.items.length }}

// Join
{{ $json.tags.join(', ') }}

// Map (criar lista de valores)
{{ $json.users.map(u => u.email).join(', ') }}
```

### Condicionais

```javascript
// Ternário simples
{{ $json.active ? 'Ativo' : 'Inativo' }}

// Com verificação de existência
{{ $json.name ? $json.name : 'Anônimo' }}

// Operador nullish coalescing
{{ $json.name ?? 'Valor padrão' }}

// Operador OR para valores falsy
{{ $json.name || 'Valor padrão' }}

// Múltiplas condições
{{ $json.score > 90 ? 'A' : $json.score > 70 ? 'B' : 'C' }}
```

### Datas (Luxon)

```javascript
// Data atual formatada
{{ $now.toFormat('yyyy-MM-dd') }}

// Data do item formatada
{{ DateTime.fromISO($json.date).toFormat('dd/MM/yyyy') }}

// Adicionar dias
{{ $now.plus({days: 7}).toFormat('yyyy-MM-dd') }}

// Diferença entre datas
{{ $now.diff(DateTime.fromISO($json.created_at), 'days').days }}
```

---

## Erros Comuns e Soluções

### ❌ Faltando {{ }}

```javascript
// ERRADO - interpretado como texto literal
$json.email

// CORRETO
{{ $json.email }}
```

### ❌ Faltando $ antes de json

```javascript
// ERRADO
{{ json.email }}

// CORRETO
{{ $json.email }}
```

### ❌ Dados de webhook sem .body

```javascript
// ERRADO - dados estão em body!
{{ $json.email }}

// CORRETO
{{ $json.body.email }}
```

### ❌ Aspas erradas em nomes de nodes

```javascript
// ERRADO
{{ $node['Nome do Node'].json }}  // Aspas simples
{{ $node[Nome do Node].json }}    // Sem aspas

// CORRETO
{{ $node["Nome do Node"].json }}  // Aspas duplas
```

### ❌ Acessando propriedade de undefined

```javascript
// ERRADO - se user for undefined, erro!
{{ $json.user.name }}

// CORRETO - com verificação
{{ $json.user?.name }}
{{ $json.user ? $json.user.name : 'N/A' }}
```

### ❌ Misturando expressões e texto

```javascript
// ERRADO
{{ "Email: " + $json.email }}  // Funciona, mas confuso

// MELHOR - template literal
{{ `Email: ${$json.email}` }}

// OU - em campos que aceitam texto misto
Email: {{ $json.email }}
```

---

## Padrões Úteis

### Valor Padrão Seguro

```javascript
{{ $json.nome || 'Não informado' }}
{{ $json.preco ?? 0 }}
```

### Verificação de Existência

```javascript
{{ $json.items && $json.items.length > 0 ? 'Tem itens' : 'Vazio' }}
```

### Formatação de Moeda

```javascript
{{ 'R$ ' + $json.valor.toFixed(2).replace('.', ',') }}
```

### Data Formatada BR

```javascript
{{ DateTime.fromISO($json.data).toFormat('dd/MM/yyyy') }}
```

### Lista Separada por Vírgula

```javascript
{{ $json.tags.join(', ') }}
```

### Primeiro Nome

```javascript
{{ $json.nomeCompleto.split(' ')[0] }}
```

### Capitalizar

```javascript
{{ $json.nome.charAt(0).toUpperCase() + $json.nome.slice(1).toLowerCase() }}
```

---

## Contexto de Uso

### Em Campos de Parâmetros

Quando preencher campos de nodes, as expressões são avaliadas:

```javascript
// Campo "Message" do Slack
Novo pedido de {{ $json.body.customer_name }} - Total: R$ {{ $json.body.total.toFixed(2) }}
```

### Em Condições (IF Node)

```javascript
// Condição true/false
{{ $json.valor > 1000 }}

// Com múltiplas condições
{{ $json.status === 'paid' && $json.valor > 0 }}
```

### Em URLs

```javascript
https://api.exemplo.com/users/{{ $json.userId }}
```

### Em Headers HTTP

```javascript
Bearer {{ $json.token }}
```

---

## Dicas de Debug

### Ver Estrutura de Dados

1. Adicione um node **Code** temporário:
```javascript
console.log(JSON.stringify($json, null, 2));
return $input.all();
```

2. Verifique a saída na aba "Output" do node anterior

### Expressão Não Funciona

1. Verifique se está dentro de `{{ }}`
2. Verifique se usou `$` antes de `json`/`node`
3. Verifique o caminho do dado (use Code node para debug)
4. Para webhooks, lembre do `.body`

---

## Integração com Outras Skills

- **n8n Code JavaScript** - Para lógica mais complexa que expressões
- **n8n Node Configuration** - Onde expressões são usadas
- **n8n Workflow Patterns** - Padrões que usam expressões
