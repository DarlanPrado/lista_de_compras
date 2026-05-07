# 🛒 Lista de Compras
**Documento de Produto — PRD v1.0**
Maio de 2026 | Versão inicial

---

## 1. Visão Geral do Produto

O Lista de Compras é um aplicativo web simples e focado que permite ao usuário gerenciar listas de compras de forma prática, com acesso seguro via login. O objetivo é ser leve, rápido e sem fricção — o usuário entra, adiciona o que precisa comprar e marca o que já comprou.

| Campo | Valor |
|---|---|
| Produto | Lista de Compras |
| Tipo | Aplicação Web (SPA + API REST) |
| Público-alvo | Qualquer pessoa que faz compras regularmente |
| Problema resolvido | Esquecimento de itens e desorganização na hora de comprar |
| Proposta de valor | Lista simples, acessível de qualquer lugar, salva automaticamente |

---

## 2. Persona

> **👤 Ana — A compradora do dia a dia**
>
> **Idade:** 28 anos | **Perfil:** trabalha fora, faz compras semanalmente
>
> **Dor:** esquece itens no mercado, usa papel e perde a lista
>
> **Desejo:** ter uma listinha rápida de acessar pelo celular, sem complicação
>
> **Critério de sucesso:** conseguir adicionar itens em menos de 5 segundos

---

## 3. Funcionalidades

### 3.1 Autenticação (Login / Cadastro)

O que o usuário pode fazer:
- Criar uma conta com e-mail e senha
- Fazer login com as credenciais cadastradas
- Fazer logout da aplicação
- Recuperar senha via e-mail *(fase 2)*

### 3.2 Lista de Compras

O que o usuário pode fazer:
- Adicionar um item à lista (texto livre)
- Marcar um item como comprado (check)
- Desmarcar um item já marcado
- Remover um item da lista
- Ver todos os itens separados em "a comprar" e "comprados"
- Limpar todos os itens comprados de uma vez

**Fora do escopo desta versão (v1.0):**
- Compartilhar lista com outros usuários
- Criar múltiplas listas
- Definir quantidade ou categoria de itens
- Integração com supermercados ou preços

---

## 4. Fluxos Principais

### 4.1 Fluxo de Cadastro

| Passo | Ação |
|---|---|
| 1 | Usuário acessa a aplicação pela primeira vez |
| 2 | Clica em "Criar conta" |
| 3 | Preenche e-mail e senha (mínimo 6 caracteres) |
| 4 | Confirma a senha |
| 5 | Clica em "Cadastrar" |
| 6 | Sistema valida e-mail único e senha válida |
| 7 | Usuário é redirecionado para a lista já logado |

### 4.2 Fluxo de Login

| Passo | Ação |
|---|---|
| 1 | Usuário acessa a aplicação |
| 2 | Insere e-mail e senha |
| 3 | Clica em "Entrar" |
| 4 | Sistema valida credenciais |
| 5a | ✅ Sucesso → redireciona para a lista |
| 5b | ❌ Erro → exibe mensagem: "E-mail ou senha incorretos" |

### 4.3 Fluxo de Uso da Lista

| Passo | Ação |
|---|---|
| 1 | Usuário vê sua lista (pode estar vazia) |
| 2 | Digita o nome do item no campo e pressiona Enter ou clica em "+" |
| 3 | Item aparece na seção "A comprar" |
| 4 | No mercado, clica no item para marcar como comprado |
| 5 | Item vai para a seção "Comprados" com visual riscado |
| 6 | Pode remover itens individuais clicando no ícone de lixeira |
| 7 | Pode limpar todos os comprados de uma vez com botão dedicado |

---

## 5. Regras de Negócio

### 5.1 Autenticação

- E-mail deve ser único no sistema — não é possível cadastrar dois usuários com o mesmo e-mail
- Senha deve ter no mínimo 6 caracteres
- Senha é armazenada com hash bcrypt (nunca em texto puro)
- Sessão expira após 7 dias (via expiração do JWT)
- Erros de login exibem mensagem genérica — não informa se o e-mail existe ou não
- Usuário não autenticado é redirecionado para `/login` ao acessar qualquer rota protegida

### 5.2 Lista de Compras

- Cada usuário tem exatamente uma lista de compras (relação 1:1)
- Item deve ter pelo menos 1 caractere não-vazio
- Item não tem limite rígido de texto, mas a interface trunca visualmente em 80 caracteres
- A ordem dos itens é preservada por ordem de criação (mais recentes primeiro)
- Itens marcados como comprados permanecem na lista até serem removidos
- "Limpar comprados" remove todos os itens marcados de forma permanente (sem confirmação na v1)
- Não há limite de itens na lista para o MVP

---

## 6. Modelo de Dados

### Entidade: User

| Campo | Tipo | Descrição |
|---|---|---|
| id | UUID | Identificador único do usuário |
| email | string | E-mail único, usado no login |
| password_hash | string | Senha com bcrypt hash |
| created_at | timestamp | Data de criação da conta |

