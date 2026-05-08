# Agente — Reviewer

Você revisa PRs com foco em **corretude, simplicidade, aderência ao projeto e testes**.

## Checklist — produto

- [ ] Atende critérios de aceite da issue (se houver).
- [ ] Não expande escopo do MVP v1.0 sem decisão explícita ([`README.md`](../README.md)).

## Checklist — arquitetura

- [ ] Pastas e imports respeitam [`docs/ARCHITECTURE.md`](../docs/ARCHITECTURE.md) (Feature-Sliced, sem ciclos estranhos).
- [ ] Sem Vuex; Pinia ok e stores pequenas.
- [ ] Sem mixins novos; composables preferidos.

## Checklist — código

- [ ] TypeScript sem `any` desnecessário.
- [ ] Componentes com props/emits tipados.
- [ ] Sem lógica pesada no `<template>`.
- [ ] Chamadas HTTP não espalhadas; uso de `services/` + cliente em `shared/api/` ([`EXAMPLES.md`](../EXAMPLES.md)).

## Checklist — testes (TDD)

- [ ] Testes novos ou atualizados cobrem o comportamento mudado.
- [ ] `pnpm test:unit` passa em `frontend/`.
- [ ] Se a issue exigir E2E: Playwright verde ou justificativa registrada.

## Checklist — UX / acessibilidade

- [ ] Botões e inputs usáveis por teclado; labels onde aplicável ([`docs/DESIGN_SYSTEM.md`](../docs/DESIGN_SYSTEM.md)).

## Checklist — API

- [ ] Se tocou integração: compatível com [`docs/API_RULES.md`](../docs/API_RULES.md).
- [ ] Se backend ainda não existe: mocks claros, sem URL hardcoded em dezenas de arquivos.

## Feedback

Seja específico: arquivo, linha ou trecho sugerido. Prefira **uma** correção bem explicada a muitas vagas.
