# API Rules — Lista de Compras

Contrato planejado entre SPA e backend REST. **O servidor Go ainda não está implementado neste repositório** — use mocks no frontend e testes até a API existir.

## Base URL

Definir via variável de ambiente (ex.: `VITE_API_BASE_URL`). Sem barra final obrigatória; normalizar no cliente HTTP.

## Autenticação

Rotas protegidas exigem header:

```http
Authorization: Bearer <jwt>
```

JWT emitido no login/register; expiração **7 dias** (PRD). Frontend: armazenar de forma segura para o MVP (ex.: `localStorage` — revisar hardening depois).

## Formato de erro (recomendado)

Alinhar implementação futura a algo previsível para o cliente:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Mensagem legível"
  }
}
```

Status HTTP: `400` validação; `401` não autenticado; `403` proibido; `404` recurso; `409` conflito (ex.: e-mail já cadastrado); `500` interno.

Cliente deve mapear para mensagens de UI sem vazar detalhes internos.

---

## Endpoints

### Auth

#### `POST /auth/register`

Público. Cria usuário.

**Body:**

```json
{
  "email": "ana@email.com",
  "password": "minhasenha"
}
```

**Sucesso:** `201` + body com token e/ou usuário (definir schema único na implementação).

**Erros:** `409` e-mail duplicado; `400` senha curta ou e-mail inválido.

#### `POST /auth/login`

Público.

**Body:**

```json
{
  "email": "ana@email.com",
  "password": "minhasenha"
}
```

**Sucesso:** `200` + JWT.

**Erro:** `401` — UI sempre mensagem genérica tipo **"E-mail ou senha incorretos"** (PRD).

---

### Items

Todos exigem `Authorization`.

#### `GET /items`

Lista todos os itens do usuário autenticado.

**Sucesso:** `200` + array de itens ordenado (mais recentes primeiro — PRD).

#### `POST /items`

Cria item.

**Body:**

```json
{
  "name": "Arroz"
}
```

**Sucesso:** `201` + item criado.

**Erro:** `400` se `name` vazio ou só espaços.

#### `PATCH /items/:id`

Atualiza item (ex.: alternar comprado).

**Body (exemplo):**

```json
{
  "is_checked": true
}
```

**Sucesso:** `200` + item atualizado.

**Erros:** `404` item inexistente ou de outro usuário.

#### `DELETE /items/:id`

Remove um item.

**Sucesso:** `204`.

**Erros:** `404`.

#### `DELETE /items?checked=true`

Remove **todos** os itens com `is_checked === true` do usuário.

**Sucesso:** `204` ou `200` com contagem — definir na implementação e documentar aqui.

---

## Convenções para o frontend

1. **Um cliente HTTP** em `shared/api/` (wrapper `fetch` com tipos, JSON parse, header Authorization).
2. **Services** por domínio (`authService`, `itemsService`) chamando o cliente.
3. **Não** espalhar URLs em componentes.
4. Enquanto não houver backend: **MSW**, mocks em Vitest, ou stub que retorna dados fixos; manter assinaturas iguais a este documento para migração trivial.

## Segurança (backend futuro)

Senhas com hash **bcrypt**; nunca logar tokens ou senhas.
