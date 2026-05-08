# Instruções para o GitHub Copilot — Lista de Compras

Este repositório é construído principalmente com assistência de IA. **Siga estas regras sempre.**

## Fonte de verdade

1. Produto e escopo: [`README.md`](../README.md) (PRD v1.0).
2. Arquitetura e convenções: [`docs/ARCHITECTURE.md`](../docs/ARCHITECTURE.md).
3. Contrato HTTP (planejado): [`docs/API_RULES.md`](../docs/API_RULES.md).
4. Exemplos de código esperado: [`EXAMPLES.md`](../EXAMPLES.md).

## Stack obrigatória

| Área | Tecnologia |
|------|------------|
| Framework | Vue 3 com Composition API e `<script setup lang="ts">` |
| Linguagem | TypeScript (strict; evitar `any`) |
| Build | Vite (`frontend/`) |
| Estado | Pinia (nunca Vuex) |
| Rotas | Vue Router |
| Utilitários | VueUse quando fizer sentido |
| Estilo | Tailwind CSS |
| Testes unitários | Vitest + `@vue/test-utils` |
| Testes E2E | Playwright |

## Backend

**O backend (Go + Gin + PostgreSQL) ainda não existe neste repositório.**

- Não invente endpoints que contradigam [`docs/API_RULES.md`](../docs/API_RULES.md).
- Para desenvolvimento frontend, use **mocks** (MSW, `fetch` mockado em testes, ou variáveis de ambiente apontando para stub).
- Quando integrar com API real, centralize chamadas em `services/` e no cliente HTTP em `shared/api/` (ver arquitetura).

## Arquitetura — Feature-Sliced (frontend)

Estrutura em `frontend/src/`:

`app/` · `pages/` · `widgets/` · `features/` · `entities/` · `shared/` · `composables/` · `services/` · `stores/` · `types/` · `styles/`

**Regra de dependência:** cada camada só importa camadas “abaixo”: `pages` → `widgets` / `features`; `widgets` → `features` / `entities`; `features` → `entities` / `shared`; `entities` → `shared`; `shared` não importa domínio da aplicação.

**Evitar:**

- Pastas só por tipo técnico na raiz de `src/` (`components/`, `views/` soltos).
- Mixins (preferir composables).
- Lógica de negócio pesada em `<template>` (extrair para composables ou stores).

Detalhes: [`docs/ARCHITECTURE.md`](../docs/ARCHITECTURE.md).

## TDD (obrigatório)

1. Cada issue/feature deve declarar **critérios de aceite** e **testes planejados** (Vitest + Playwright quando houver fluxo de usuário).
2. Preferência: escrever ou atualizar testes **antes** ou **em lockstep** com a implementação.
3. Antes de considerar a tarefa pronta: `pnpm test:unit` (e `pnpm exec playwright test` quando E2E aplicável) deve passar no `frontend/`.
4. Bugs: incluir **teste de regressão** que falharia antes da correção.

## Convenções de código

- ESLint + Prettier do projeto (`frontend/`): não desligue regras sem motivo documentado.
- Imports: usar alias `@/` para `src/` quando configurado no Vite.
- Componentes: nomes em PascalCase; um componente principal por arquivo quando possível.
- Stores Pinia: pequenas e focadas; preferir composables para lógica reutilizável sem estado global.
- Acessibilidade: botões reais, labels em inputs, foco visível (ver [`docs/DESIGN_SYSTEM.md`](../docs/DESIGN_SYSTEM.md)).

## Documentação ao mudar comportamento

Se alterar fluxo de API, regras de negócio ou estrutura de pastas, atualize os `docs/` correspondentes no mesmo PR quando possível.

## Agentes e papéis (referência)

Instruções mais específicas por papel: pasta [`agents/`](../agents/).
