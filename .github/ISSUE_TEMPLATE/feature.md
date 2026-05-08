---
name: Feature (TDD)
about: Nova funcionalidade com critérios de aceite e testes obrigatórios
title: "[feat] "
labels: []
assignees: []
---

## Objetivo

<!-- Uma frase: o que entregar e por quê. -->

## Contexto / usuário

<!-- Persona (ex.: Ana do PRD), motivação e onde isso aparece na UI ou no fluxo. -->

## Escopo

### Dentro do escopo

-

### Fora do escopo (desta issue)

-

## Critérios de aceite

<!-- Lista verificável. Use linguagem de produto + comportamento observável. -->

- [ ]
- [ ]

## Testes (obrigatório — TDD)

<!-- Liste casos concretos. Marque [ ] antes de implementar; [x] ao fechar a PR. -->

### Vitest (unitário / componente)

- [ ] Descreva cenário 1 (Given / When / Then)
- [ ] Descreva cenário 2

### Playwright (E2E) — quando houver jornada de usuário ou rota nova

- [ ] Fluxo ou página afetada:
- [ ] Passos do teste automatizado (precondição → ações → resultado esperado):

### Comandos antes de fechar

Rodar no diretório `frontend/`:

```bash
pnpm test:unit
```

Se esta issue incluir E2E:

```bash
pnpm exec playwright test
```

## Camadas / arquivos afetados (opcional)

<!-- Sugestão Feature-Sliced: pages / widgets / features / entities / shared -->

-

## Integração com API

- [ ] Esta issue **não** depende de backend (apenas UI / estado local / mocks)
- [ ] Depende de backend — **backend ainda não existe**: usar mocks conforme `docs/API_RULES.md`

## Notas para IA / Copilot

<!-- Links para docs, decisões, mocks, variáveis de ambiente. -->

-
