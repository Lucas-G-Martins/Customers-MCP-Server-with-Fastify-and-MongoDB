# @lucasgabriel/ciphersuite-mcp

An [MCP (Model Context Protocol)](https://modelcontextprotocol.io) server that provides AES-256-CBC encryption and decryption tools, a resource describing the algorithm, and ready-to-use prompts — all runnable directly inside VS Code Copilot Chat.

---

## What it does

| Capability | Name | Description |
|---|---|---|
| 🔧 Tool | `list_customers` | Lista todos os clientes |
| 🔧 Tool | `get_customer` | Busca um cliente por ID |
| 🔧 Tool | `create_customer` | Cria um novo cliente |
| 🔧 Tool | `update_customer` | Atualiza um cliente existente |
| 🔧 Tool | `delete_customer` | Exclui um cliente |
| 📄 Resource | `api-info` | Retorna informações sobre a API externa |
| 💬 Prompt | `find_customer_prompt` | Prompt pronto para buscar um cliente |

### How it works

- **API externa**: Conecta-se a uma API REST em `http://localhost:9999/v1` para operações CRUD.
- **Autenticação**: Usa `SERVICE_TOKEN` para autenticar na API externa.
- **Ferramentas**: Cada operação CRUD é exposta como uma ferramenta MCP.
- **Recursos**: `api-info` fornece metadados da API.
- **Prompts**: `find_customer_prompt` é um prompt pré-construído para buscar clientes.

---

## Prerequisites

- **Node.js v24+** (see `engines` in `package.json`)
- **SERVICE_TOKEN** defined in environment (obtained from external API)

---

## Installation

```bash
npm install
```

No build step is needed — the server runs TypeScript directly via Node.js native TypeScript support.

---

## Using in VS Code

### 1. Add the MCP server configuration

Create (or open) `.vscode/mcp.json` in your workspace and add:

```json
{
  "servers": {
    "customers-mcp": {
      "command": "node",
      "args": ["--experimental-strip-types", "ABSOLUTE_PATH_TO_PROJECT/src/index.ts"],
      "env": {
        "SERVICE_TOKEN": "YOUR_SERVICE_TOKEN_HERE"
      }
    }
  }
}
```

or via npm (if published):
```json
{
  "servers": {
    "customers-mcp": {
      "command": "npx",
      "args": ["-y", "@lucasgabriel/ew-customers-mcp"],
      "env": {
        "SERVICE_TOKEN": "YOUR_SERVICE_TOKEN_HERE"
      }
    }
  }
}
```

> **Tip:** You can also add this server to your user-level MCP config at `~/.vscode/mcp.json` to make it available in every workspace.

### 2. Reload VS Code

Open the Command Palette (`Cmd+Shift+P`) and run **Developer: Reload Window** (or just restart VS Code).

### 3. Use it in Copilot Chat

Open Copilot Chat (Agent mode) and try:

```
List all customers
```

```
Get customer with ID 123
```

```
Create a new customer with name "John Doe" and phone "123456789"
```

The agent will automatically call the appropriate tool and return the result.

---

## Running the MCP Inspector

The MCP Inspector lets you explore and test all tools, resources, and prompts interactively in a browser UI:

```bash
npm run mcp:inspect
```

This opens the inspector at `http://localhost:5173` and connects it to the running server.

---

## Running tests

```bash
# Run all tests once
npm test

# Run tests in watch mode (with debugger)
npm run test:dev
```

The test suite covers:

- Listing customers
- Getting customer by ID
- Creating customer
- Updating customer
- Deleting customer
- Reading the `api-info` resource
- Fetching the `find_customer_prompt` prompt
- Errors: customer not found, etc.

---

## Project structure

```
src/
  index.ts          # Entry point — connects the server to stdio transport
  application/
    customer-service.ts  # Service to interact with external API
  domain/
    customer.ts      # Customer domain model
    errors.ts        # Error definitions
  infrastructure/
    customer-http-client.ts  # HTTP client for the API
  mcp/
    server.ts        # Registration of tools, resources, and prompts
    tools/           # MCP tools (list, get, create, etc.)
    resources/       # MCP resources (api-info)
    prompts/         # MCP prompts (findCustomer)
tests/
  helpers.ts        # Test helpers
  prompts/
  resources/
  tools/
```

---

## Available scripts

| Script | Description |
|---|---|
| `npm start` | Start the server (used by MCP clients) |
| `npm run dev` | Start with file-watch and Node.js inspector |
| `npm test` | Run all tests |
| `npm run test:dev` | Run tests in watch mode |
| `npm run mcp:inspect` | Open the MCP Inspector UI |

---

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
