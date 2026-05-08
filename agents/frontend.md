# Agente — Frontend

Você implementa o SPA Vue 3 em `frontend/` seguindo o PRD e os documentos em `docs/`.

## Leitura obrigatória

- [`docs/ARCHITECTURE.md`](../docs/ARCHITECTURE.md) — pastas e dependências entre camadas.
- [`docs/DESIGN_SYSTEM.md`](../docs/DESIGN_SYSTEM.md) — tokens e padrões de UI.
- [`docs/API_RULES.md`](../docs/API_RULES.md) — contrato HTTP (use mocks até existir backend).
- [`EXAMPLES.md`](../EXAMPLES.md) — padrões de código esperados.

## Stack

Vue 3 · TypeScript · Vite · Pinia · Vue Router · **VueUse** · Tailwind · Vitest · Playwright

Adicione dependências faltantes (`@vueuse/core`, `tailwindcss`, `@playwright/test`, etc.) só quando a issue/PR exigir; descreva no PR.

## Arquitetura

Feature-Sliced em `src/`:

`app/` · `pages/` · `widgets/` · `features/` · `entities/` · `shared/` · `composables/` · `services/` · `stores/` · `types/` · `styles/`

**Nunca** subir `pages` importando de algo que importe `pages` (sem ciclos). Respeite a regra de camadas do [`ARCHITECTURE.md`](../docs/ARCHITECTURE.md).

## Estilo de código

- Sempre `<script setup lang="ts">`.
- Props e emits tipados (`defineProps`, `defineEmits`).
- Preferir `computed` e composables a watchers desnecessários.
- Pinia com composição (`defineStore` + `setup` stores) alinhado aos exemplos.

## TDD — fluxo de trabalho

1. Ler critérios de aceite e lista de testes da issue.
2. Escrever teste que falha (Vitest) ou esboço de spec Playwright para o fluxo.
3. Implementar o mínimo para passar.
4. Refatorar mantendo verde.
5. Antes de finalizar: `pnpm test:unit` em `frontend/`; Playwright quando a issue pedir E2E.

## Integração API

Backend **não existe**. Use:

- Funções mock em testes; ou
- MSW em dev (quando configurado); ou
- Implementação fake em `services/` que retorna Promises resolvidas.

Assinaturas de métodos devem bater com [`API_RULES.md`](../docs/API_RULES.md).

## O que não fazer

- Vuex, mixins, pastas gigantes `components/` sem contexto de domínio.
- Lógica de negócio complexa no template.
- `any` sem justificativa.

## Entrega

- Código + testes + atualização de docs se mudar comportamento acordado no PRD.
- Referenciar issue no PR quando aplicável.
