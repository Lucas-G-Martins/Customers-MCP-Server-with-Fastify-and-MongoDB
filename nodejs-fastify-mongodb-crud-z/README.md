# CRUD API com Node.js, Fastify e MongoDB

Esse projeto demonstra como construir uma API CRUD de clientes usando Node.js, Fastify e MongoDB. Ele usa autenticação JWT, geração de service token com rate limiting e testes com o Node.js Test Runner.

O conteúdo foi desenvolvido com base nos ensinamentos da pós-graduação em Engenharia de IA Aplicada e no material disponibilizado pelo professor Erick Wendel.

## Descrição

- API REST para gerenciamento de clientes
- Autenticação JWT para rotas protegidas
- Geração de service token com limite de requisições
- Testes automatizados com Node.js Test Runner

## Iniciando

### Pré-requisitos
- Docker e Docker Compose
- Node.js 20 ou superior
- MongoDB local ou na nuvem

### Instalação

1. Clone o repositório:
   ```bash
git clone https://github.com/SEU_USUARIO/NOME_DO_REPO.git
cd NOME_DO_REPO
```

2. Instale as dependências:
   ```bash
npm ci
```

### Executando os testes

```bash
docker-compose up -d mongodb
npm test
```

### Executando o projeto

```bash
docker-compose up -d mongodb
npm start
```

A API ficará disponível em `http://localhost:9999`.

## Autenticação e autorização

A aplicação usa JWT para proteção das rotas. Apenas `GET /v1/health` é pública.

A geração de service token (`/v1/auth/service-token`) exige credenciais válidas e o valor secreto `adminSuperSecret`.

Usuários de exemplo:

| Role   | Username      | Password | Permissões                  |
|--------|---------------|----------|------------------------------|
| admin  | lucasgabriel  | 123123   | read, create, update, delete |
| member | ananeri       | 1234     | read only                    |

#### Gerar um service token

```bash
curl -X POST http://localhost:9999/v1/auth/service-token \
  -H "Content-Type: application/json" \
  -d '{"username": "lucasgabriel", "password": "123123", "adminSuperSecret": "AM I THE BOSS?"}'
```

Resposta de exemplo:

```json
{ "role": "admin", "serviceToken": "550e8400-e29b-41d4-a716-446655440000" }
```

Use o token retornado como Bearer token nas chamadas à API:

```bash
export SERVICE_TOKEN="<uuid-from-response>"
curl http://localhost:9999/v1/customers \
  -H "Authorization: Bearer $SERVICE_TOKEN"
```

> ⚠️ As requisições de service token são limitadas a **3 requests por minuto** por token. Exceder esse limite retorna `429 Too Many Requests`.

#### Login

```bash
curl -X POST http://localhost:9999/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username": "lucasgabriel", "password": "123123"}'
```

#### Login como membro

```bash
curl -X POST http://localhost:9999/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username": "ananeri", "password": "1234"}'
```

Ambos retornam:

```json
{ "token": "<jwt-token>" }
```

Use o token como `Authorization: Bearer <token>` nas rotas protegidas.

## Endpoints

1. **Health check** — público (GET)
   ```bash
   curl http://localhost:9999/v1/health
   ```

2. **Criar um cliente** — admin apenas (POST)
   ```bash
   curl -X POST http://localhost:9999/v1/customers \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer $TOKEN" \
     -d '{"name": "John Doe", "phone": "123456789"}'
   ```

3. **Listar clientes** — admin e member (GET)
   ```bash
   curl http://localhost:9999/v1/customers \
     -H "Authorization: Bearer $TOKEN"
   ```

4. **Buscar cliente por ID** — admin e member (GET)
   ```bash
   curl http://localhost:9999/v1/customers/<customer_id> \
     -H "Authorization: Bearer $TOKEN"
   ```

5. **Atualizar cliente** — admin apenas (PUT)
   ```bash
   curl -X PUT http://localhost:9999/v1/customers/<customer_id> \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer $TOKEN" \
     -d '{"name": "Jane Doe", "phone": "987654321"}'
   ```

6. **Excluir cliente** — admin apenas (DELETE)
   ```bash
   curl -X DELETE http://localhost:9999/v1/customers/<customer_id> \
     -H "Authorization: Bearer $TOKEN"
   ```

## License

Este projeto está licenciado sob a MIT License. Veja o arquivo [LICENSE](LICENSE) para mais detalhes.

## Agradecimentos

- [Fastify](https://www.fastify.io/)
- [MongoDB](https://www.mongodb.com/)
- [Node.js Test Runner](https://nodejs.org/en/docs/guides/test-runner/)