### Entidade: Item

| Campo | Tipo | Descrição |
|---|---|---|
| id | UUID | Identificador único do item |
| user_id | UUID (FK) | Dono do item (referência ao User) |
| name | string | Nome do item (ex: "Arroz", "Leite") |
| is_checked | bool | `true` = comprado, `false` = pendente |
| created_at | timestamp | Data/hora de adição na lista |

---

## 7. Critérios de Aceitação (Definition of Done)

### Login / Cadastro
- [ ] Usuário consegue criar conta com e-mail e senha válidos
- [ ] Sistema rejeita e-mail já cadastrado com mensagem clara
- [ ] Sistema rejeita senha com menos de 6 caracteres
- [ ] Usuário consegue logar e ver sua lista
- [ ] Usuário consegue deslogar
- [ ] Rota `/lista` redireciona para `/login` se não autenticado

### Lista de Compras
- [ ] Adicionar item: campo + Enter ou botão "+"
- [ ] Campo vazio não adiciona item
- [ ] Item aparece imediatamente após adição (sem refresh)
- [ ] Clicar no item alterna entre comprado / não comprado
- [ ] Itens comprados aparecem visualmente diferentes (riscado + opacidade)
- [ ] Botão de remover item (lixeira) funciona individualmente
- [ ] Botão "Limpar comprados" remove apenas os marcados
- [ ] Lista é persistida: ao recarregar a página, os itens continuam lá

---

## 8. Mapa de Telas

| Tela | Rota | Acesso |
|---|---|---|
| Login | `/login` | Pública |
| Cadastro | `/cadastro` | Pública |
| Lista de Compras | `/lista` | Protegida (requer login) |

---

## 9. Endpoints da API (Go)

> Todas as rotas protegidas exigem o header: `Authorization: Bearer <token>`

| Método | Rota | Auth? | Descrição |
|---|---|---|---|
| `POST` | `/auth/register` | Não | Cria novo usuário |
| `POST` | `/auth/login` | Não | Retorna JWT |
| `GET` | `/items` | Sim | Lista todos os itens do usuário autenticado |
| `POST` | `/items` | Sim | Cria um novo item |
| `PATCH` | `/items/:id` | Sim | Atualiza `is_checked` do item |
| `DELETE` | `/items/:id` | Sim | Remove um item individual |
| `DELETE` | `/items?checked=true` | Sim | Remove todos os itens comprados |

### Exemplos de payload

**POST /auth/register**
```json
{
  "email": "ana@email.com",
  "password": "minhasenha"
}
```

**POST /items**
```json
{
  "name": "Arroz"
}
```

**PATCH /items/:id**
```json
{
  "is_checked": true
}
```

---

## 10. Stack Tecnológica

| Camada | Tecnologia | Motivo |
|---|---|---|
| Frontend | Vue 3 + Vite | Composition API, leve e produtivo |
| Estilo | Tailwind CSS | Produtividade no CSS sem arquivos extras |
| Backend | Go (Golang) | Alta performance, binário único, simples de deployar |
| Roteamento | Gin | Middleware de auth JWT fácil de plugar |
| Banco de Dados | PostgreSQL | Relacional, robusto, ótimo suporte no Go via `pgx` |
| Auth | JWT (gerado no Go) | Token salvo no localStorage, validado no backend |

### Estrutura de pastas sugerida

```
lista-compras/
├── backend/                  # Go
│   ├── cmd/
│   │   └── server/
│   │       └── main.go
│   ├── internal/
│   │   ├── auth/             # JWT, hash de senha
│   │   ├── handlers/         # Handlers HTTP (Gin)
│   │   ├── middleware/        # Auth middleware
│   │   ├── models/           # Structs User e Item
│   │   └── repository/       # Queries SQL (pgx)
│   ├── go.mod
│   └── go.sum
│
└── frontend/                 # Vue 3
    ├── src/
    │   ├── components/       # ListItem.vue, AddItemForm.vue...
    │   ├── pages/            # LoginPage.vue, ListPage.vue...
    │   ├── stores/           # Pinia: auth.ts, items.ts
    │   ├── services/         # api.ts (fetch wrapper)
    │   └── router/           # Vue Router com guard de auth
    ├── index.html
    └── vite.config.ts
```

---

## 11. Roadmap

| Fase | Nome | Escopo |
|---|---|---|
| **v1.0 — MVP** | Login + Lista básica | Cadastro, login, adicionar, checar e remover itens |
| v1.1 | UX Polish | Animações, feedback visual, modo offline básico |
| v1.2 | Múltiplas Listas | Usuário cria e nomeia várias listas independentes |
| v2.0 | Social | Compartilhar lista com outro usuário em tempo real |

---

*Lista de Compras — PRD v1.0 | Maio 2026*
