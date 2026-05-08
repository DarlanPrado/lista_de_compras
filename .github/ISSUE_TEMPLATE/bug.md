---
name: Bug (com regressão de teste)
about: Reportar defeito com contexto suficiente para reprodução e teste automatizado
title: "[bug] "
labels: []
assignees: []
---

## Comportamento esperado

<!-- O que deveria acontecer segundo PRD ou UX. -->

## Comportamento atual

<!-- O que acontece hoje (screenshots ajudam). -->

## Passos para reproduzir

1.
2.
3.

## Ambiente

- SO / navegador:
- Branch ou commit:
- Frontend: `pnpm dev` ou build?

## Severidade

<!-- Baixa / média / alta / bloqueante -->

## Contexto técnico (opcional)

<!-- Stack trace, logs da rede, componente ou store suspeito. -->

```text

```

## Teste de regressão (obrigatório)

<!-- Após o fix, deve existir um teste que falharia sem a correção. -->

### Vitest

- [ ] Descrever caso (componente/composable/store):

### Playwright (se for bug de fluxo ou integração)

- [ ] Descrever cenário E2E:

### Comandos antes de fechar

```bash
cd frontend && pnpm test:unit
# se aplicável:
pnpm exec playwright test
```

## Integração com API

- [ ] Bug reproduzível sem backend (mock / estado local)
- [ ] Relacionado à API — lembrar: backend pode não existir ainda; usar `docs/API_RULES.md` e mocks

## Notas para IA / Copilot

-
