# AI Context — Lista de Compras

Documento compacto para agentes de IA. Detalhes completos: [`README.md`](../README.md).

## Produto

App web para gerenciar **uma lista de compras por usuário**, com login. Leve, rápido, uso no celular.

| Campo | Valor |
|-------|-------|
| Público | Pessoas que fazem compras com frequência |
| Problema | Esquecer itens e desorganização |
| Valor | Lista simples, de qualquer lugar, persistida |

## Persona

**Ana** — 28 anos, trabalha fora, compras semanais. Dor: esquece itens e perde lista em papel. Sucesso: adicionar item em **menos de 5 segundos**.

## MVP v1.0 — funcionalidades

### Autenticação

- Cadastro (e-mail + senha), login, logout.
- Recuperação de senha: **fora do escopo v1**.

### Lista

- Adicionar item (texto livre); Enter ou botão "+".
- Marcar/desmarcar comprado.
- Remover item.
- Seções **A comprar** e **Comprados** (visual diferenciado para comprados).
- Limpar todos os comprados de uma vez.

### Fora do escopo v1

Múltiplas listas, compartilhamento, quantidade/categoria, integração com lojas/preços.

## Regras de negócio (resumo)

### Auth

- E-mail único.
- Senha mínimo **6** caracteres.
- Backend (quando existir): hash bcrypt; JWT com expiração **7 dias**; mensagem de login genérica sem revelar se e-mail existe.
- Rotas protegidas → não autenticado vai para `/login`.

### Lista

- **1 lista por usuário** (1:1).
- Nome do item: pelo menos 1 caractere não branco.
- Sem limite rígido de tamanho; UI trunca visualmente em **80** caracteres.
- Ordem: **mais recentes primeiro**.
- Limpar comprados: permanente, **sem confirmação** na v1.

## Modelo de dados (conceitual)

**User:** `id` (UUID), `email`, `password_hash`, `created_at`.

**Item:** `id` (UUID), `user_id`, `name`, `is_checked`, `created_at`.

## Telas e rotas (PRD)

| Tela | Rota | Acesso |
|------|------|--------|
| Login | `/login` | Pública |
| Cadastro | `/cadastro` | Pública |
| Lista | `/lista` | Protegida |

## Backend

**Não implementado neste repositório.** Contrato HTTP planejado: [`API_RULES.md`](API_RULES.md). Frontend deve usar mocks até a API existir.

## Testes

Projeto segue **TDD**: toda feature deve listar testes na issue; Vitest obrigatório; Playwright quando houver jornada crítica.
