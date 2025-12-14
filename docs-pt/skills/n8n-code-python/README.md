# Código Python n8n

Escreva Python em nodes Code do n8n.

---

## Propósito

Esta skill ajuda a escrever código Python correto em nodes Code do n8n, usando a sintaxe específica do n8n para acesso a dados e entendendo as limitações do ambiente Python no n8n.

---

## Quando Usar

- Escrevendo Python em nodes Code
- Usando sintaxe `_input`, `_json`, `_node`
- Trabalhando com biblioteca padrão Python
- Entendendo limitações do Python no n8n

---

## Diferenças Importantes

O Python no n8n usa **underscore** ao invés de **dollar sign**:

| JavaScript | Python |
|------------|--------|
| `$json` | `_json` |
| `$input` | `_input` |
| `$node` | `_node` |
| `$now` | `_now` |

---

## Acesso a Dados

### Dados de Entrada

```python
# Todos os itens de entrada
items = _input.all()

# Primeiro item (atual no loop)
first_item = _json

# Item específico por índice
third_item = _input.item(2)

# Todos os dados como lista
all_data = [item.json for item in _input.all()]
```

### Dados de Outros Nodes

```python
# Dados do node anterior
prev_data = _node["Nome do Node Anterior"].json

# Campo específico de outro node
email = _node["Webhook"].json["body"]["email"]

# Todos os itens de outro node
all_items = _node["HTTP Request"].all()
```

### Dados de Execução

```python
# ID da execução atual
execution_id = _execution_id

# ID do workflow
workflow_id = _workflow.id

# Nome do workflow
workflow_name = _workflow.name

# Timestamp atual
now = _now
```

---

## Retornando Dados

### Item Único

```python
return {
    "name": "João",
    "email": "joao@exemplo.com"
}
```

### Múltiplos Itens

```python
return [
    {"id": 1, "name": "Item 1"},
    {"id": 2, "name": "Item 2"},
    {"id": 3, "name": "Item 3"}
]
```

### Preservar Dados de Entrada

```python
from datetime import datetime

result = dict(_json)  # Cópia dos dados originais
result["processedAt"] = datetime.now().isoformat()
result["status"] = "processed"

return result
```

---

## Biblioteca Padrão Disponível

O n8n disponibiliza módulos comuns da biblioteca padrão:

```python
import json
import datetime
import re
import math
import random
import hashlib
import base64
import urllib.parse
```

### Exemplos

```python
import json
import datetime
import re
import hashlib

# JSON
data = json.dumps({"key": "value"})
parsed = json.loads('{"key": "value"}')

# Datas
now = datetime.datetime.now()
formatted = now.strftime("%Y-%m-%d %H:%M:%S")

# Regex
match = re.search(r'\d+', "abc123def")
if match:
    number = match.group()

# Hash
hash_value = hashlib.md5("texto".encode()).hexdigest()

# Base64
encoded = base64.b64encode("texto".encode()).decode()
decoded = base64.b64decode(encoded).decode()
```

---

## Padrões Comuns

### Processar Todos os Itens

```python
from datetime import datetime

results = []

for item in _input.all():
    processed = dict(item.json)
    processed["processed"] = True
    processed["timestamp"] = datetime.now().isoformat()
    results.append(processed)

return results
```

### Filtrar Itens

```python
filtered = [
    {"id": item.json["id"], "name": item.json["name"]}
    for item in _input.all()
    if item.json.get("status") == "active"
]

return filtered
```

### Agrupar Dados

```python
from collections import defaultdict

items = [item.json for item in _input.all()]

grouped = defaultdict(list)
for item in items:
    key = item.get("category", "other")
    grouped[key].append(item)

return dict(grouped)
```

### Validação de Dados

```python
import re

data = _json

email = data.get("email", "")
if not email or "@" not in email:
    raise ValueError("Email inválido")

name = data.get("name", "")
if len(name) < 2:
    raise ValueError("Nome deve ter pelo menos 2 caracteres")

return {"valid": True, "data": data}
```

---

## Limitações do Python no n8n

### ❌ NÃO Disponível

```python
# Requisições HTTP - use JavaScript ou node HTTP Request
import requests  # NÃO FUNCIONA

# Pacotes externos
import pandas  # NÃO FUNCIONA
import numpy   # NÃO FUNCIONA

# Operações de sistema
import os      # LIMITADO
import subprocess  # NÃO FUNCIONA
```

### Alternativas

| Necessidade | Solução |
|-------------|---------|
| HTTP requests | Use node HTTP Request ou Code JS |
| Pandas/NumPy | Use serviço Python externo |
| Arquivos | Use nodes de storage do n8n |
| Subprocess | Use serviço externo via API |

---

## Datas e Horários

```python
from datetime import datetime, timedelta

# Data atual
now = datetime.now()

# Formatação
formatted = now.strftime("%Y-%m-%d %H:%M:%S")
date_only = now.strftime("%Y-%m-%d")
brazilian = now.strftime("%d/%m/%Y")

# Parsing
parsed = datetime.strptime("2024-01-15", "%Y-%m-%d")
parsed2 = datetime.strptime("15/01/2024", "%d/%m/%Y")

# Operações
tomorrow = now + timedelta(days=1)
last_week = now - timedelta(weeks=1)
hours_ago = now - timedelta(hours=3)

# Comparação
is_future = parsed > now
diff = (now - parsed).days

# ISO format
iso_string = now.isoformat()
```

---

## Erros Comuns

### ❌ Usando $ ao invés de _

```python
# ERRADO
email = $json["email"]

# CORRETO
email = _json["email"]
```

### ❌ Retornando tipo incorreto

```python
# ERRADO - não é dict ou list
return "sucesso"

# CORRETO
return {"status": "sucesso"}
```

### ❌ Acessando chave inexistente

```python
# ERRADO - causa KeyError
email = _json["email"]

# CORRETO - com valor padrão
email = _json.get("email", "")
```

### ❌ Importando pacotes não disponíveis

```python
# ERRADO
import requests
import pandas

# CORRETO - use apenas biblioteca padrão
import json
import datetime
```

---

## Quando Usar Python vs JavaScript

### Use Python quando:
- Preferência pessoal por Python
- Lógica simples de transformação
- Operações com strings e regex
- Cálculos matemáticos

### Use JavaScript quando:
- Precisa de requisições HTTP (`$helpers.httpRequest`)
- Precisa de DateTime do Luxon
- Acesso a funções específicas do n8n (`$helpers.generateUuid`)
- Melhor suporte e documentação

---

## Integração com Outras Skills

- **n8n Expression Syntax** - Expressões em campos de parâmetros
- **n8n Node Configuration** - Quando usar Code vs outros nodes
- **n8n Workflow Patterns** - Onde Code nodes se encaixam em fluxos
