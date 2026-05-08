# Design System — Lista de Compras

Guia mínimo para UI consistente e previsível para implementação humana e por IA. Ajuste tokens no `tailwind.config` quando Tailwind for adicionado ao projeto.

## Princípios

1. **Mobile-first** — lista usável no celular com dedos.
2. **Contraste** — texto legível (WCAG AA como alvo).
3. **Feedback claro** — estados de foco, hover, loading e erro visíveis.

## Paleta sugerida (Tailwind)

| Token semântico | Uso | Sugestão Tailwind |
|-----------------|-----|---------------------|
| `--color-bg` | Fundo da página | `bg-slate-50` dark: `bg-slate-950` |
| `--color-surface` | Cards, painéis | `bg-white` dark: `bg-slate-900` |
| `--color-primary` | Ações principais | `emerald-600` hover `emerald-700` |
| `--color-danger` | Destruir / remover | `rose-600` |
| `--color-text` | Corpo | `slate-900` dark: `slate-100` |
| `--color-muted` | Secundário | `slate-500` |
| `--color-border` | Bordas | `slate-200` dark: `slate-700` |

Definir em `tailwind.config` como `theme.extend.colors.primary` etc. para não espalhar hexadecimais.

## Tipografia

- **Título de página:** `text-2xl font-semibold tracking-tight`
- **Subtítulo / seção:** `text-sm font-medium text-slate-500 uppercase tracking-wide`
- **Corpo:** `text-base text-slate-900`
- **Nome do item na lista:** `text-base`; truncar com `truncate max-w-*` alinhado à regra de 80 caracteres visíveis quando necessário

Fonte: stack do sistema (`font-sans`), ou fonte definida no projeto quando escolhida.

## Espaçamento

- Padding de página: `px-4 py-6` (mobile); `max-w-lg mx-auto` ou similar para leitura confortável.
- Entre seções: `space-y-6` ou `gap-4` em grids/flex.
- Lista de itens: `divide-y` ou `gap-2` entre linhas.

## Componentes — padrões

### Botão primário

- Classes base: `inline-flex items-center justify-center rounded-lg px-4 py-2 text-sm font-medium`
- Primário: cor primary + `text-white`; foco: `focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2`
- Desabilitado: `opacity-50 cursor-not-allowed`

### Botão fantasma / secundário

- `border border-slate-300 bg-transparent`; hover levemente mais escuro.

### Campo de texto (item novo, login)

- `w-full rounded-lg border border-slate-300 px-3 py-2 text-base`
- Foco: `focus:border-emerald-600 focus:ring-2 focus:ring-emerald-600/20`
- Label associada com `for` / `id`; mensagens de erro com `role="alert"` ou `aria-live="polite"`.

### Item da lista

- Área clicável para alternar comprado: mínimo **44×44px** de alvo tátil (`min-h-[44px] flex items-center`).
- Comprado: `line-through opacity-70` (alinhar ao PRD).

### Ícone de remover

- Botão com `aria-label="Remover item"` (ou texto equivalente em PT).

## Lista — seções

- Cabeçalhos **A comprar** e **Comprados** com hierarquia visual clara (`text-sm font-semibold`).
- Lista vazia: mensagem curta + mesmo campo de adicionar visível.

## Acessibilidade

- Contraste de texto sobre fundo ≥ 4.5:1 quando possível.
- Navegação por teclado em todos os controles interativos.
- Não depender só de cor para estado (combinar texto/risco/check).

## Implementação

Quando o Tailwind for configurado no `frontend/`, centralizar variantes repetidas em componentes em `shared/ui/` (ex.: `UiButton.vue`, `UiInput.vue`) em vez de copiar classes enormes em todo lugar.
