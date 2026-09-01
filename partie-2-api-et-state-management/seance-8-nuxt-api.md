# Séance 8 : Data Fetching avec `useFetch` & `useAsyncData`

## 🎯 Objectifs de la séance

* Maîtriser les utilitaires de chargement de données de Nuxt 4 : **`useFetch`**, **`useAsyncData`** et **`$fetch`**.
* Comprendre le cycle d'exécution du Data Fetching en SSR (serveur puis réhydratation côté client sans double requête).
* Gérer les états réactifs : `pending`, `error`, `data`, `refresh` et `clear`.
* Savoir quand utiliser `lazy: true`, les clés de cache uniques et les fonctions de transformation (`transform`).
* Consommer une API REST externe avec typage strict.

---

## 🌐 1. Les Utilitaires de Fetching dans Nuxt 4

Nuxt 4 fournit des helpers auto-importés spécialement conçus pour le rendu hybride et SSR.

```
┌────────────────────────────────────────────────────────────┐
│                    useFetch('/api/tasks')                  │
├────────────────────────────────────────────────────────────┤
│  1. Exécution côté Serveur (SSR) :                         │
│     - Appel API exécuté côté Node/Nitro                    │
│     - Données injectées directement dans le payload HTML   │
│                                                            │
│  2. Réhydratation côté Client :                            │
│     - Récupération du payload JSON sérialisé               │
│     - ZÉRO double requête HTTP inutile au chargement !     │
└────────────────────────────────────────────────────────────┘
```

### A. `useFetch` : Le Wrapper déclaratif standard

```vue
<script setup lang="ts">
import type { Task } from '~/types/devflow'

// Appel typé avec gestion automatique de la réactivité
const { data: tasks, pending, error, refresh } = await useFetch<Task[]>('/api/tasks', {
  // Options courantes
  lazy: false, // Bloque la navigation jusqu'à réception des données (SSR)
  headers: {
    'Accept': 'application/json'
  }
})
</script>

<template>
  <div>
    <div v-if="pending" class="loader">Chargement des tâches...</div>
    <div v-else-if="error" class="error-box">Erreur : {{ error.message }}</div>
    <div v-else>
      <TaskCard v-for="task in tasks" :key="task.id" :task="task" />
    </div>
    <button @click="() => refresh()">Actualiser</button>
  </div>
</template>
```

---

### B. `useAsyncData` : Pour les flux asynchrones complexes

`useAsyncData` est indispensable lorsque vous devez combiner plusieurs appels ou appeler un SDK tiers tout en contrôlant la clé de cache Nuxt.

```typescript
const { data: dashboardData } = await useAsyncData('dashboard-metrics', async () => {
  const [tasks, users] = await Promise.all([
    $fetch<Task[]>('/api/tasks'),
    $fetch<User[]>('/api/users')
  ])
  return { tasks, users, loadedAt: Date.now() }
}, {
  // Sélectionner ou transformer uniquement une partie des données reçues
  transform: (result) => ({
    taskCount: result.tasks.length,
    users: result.users
  })
})
```

---

### C. `$fetch` : Pour les actions utilisateur (POST, PUT, DELETE)

`$fetch` est l'utilitaire HTTP sous-jacent (basé sur ofetch). Il s'utilise directement dans les fonctions d'action (soumission de formulaire, suppression au clic) où la persistance SSR n'est pas nécessaire.

```typescript
const handleCreateTask = async (taskPayload: Partial<Task>) => {
  try {
    const createdTask = await $fetch<Task>('/api/tasks', {
      method: 'POST',
      body: taskPayload
    })
    console.log('Tâche créée :', createdTask)
  } catch (err) {
    console.error('Échec de la création', err)
  }
}
```

---

## 🛠️ 2. Travaux Pratiques

1. Remplacer les données mockées de `useTasks` par des appels `useFetch` vers une API distante (ou mockée).
2. Gérer proprement les états de chargement (`pending`) et les erreurs d'API dans l'interface utilisateur.
3. Implémenter l'ajout et la suppression de tâches avec `$fetch` (`POST`, `DELETE`).
