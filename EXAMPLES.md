# EXAMPLES.md — Padrões ideais para IA

Exemplos **concretos** do que esperamos neste repositório. Copie o estilo, não só o texto. Caminhos assumem `frontend/src/` e stack definida em [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md).

> **Nota:** `tailwindcss`, `@vueuse/core` e `@playwright/test` podem ainda não estar instalados no `package.json` — adicione-os quando implementar features que dependam deles.

---

## 1. Entidade — tipos (`entities/item/types.ts`)

```ts
/** Modelo de item na UI (camelCase). Mapear de/para DTO snake_case no service. */
export interface ItemCardModel {
  id: string
  name: string
  isChecked: boolean
}
```

## 2. Entidade — componente de item (`entities/item/ItemCard.vue`)

UI mínima do domínio; recebe dados por props e comunica eventos. Classes Tailwind alinhadas a [`docs/DESIGN_SYSTEM.md`](docs/DESIGN_SYSTEM.md).

```vue
<script setup lang="ts">
import type { ItemCardModel } from './types'

const props = defineProps<{
  item: ItemCardModel
}>()

const emit = defineEmits<{
  toggle: [id: string]
  remove: [id: string]
}>()

function onToggle() {
  emit('toggle', props.item.id)
}

function onRemove() {
  emit('remove', props.item.id)
}
</script>

<template>
  <div
    class="flex min-h-[44px] items-center gap-3 rounded-lg border border-slate-200 bg-white px-3 py-2 dark:border-slate-700 dark:bg-slate-900"
  >
    <button
      type="button"
      class="flex h-11 w-11 shrink-0 items-center justify-center rounded-md border border-slate-300 text-slate-700 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-emerald-600"
      :aria-pressed="item.isChecked"
      :aria-label="item.isChecked ? 'Marcar como não comprado' : 'Marcar como comprado'"
      data-testid="item-toggle"
      @click="onToggle"
    >
      <span v-if="item.isChecked" aria-hidden="true">✓</span>
    </button>

    <span
      class="min-w-0 flex-1 truncate text-base text-slate-900 dark:text-slate-100"
      :class="{ 'line-through opacity-70': item.isChecked }"
      :title="item.name"
    >
      {{ item.name }}
    </span>

    <button
      type="button"
      class="rounded-md p-2 text-rose-600 hover:bg-rose-50 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-emerald-600 dark:hover:bg-rose-950"
      aria-label="Remover item"
      data-testid="item-remove"
      @click="onRemove"
    >
      🗑
    </button>
  </div>
</template>
```

---

## 3. Feature — adicionar item (`features/add-item/AddItemForm.vue` + composable)

### `features/add-item/useAddItemForm.ts`

```ts
import { ref, type Ref } from 'vue'

/** Composable pequeno: validação alinhada ao PRD (não vazio). */
export function useAddItemForm(onSubmit: (name: string) => void) {
  const name: Ref<string> = ref('')
  const error: Ref<string | null> = ref(null)

  function submit() {
    const trimmed = name.value.trim()
    if (!trimmed) {
      error.value = 'Informe o nome do item'
      return
    }
    error.value = null
    onSubmit(trimmed)
    name.value = ''
  }

  return { name, error, submit }
}
```

### `features/add-item/AddItemForm.vue`

```vue
<script setup lang="ts">
import { useAddItemForm } from './useAddItemForm'

const emit = defineEmits<{
  add: [name: string]
}>()

const { name, error, submit } = useAddItemForm((itemName) => emit('add', itemName))

function onKeydown(e: KeyboardEvent) {
  if (e.key === 'Enter') {
    e.preventDefault()
    submit()
  }
}
</script>

<template>
  <form class="flex flex-col gap-2" @submit.prevent="submit">
    <label class="sr-only" for="new-item">Novo item</label>
    <div class="flex gap-2">
      <input
        id="new-item"
        v-model="name"
        type="text"
        maxlength="200"
        autocomplete="off"
        class="min-w-0 flex-1 rounded-lg border border-slate-300 px-3 py-2 text-base focus:border-emerald-600 focus:ring-2 focus:ring-emerald-600/20"
        placeholder="Ex.: Leite"
        data-testid="add-item-input"
        @keydown="onKeydown"
      />
      <button
        type="submit"
        class="shrink-0 rounded-lg bg-emerald-600 px-4 py-2 text-sm font-medium text-white hover:bg-emerald-700 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-emerald-600"
        data-testid="add-item-submit"
      >
        +
      </button>
    </div>
    <p v-if="error" role="alert" class="text-sm text-rose-600">{{ error }}</p>
  </form>
</template>
```

