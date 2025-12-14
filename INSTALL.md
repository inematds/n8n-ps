# Guia de Instalação

> [English version](INSTALL-EN.md)

## Pré-requisitos

- [Claude Code](https://claude.ai/claude-code) instalado
- Uma instância n8n (cloud ou self-hosted)
- Node.js 18+ (para npx)

---

## Passo 1: Clone Este Repositório

```bash
git clone https://github.com/inematds/n8n-ps.git
cd n8n-ps
```

---

## Passo 2: Obtenha Sua Chave de API do n8n

1. Faça login na sua instância n8n
2. Vá para **Settings** (ícone de engrenagem na barra lateral)
3. Clique em **API**
4. Clique em **Create API Key**
5. Copie a chave gerada

---

## Passo 3: Instale o n8n MCP

Execute este comando, substituindo os placeholders:

```bash
claude mcp add n8n-mcp-api \
  -e MCP_MODE=stdio \
  -e LOG_LEVEL=error \
  -e DISABLE_CONSOLE_OUTPUT=true \
  -e N8N_API_URL=https://SUA-INSTANCIA.app.n8n.cloud \
  -e N8N_API_KEY=sua-chave-api-aqui \
  -- npx -y n8n-mcp
```

### Para n8n Cloud

Substitua `SUA-INSTANCIA` com seu subdomínio do n8n cloud:
```
N8N_API_URL=https://minhaempresa.app.n8n.cloud
```

### Para n8n Self-Hosted

Use a URL da sua instância n8n:
```
N8N_API_URL=https://n8n.minhaempresa.com
```

---

## Passo 4: Verifique a Instalação

Inicie o Claude Code no diretório do projeto:

```bash
claude
```

Então pergunte ao Claude:
```
Quais skills de n8n você tem disponíveis?
```

Claude deve listar as 8 skills do diretório `.claude/skills/`.

---

## Passo 5: Teste a Conexão MCP

Pergunte ao Claude:
```
Liste meus workflows do n8n
```

Se configurado corretamente, Claude usará o MCP para listar os workflows da sua instância n8n.

---

## Solução de Problemas

### "MCP not found" ou "n8n-mcp-api not available"

1. Verifique se o MCP foi adicionado:
   ```bash
   claude mcp list
   ```

2. Verifique se as variáveis de ambiente estão corretas

3. Tente remover e adicionar novamente:
   ```bash
   claude mcp remove n8n-mcp-api
   claude mcp add n8n-mcp-api ...
   ```

### "API key invalid" ou "401 Unauthorized"

1. Regenere a chave de API em n8n Settings → API
2. Verifique se há espaços extras na chave
3. Certifique-se de que a chave não expirou

### "Cannot connect to n8n"

1. Verifique se N8N_API_URL está correto
2. Verifique se sua instância n8n está rodando
3. Para self-hosted: certifique-se de que a API está habilitada nas configurações do n8n

---

## Atualizando

Para obter as skills mais recentes:

```bash
cd n8n-ps
git pull origin main
```

As skills estarão automaticamente disponíveis na próxima vez que você iniciar o Claude Code.

---

## Desinstalando

Remova o MCP:
```bash
claude mcp remove n8n-mcp-api
```

Delete o repositório:
```bash
rm -rf n8n-ps
```
