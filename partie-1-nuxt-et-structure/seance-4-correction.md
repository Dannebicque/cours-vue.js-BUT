# Séance 4 (correction) : useTasks & useTaskFilters

Ce document présente la correction détaillée de l'atelier pratique de la **Séance 4**.

***

## 🛠️ 1. Composable Métier `app/composables/useTasks.ts`

```typescript
// app/composables/useTasks.ts
import type { Task, TaskPriority, TaskStatus } from '~/types/devflow'

// Données initiales simulées (Mock pour la Partie 1)
const INITIAL_TASKS: Task[] = [
  {
    id: 'TASK-101',
    title: 'Configurer l\'architecture Nuxt 4 & TypeScript',
    description: 'Mettre en place le dossier app/, les types stricts et le layout default.',
    status: 'done',
    priority: 'high',
    assignee: {
      id: 'USR-1',
      name: 'Alexandre Dubois',
      email: 'alex@devsquad.pro',
      avatar: '',
      role: 'lead'
    },
    tags: ['Architecture', 'Setup'],
    estimatedHours: 4,
    createdAt: '2026-09-01T08:00:00Z',
    completedAt: '2026-09-01T12:00:00Z'
  },
  {
    id: 'TASK-102',
    title: 'Développer le système de filtrage réactif',
    description: 'Permettre le tri par priorité, assigné et statut avec recherche textuelle instantanée.',
    status: 'in_progress',
    priority: 'urgent',
    assignee: {
      id: 'USR-2',
      name: 'Sarah Connor',
      email: 'sarah@devsquad.pro',
      avatar: '',
      role: 'frontend'
    },
    tags: ['Frontend', 'Reactivité'],
    estimatedHours: 6,
    createdAt: '2026-09-01T09:30:00Z'
  },
  {
    id: 'TASK-103',
    title: 'Rédiger les spécifications des endpoints Nitro',
    description: 'Définir les contrats d\'API /api/tasks pour la phase 2 du projet.',
    status: 'todo',
    priority: 'medium',
    assignee: null,
    tags: ['Backend', 'Nitro'],
    estimatedHours: 3,
    createdAt: '2026-09-01T10:00:00Z'
  },
  {
    id: 'TASK-104',
    title: 'Audit d\'accessibilité et conformité WCAG',
    description: 'Vérifier les contrastes, la navigation au clavier et les balises ARIA du dashboard.',
    status: 'todo',
    priority: 'low',
    assignee: {
      id: 'USR-2',
      name: 'Sarah Connor',
      email: 'sarah@devsquad.pro',
      avatar: '',
      role: 'frontend'
    },
    tags: ['A11y', 'Design'],
    estimatedHours: 2,
    createdAt: '2026-09-01T11:00:00Z'
  }
]

export function useTasks() {
  const tasks = ref<Task[]>(INITIAL_TASKS)
  const isLoading = ref<boolean>(false)

  // Indicateurs calculés
  const totalTasks = computed(() => tasks.value.length)
  const completedTasks = computed(() => tasks.value.filter(t => t.status === 'done'))
  const pendingTasks = computed(() => tasks.value.filter(t => t.status !== 'done'))
  const urgentTasks = computed(() => tasks.value.filter(t => t.priority === 'urgent' && t.status !== 'done'))

  const completionRate = computed(() => {
    if (totalTasks.value === 0) return 0
    return Math.round((completedTasks.value.length / totalTasks.value) * 100)
  })

  const totalEstimatedHours = computed(() => {
    return tasks.value.reduce((acc, t) => acc + t.estimatedHours, 0)
  })

  // Actions
  const getTaskById = (id: string): Task | undefined => {
    return tasks.value.find(t => t.id === id)
  }

  const addTask = (taskData: Omit<Task, 'id' | 'createdAt'>): Task => {
    const newTask: Task = {
      ...taskData,
      id: `TASK-${Math.floor(100 + Math.random() * 900)}`,
      createdAt: new Date().toISOString()
    }
    tasks.value.unshift(newTask)
    return newTask
  }

  const updateTaskStatus = (id: string, newStatus: TaskStatus) => {
    const task = tasks.value.find(t => t.id === id)
    if (task) {
      task.status = newStatus
      task.completedAt = newStatus === 'done' ? new Date().toISOString() : undefined
    }
  }

  const deleteTask = (id: string) => {
    tasks.value = tasks.value.filter(t => t.id !== id)
  }

  return {
    tasks: readonly(tasks),
    isLoading: readonly(isLoading),
    totalTasks,
    completedTasks,
    pendingTasks,
    urgentTasks,
    completionRate,
    totalEstimatedHours,
    getTaskById,
    addTask,
    updateTaskStatus,
    deleteTask
  }
}
```

***

## 🛠️ 2. Composable de Filtrage `app/composables/useTaskFilters.ts`

```typescript
// app/composables/useTaskFilters.ts
import type { Task, TaskPriority, TaskStatus } from '~/types/devflow'

export function useTaskFilters(tasks: Ref<readonly Task[] | Task[]>) {
  const searchQuery = ref('')
  const selectedStatus = ref<TaskStatus | 'all'>('all')
  const selectedPriority = ref<TaskPriority | 'all'>('all')
  const selectedAssigneeId = ref<string | 'all'>('all')

  const filteredTasks = computed(() => {
    return tasks.value.filter((task) => {
      const query = searchQuery.value.trim().toLowerCase()
      const matchesQuery = !query ||
        task.title.toLowerCase().includes(query) ||
        task.description.toLowerCase().includes(query) ||
        task.tags.some(tag => tag.toLowerCase().includes(query))

      const matchesStatus = selectedStatus.value === 'all' || task.status === selectedStatus.value
      const matchesPriority = selectedPriority.value === 'all' || task.priority === selectedPriority.value
      const matchesAssignee = selectedAssigneeId.value === 'all' || task.assignee?.id === selectedAssigneeId.value

      return matchesQuery && matchesStatus && matchesPriority && matchesAssignee
    })
  })

  const resetFilters = () => {
    searchQuery.value = ''
    selectedStatus.value = 'all'
    selectedPriority.value = 'all'
    selectedAssigneeId.value = 'all'
  }

  return {
    searchQuery,
    selectedStatus,
    selectedPriority,
    selectedAssigneeId,
    filteredTasks,
    resetFilters
  }
}
```