---

## 4. Store Pinia — itens (`stores/items.ts`)

Store **pequena**: lista normalizada ou array simples + actions que espelham [`docs/API_RULES.md`](docs/API_RULES.md).

```ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'
import type { ItemCardModel } from '@/entities/item/types'

export const useItemsStore = defineStore('items', () => {
  const items = ref<ItemCardModel[]>([])

  const pendingItems = computed(() => items.value.filter((i) => !i.isChecked))
  const boughtItems = computed(() => items.value.filter((i) => i.isChecked))

  function addItem(name: string) {
    const id = crypto.randomUUID()
    items.value = [{ id, name, isChecked: false }, ...items.value]
  }

  function toggleItem(id: string) {
    items.value = items.value.map((i) =>
      i.id === id ? { ...i, isChecked: !i.isChecked } : i,
    )
  }

  function removeItem(id: string) {
    items.value = items.value.filter((i) => i.id !== id)
  }

  function clearBought() {
    items.value = items.value.filter((i) => !i.isChecked)
  }

  return {
    items,
    pendingItems,
    boughtItems,
    addItem,
    toggleItem,
    removeItem,
    clearBought,
  }
})
```

Em produção, essas actions chamariam `itemsService` em vez de só mutar estado local.

---

## 5. Cliente HTTP (`shared/api/http.ts`)

Wrapper único; base URL de env; JSON tipado; Bearer opcional.

```ts
const API_BASE = import.meta.env.VITE_API_BASE_URL?.replace(/\/$/, '') ?? ''

export class ApiError extends Error {
  constructor(
    message: string,
    readonly status: number,
    readonly body?: unknown,
  ) {
    super(message)
    this.name = 'ApiError'
  }
}

export async function apiFetch<T>(
  path: string,
  options: RequestInit & { token?: string | null } = {},
): Promise<T> {
  const { token, headers, ...rest } = options
  const url = `${API_BASE}${path.startsWith('/') ? path : `/${path}`}`

  const res = await fetch(url, {
    ...rest,
    headers: {
      'Content-Type': 'application/json',
      ...(token ? { Authorization: `Bearer ${token}` } : {}),
      ...headers,
    },
  })

  const text = await res.text()
  const body = text ? (JSON.parse(text) as unknown) : null

  if (!res.ok) {
    throw new ApiError(res.statusText || 'Request failed', res.status, body)
  }

  return body as T
}
```

---

## 6. Service (`services/itemsService.ts`)

Camada fina sobre `apiFetch`; tipos alinhados ao contrato.

```ts
import { apiFetch } from '@/shared/api/http'

export interface ItemDto {
  id: string
  name: string
  is_checked: boolean
}

export function createItemsService(getToken: () => string | null) {
  return {
    list(): Promise<ItemDto[]> {
      return apiFetch<ItemDto[]>('/items', { token: getToken() })
    },
    create(name: string): Promise<ItemDto> {
      return apiFetch<ItemDto>('/items', {
        method: 'POST',
        token: getToken(),
        body: JSON.stringify({ name }),
      })
    },
    patch(id: string, patch: Partial<Pick<ItemDto, 'is_checked'>>): Promise<ItemDto> {
      return apiFetch<ItemDto>(`/items/${id}`, {
        method: 'PATCH',
        token: getToken(),
        body: JSON.stringify(patch),
      })
    },
    deleteOne(id: string): Promise<void> {
      return apiFetch(`/items/${id}`, { method: 'DELETE', token: getToken() })
    },
    deleteChecked(): Promise<void> {
      return apiFetch(`/items?checked=true`, { method: 'DELETE', token: getToken() })
    },
  }
}
```

