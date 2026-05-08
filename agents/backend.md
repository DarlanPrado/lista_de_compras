# Agente — Backend

## Estado atual do repositório

**Não há backend implementado.** Não crie pastas `backend/` ou código Go neste repositório **a menos que uma issue/PR explicitamente autorize** a introdução do servidor.

Este arquivo existe para **alinhar expectativas** quando o backend for adicionado.

## Stack planejada (PRD)

| Camada | Tecnologia |
|--------|------------|
| Linguagem | Go |
| HTTP | Gin |
| Banco | PostgreSQL |
| Driver | `pgx` |
| Auth | JWT (expiração 7 dias) |
| Senha | bcrypt |

## Estrutura sugerida (futura)

```
backend/
├── cmd/server/main.go
├── internal/
│   ├── auth/
│   ├── handlers/
│   ├── middleware/
│   ├── models/
│   └── repository/
├── go.mod
└── go.sum
```

## Contrato

Implementação deve respeitar [`docs/API_RULES.md`](../docs/API_RULES.md). Mudanças de contrato exigem atualização desse doc + coordenação com frontend.

## Testes (quando existir código Go)

- Testes de handler com `httptest`.
- Testes de repositório com banco de teste ou containers (decidir na issue).

## Até lá

Agentes de frontend devem assumir **API ausente** e usar mocks. Este agente não deve “inventar” endpoints fora do documento de API.
