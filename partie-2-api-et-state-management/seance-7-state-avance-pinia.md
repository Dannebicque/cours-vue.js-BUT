# Séance 7 : State Avancé avec Pinia

## 🎯 Objectifs de la séance

* Comprendre le rôle d'un store centralisé dans une architecture front-end professionnelle.
* Maîtriser **Pinia**, le gestionnaire d'état officiel de Vue 3 et Nuxt 4 (Setup Stores avec Composition API).
* Concevoir des stores typés avec TypeScript (`state`, `getters`, `actions`).
* Mettre en place la persistance automatique de l'état (localStorage / Cookies / `pinia-plugin-persistedstate`).
* **Fil rouge DevFlow** : Centraliser la gestion des tâches et de la session utilisateur dans des stores Pinia.

---

## 📖 1. Qu'est-ce qu'un Store et Pourquoi l'utiliser ?

Un **Store** est un conteneur centralisé pour l'état partagé d'une application. Plutôt que de faire transiter des données et des callbacks via des props à travers une arborescence profonde de composants (*prop drilling*), le store fournit une **source unique de vérité** (*Single Source of Truth*) accessible depuis n'importe quel composant ou composable.

```
                  ┌──────────────────────┐
                  │      Store Pinia     │
                  │ (tasks, user, token) │
                  └──────────┬───────────┘
                             │
         ┌───────────────────┼───────────────────┐
         ▼                   ▼                   ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│ TaskHeader.vue  │ │ TaskKanban.vue  │ │ UserMenu.vue    │
└─────────────────┘ └─────────────────┘ └─────────────────┘
```

### Pourquoi Pinia ?
* **Compatibilité native Vue 3 & Nuxt 4** : Utilise la Composition API (`ref`, `computed`).
* **Typage TypeScript irréprochable** : Inférence automatique des types sans code verbeux.
* **Architecture modulaire** : Chaque store est un fichier isolé (`useAuthStore`, `useTaskStore`), pas de store monolithique complexe comme l'ancien Vuex.
* **Support SSR natif** : Gestion propre de la sérialisation et de l'hydratation du state entre serveur et client.

---

## 🏗️ 2. Structure d'un Store Pinia en Setup Syntax

### Exemple : `app/stores/taskStore.ts`

```typescript
// app/stores/taskStore.ts
import { defineStore } from 'pinia'
import type { Task, TaskPriority, TaskStatus } from '~/types/devflow'

export const useTaskStore = defineStore('tasks', () => {
  // 1. State (ref)
  const tasks = ref<Task[]>([])
  const isLoading = ref(false)
  const selectedTaskId = ref<string | null>(null)

  // 2. Getters (computed)
  const totalTasks = computed(() => tasks.value.length)
  const completedTasks = computed(() => tasks.value.filter(t => t.status === 'done'))
  const selectedTask = computed(() => tasks.value.find(t => t.id === selectedTaskId.value) ?? null)

  const tasksByStatus = computed(() => (status: TaskStatus) => {
    return tasks.value.filter(t => t.status === status)
  })

  // 3. Actions (fonctions)
  function setTasks(initialTasks: Task[]) {
    tasks.value = initialTasks
  }

  function addTask(newTask: Task) {
    tasks.value.unshift(newTask)
  }

  function updateStatus(taskId: string, newStatus: TaskStatus) {
    const task = tasks.value.find(t => t.id === taskId)
    if (task) {
      task.status = newStatus
      task.completedAt = newStatus === 'done' ? new Date().toISOString() : undefined
    }
  }

  function deleteTask(taskId: string) {
    tasks.value = tasks.value.filter(t => t.id !== taskId)
  }

  return {
    tasks,
    isLoading,
    selectedTaskId,
    totalTasks,
    completedTasks,
    selectedTask,
    tasksByStatus,
    setTasks,
    addTask,
    updateStatus,
    deleteTask
  }
})
```

---

## 💾 3. Persistance de l'État

Pour conserver les données d'un store lors du rechargement de page (ex: session utilisateur, préférences d'affichage), on utilise le module Nuxt `@pinia-plugin-persistedstate/nuxt`.

```typescript
// app/stores/authStore.ts
import { defineStore } from 'pinia'
import type { User } from '~/types/devflow'

export const useAuthStore = defineStore('auth', () => {
  const user = ref<User | null>(null)
  const token = ref<string | null>(null)

  const isAuthenticated = computed(() => !!token.value)

  function login(userData: User, authToken: string) {
    user.value = userData
    token.value = authToken
  }

  function logout() {
    user.value = null
    token.value = null
  }

  return {
    user,
    token,
    isAuthenticated,
    login,
    logout
  }
}, {
  // Active la persistance automatique dans les cookies / localStorage
  persist: true
})
```

---

## 🛠️ 4. Travaux Pratiques

1. Installer le module Pinia pour Nuxt 4 (`@pinia/nuxt`).
2. Créer le store `useTaskStore.ts` et y migrer la gestion de la liste des tâches du projet DevFlow.
3. Créer le store `useAuthStore.ts` pour gérer l'utilisateur connecté et simuler le flux de connexion/déconnexion.
4. Raccorder les composants `TaskCard.vue`, `TaskColumn.vue` et `NavigationMenu.vue` aux stores Pinia.