***

## 🛠️ 3. Intégration dans `app/pages/tasks/index.vue`

```vue
<!-- app/pages/tasks/index.vue -->
<script setup lang="ts">
const {
  tasks,
  totalTasks,
  completedTasks,
  urgentTasks,
  completionRate,
  updateTaskStatus,
  deleteTask
} = useTasks()

const {
  searchQuery,
  selectedStatus,
  selectedPriority,
  filteredTasks,
  resetFilters
} = useTaskFilters(tasks)
</script>

<template>
  <div class="tasks-page">
    <header class="stats-overview">
      <div class="stat-card">
        <span class="stat-label">Total des tâches</span>
        <strong class="stat-value">{{ totalTasks }}</strong>
      </div>
      <div class="stat-card">
        <span class="stat-label">Terminées</span>
        <strong class="stat-value text-success">{{ completedTasks.length }}</strong>
      </div>
      <div class="stat-card">
        <span class="stat-label">Urgentes</span>
        <strong class="stat-value text-danger">{{ urgentTasks.length }}</strong>
      </div>
      <div class="stat-card">
        <span class="stat-label">Taux d'achèvement</span>
        <strong class="stat-value text-brand">{{ completionRate }}%</strong>
      </div>
    </header>

    <section class="filters-bar">
      <input
        v-model="searchQuery"
        type="text"
        placeholder="🔍 Rechercher une tâche ou un tag..."
        class="input-search"
      >

      <select v-model="selectedStatus" class="select-filter">
        <option value="all">Tous les statuts</option>
        <option value="todo">À faire</option>
        <option value="in_progress">En cours</option>
        <option value="in_review">En revue</option>
        <option value="done">Terminé</option>
      </select>

      <select v-model="selectedPriority" class="select-filter">
        <option value="all">Toutes les priorités</option>
        <option value="low">Basse</option>
        <option value="medium">Moyenne</option>
        <option value="high">Haute</option>
        <option value="urgent">Urgente</option>
      </select>

      <button @click="resetFilters" class="btn-reset">
        Réinitialiser
      </button>
    </section>

    <section class="tasks-results">
      <p class="results-count">
        {{ filteredTasks.length }} tâche(s) trouvée(s)
      </p>

      <div v-if="filteredTasks.length === 0" class="empty-state">
        Aucune tâche ne correspond aux critères de recherche.
      </div>

      <div v-else class="tasks-grid">
        <article
          v-for="task in filteredTasks"
          :key="task.id"
          class="simple-task-card"
        >
          <div class="card-header">
            <span class="task-id">{{ task.id }}</span>
            <span :class="['priority-tag', `p-${task.priority}`]">
              {{ task.priority }}
            </span>
          </div>
          <h3>{{ task.title }}</h3>
          <p class="task-desc">{{ task.description }}</p>
          <div class="card-footer">
            <span class="status-badge">{{ task.status }}</span>
            <button
              v-if="task.status !== 'done'"
              @click="updateTaskStatus(task.id, 'done')"
              class="btn-action"
            >
              ✓ Terminer
            </button>
            <button
              @click="deleteTask(task.id)"
              class="btn-delete"
            >
              🗑️
            </button>
          </div>
        </article>
      </div>
    </section>
  </div>
</template>

<style scoped>
.tasks-page { display: flex; flex-direction: column; gap: 24px; }
.stats-overview {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
  gap: 16px;
}
.stat-card {
  background: #1e293b;
  border: 1px solid #334155;
  border-radius: 12px;
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}
.stat-label { font-size: 0.875rem; color: #94a3b8; }
.stat-value { font-size: 1.75rem; font-weight: 700; color: #f8fafc; }
.text-success { color: #4ade80; }
.text-danger { color: #f87171; }
.text-brand { color: #38bdf8; }

.filters-bar {
  display: flex;
  gap: 12px;
  flex-wrap: wrap;
  background: #1e293b;
  padding: 16px;
  border-radius: 12px;
  border: 1px solid #334155;
}
.input-search {
  flex: 1;
  min-width: 240px;
  background: #0f172a;
  border: 1px solid #334155;
  color: #f8fafc;
  padding: 10px 14px;
  border-radius: 8px;
}
.select-filter {
  background: #0f172a;
  border: 1px solid #334155;
  color: #f8fafc;
  padding: 10px 14px;
  border-radius: 8px;
}
.btn-reset {
  background: #334155;
  color: #f8fafc;
  border: none;
  padding: 10px 16px;
  border-radius: 8px;
  cursor: pointer;
}
.tasks-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 16px;
}
.simple-task-card {
  background: #1e293b;
  border: 1px solid #334155;
  border-radius: 12px;
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 12px;
}
.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.task-id { font-family: monospace; color: #94a3b8; font-size: 0.85rem; }
.card-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: auto;
}
.btn-action {
  background: #22c55e;
  color: white;
  border: none;
  padding: 6px 12px;
  border-radius: 6px;
  cursor: pointer;
}
.btn-delete {
  background: transparent;
  border: none;
  cursor: pointer;
}
</style>
```