---

## 7. Guard de rota (`app/router/guards/auth.ts`)

Exemplo: token em `localStorage` (ajuste conforme auth real).

```ts
import type { NavigationGuardNext, RouteLocationNormalized } from 'vue-router'

const TOKEN_KEY = 'access_token'

export function requiresAuth(
  to: RouteLocationNormalized,
  _from: RouteLocationNormalized,
  next: NavigationGuardNext,
) {
  const token = localStorage.getItem(TOKEN_KEY)
  if (!token) {
    next({ path: '/login', query: { redirect: to.fullPath } })
    return
  }
  next()
}

export function redirectIfLoggedIn(
  _to: RouteLocationNormalized,
  _from: RouteLocationNormalized,
  next: NavigationGuardNext,
) {
  const token = localStorage.getItem(TOKEN_KEY)
  if (token) {
    next({ path: '/lista' })
    return
  }
  next()
}
```

Registrar em `router/index.ts` com `meta: { requiresAuth: true }` ou arrays de rotas protegidas — ver Vue Router 4 docs.

---

## 8. Teste Vitest — componente (`entities/item/__tests__/ItemCard.spec.ts`)

```ts
import { describe, it, expect } from 'vitest'
import { mount } from '@vue/test-utils'
import ItemCard from '../ItemCard.vue'

describe('ItemCard', () => {
  it('emite toggle ao clicar no botão de check', async () => {
    const wrapper = mount(ItemCard, {
      props: {
        item: { id: '1', name: 'Leite', isChecked: false },
      },
    })

    await wrapper.get('[data-testid="item-toggle"]').trigger('click')
    expect(wrapper.emitted('toggle')?.[0]).toEqual(['1'])
  })

  it('aplica estilo de comprado quando isChecked', () => {
    const wrapper = mount(ItemCard, {
      props: {
        item: { id: '1', name: 'Leite', isChecked: true },
      },
    })

    expect(wrapper.get('[data-testid="item-toggle"]').attributes('aria-pressed')).toBe('true')
  })
})
```

---

## 9. Teste Vitest — store com Pinia

```ts
import { describe, it, expect, beforeEach } from 'vitest'
import { setActivePinia, createPinia } from 'pinia'
import { useItemsStore } from '@/stores/items'

describe('useItemsStore', () => {
  beforeEach(() => {
    setActivePinia(createPinia())
  })

  it('addItem coloca item no topo e pendente', () => {
    const store = useItemsStore()
    store.addItem('Arroz')
    expect(store.pendingItems).toHaveLength(1)
    expect(store.pendingItems[0]?.name).toBe('Arroz')
  })
})
```

---

## 10. Playwright — fluxo mínimo (`e2e/list.spec.ts`)

Requer `pnpm add -D @playwright/test` e `pnpm exec playwright install`. Subir app com `pnpm dev` ou `webServer` no config.

```ts
import { test, expect } from '@playwright/test'

test('adiciona item e aparece na lista', async ({ page }) => {
  await page.goto('/lista')
  await page.getByTestId('add-item-input').fill('Pão')
  await page.getByTestId('add-item-submit').click()
  await expect(page.getByText('Pão', { exact: true })).toBeVisible()
})
```

---

## Resumo

| Peça | Onde |
|------|------|
| UI de domínio | `entities/` |
| Caso de uso + formulários | `features/` |
| Estado compartilhado | `stores/` |
| HTTP genérico | `shared/api/` |
| Orquestração API tipada | `services/` |
| Rotas / guards | `app/` + `router` |
| Testes | colocated `__tests__` ou `*.spec.ts` + `e2e/` |

Sempre rode **`pnpm test:unit`** antes de concluir uma task; rode Playwright quando a issue exigir E2E.
