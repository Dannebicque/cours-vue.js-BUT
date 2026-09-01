# Séance 13 : Tests Front Unitaires & d'Intégration (Vitest)

## 🎯 Objectifs de la séance

* Comprendre la pyramide des tests et la stratégie de test dans une application Nuxt 4 / Vue 3.
* Configurer et utiliser **Vitest** et **`@vue/test-utils`** avec support natif de TypeScript.
* Écrire des tests unitaires ciblés sur la logique critique : **Composables** (`useTasks`), **Stores Pinia** (`useTaskStore`), **Fonctions utilitaires** et **Composants UI**.
* Maîtriser le **Mocking** (simuler des appels d'API, des timers ou des routeurs).
* Mesurer et analyser la couverture de code (*Code Coverage*).

---

## 🧪 1. Pourquoi et Que Tester en Front-End ?

Tester n'est pas "vérifier que le bouton est bleu". Tester consiste à **sécuriser la logique métier critique** contre les régressions futures.

```
            ▲
           / \     Tests E2E (Playwright / Cypress)
          /   \    -> Parcours utilisateur complets
         /─────\
        / Tests \  Tests d'intégration (Vitest + Vue Test Utils)
       / Intégr. \ -> Interactions entre composants et stores
      /───────────\
     /    Tests    \ Tests unitaires (Vitest)
    /   Unitaires   \ -> Composables, Stores Pinia, Fonctions pures
   /─────────────────\
```

---

## ⚙️ 2. Configuration de Vitest dans Nuxt

```bash
# Installation de Vitest et des utilitaires de test Nuxt
npm install -D vitest @vue/test-utils @nuxt/test-utils happy-dom @vitest/coverage-v8
```

### Configuration `vitest.config.ts`

```typescript
// vitest.config.ts
import { defineVitestConfig } from '@nuxt/test-utils/config'

export default defineVitestConfig({
  test: {
    environment: 'happy-dom',
    globals: true,
    coverage: {
      provider: 'v8',
      reporter: ['text', 'html']
    }
  }
})
```

---

## 📝 3. Exemples Pratiques de Tests Unitaires

### A. Tester un Composable Métier (`useTasks.spec.ts`)

```typescript
// tests/composables/useTasks.spec.ts
import { describe, it, expect, beforeEach } from 'vitest'
import { useTasks } from '~/composables/useTasks'

describe('useTasks Composable', () => {
  it('calcule correctement le taux d\'achèvement', () => {
    const { completionRate, totalTasks, completedTasks } = useTasks()

    expect(totalTasks.value).toBeGreaterThan(0)
    expect(completionRate.value).toBe(
      Math.round((completedTasks.value.length / totalTasks.value) * 100)
    )
  })

  it('ajoute une tâche et met à jour les métriques', () => {
    const { addTask, tasks, totalTasks } = useTasks()
    const initialCount = totalTasks.value

    const created = addTask({
      title: 'Tester DevFlow',
      description: 'Écrire des tests unitaires',
      priority: 'high',
      status: 'todo',
      assignee: null,
      tags: ['Test'],
      estimatedHours: 2
    })

    expect(totalTasks.value).toBe(initialCount + 1)
    expect(tasks.value[0].id).toBe(created.id)
  })
})
```

### B. Tester un Composant UI (`TaskCard.spec.ts`)

```typescript
// tests/components/TaskCard.spec.ts
import { describe, it, expect } from 'vitest'
import { mount } from '@vue/test-utils'
import TaskCard from '~/components/tasks/TaskCard.vue'
import type { Task } from '~/types/devflow'

const mockTask: Task = {
  id: 'TASK-1',
  title: 'Tester le composant',
  description: 'Description de test',
  status: 'todo',
  priority: 'urgent',
  assignee: null,
  tags: ['Testing'],
  estimatedHours: 2,
  createdAt: new Date().toISOString()
}

describe('TaskCard.vue', () => {
  it('affiche le titre et le badge de priorité', () => {
    const wrapper = mount(TaskCard, {
      props: { task: mockTask }
    })

    expect(wrapper.find('.task-title').text()).toContain('Tester le composant')
    expect(wrapper.find('.priority-badge').text()).toBe('urgent')
  })

  it('émet un événement lors du changement de statut', async () => {
    const wrapper = mount(TaskCard, {
      props: { task: mockTask }
    })

    const select = wrapper.find('.select-status')
    await select.setValue('done')

    expect(wrapper.emitted('update-status')).toBeTruthy()
    expect(wrapper.emitted('update-status')![0]).toEqual(['TASK-1', 'done'])
  })
})
```

---

## 🛠️ 4. Travaux Pratiques

1. Écrire la suite de tests unitaires pour `useTasks.ts` et `useTaskFilters.ts`.
2. Écrire des tests de composants pour `TaskCard.vue`, `UserAvatar.vue` et `AppModal.vue`.
3. Lancer la couverture de code (`npm run test:coverage`) et viser un minimum de 80% de couverture sur la logique métier.
