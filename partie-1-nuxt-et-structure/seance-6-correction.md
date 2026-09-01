# Séance 6 (correction) : Refactoring SOLID de DevFlow

Ce document présente la correction détaillée de l'atelier pratique de refactoring de la **Séance 6**.

---

## 🛠️ 1. Refactoring SOLID du composant `TaskCard`

Voici la version refactorisée, typée et respectant les principes SOLID :

```vue
<!-- components/tasks/TaskCardSolid.vue -->
<script setup lang="ts">
import type { Task, TaskPriority, TaskStatus } from '~/types/devflow'

// 1. Interface Segregation (ISP) & Typage strict
interface Props {
  task: Task
  isHighlighted?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  isHighlighted: false
})

// 2. Événements typés sans mutation directe (One-Way Data Flow)
const emit = defineEmits<{
  (e: 'update-status', taskId: string, newStatus: TaskStatus): void
  (e: 'delete', taskId: string): void
}>()

// 3. Single Responsibility (SRP) : formatage extrait dans un computed ou utilitaire
const formattedDate = computed(() => {
  return new Intl.DateTimeFormat('fr-FR', {
    day: 'numeric',
    month: 'short',
    year: 'numeric'
  }).format(new Date(props.task.createdAt))
})

const handleStatusChange = (event: Event) => {
  const select = event.target as HTMLSelectElement
  emit('update-status', props.task.id, select.value as TaskStatus)
}
</script>

<template>
  <article :class="['solid-task-card', { highlighted: props.isHighlighted }]">
    <!-- En-tête de carte avec SRP : badge délégué au sous-composant -->
    <header class="card-header">
      <span class="task-id">{{ task.id }}</span>
      <TaskPriorityBadge :priority="task.priority" />
    </header>

    <!-- Corps de la carte -->
    <div class="card-body">
      <h3 class="task-title">{{ task.title }}</h3>
      <p class="task-desc">{{ task.description }}</p>
      <time class="task-date" :datetime="task.createdAt">
        📅 {{ formattedDate }}
      </time>
    </div>

    <!-- Pied de carte avec délégation avatar (ISP) -->
    <footer class="card-footer">
      <UserAvatar :user="task.assignee" size="sm" />

      <!-- Open/Closed Principle (OCP) : Slot pour actions personnalisables -->
      <div class="card-actions">
        <slot name="actions" :task="task">
          <!-- Action par défaut -->
          <select :value="task.status" @change="handleStatusChange" class="select-status">
            <option value="todo">À faire</option>
            <option value="in_progress">En cours</option>
            <option value="in_review">En revue</option>
            <option value="done">Terminé</option>
          </select>
          <button @click="emit('delete', task.id)" class="btn-delete" title="Supprimer">
            🗑️
          </button>
        </slot>
      </div>
    </footer>
  </article>
</template>

<style scoped>
.solid-task-card {
  background: #1e293b;
  border: 1px solid #334155;
  border-radius: 12px;
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 12px;
  transition: border-color 0.2s, transform 0.2s;
}
.solid-task-card.highlighted {
  border-color: #38bdf8;
  box-shadow: 0 0 15px rgba(56, 189, 248, 0.2);
}
.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.task-id {
  font-family: monospace;
  font-size: 0.8rem;
  color: #94a3b8;
}
.task-title { font-size: 1rem; color: #f8fafc; margin-bottom: 4px; }
.task-desc { font-size: 0.85rem; color: #94a3b8; line-height: 1.4; }
.task-date { font-size: 0.75rem; color: #64748b; margin-top: 6px; display: block; }
.card-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: auto;
  padding-top: 10px;
  border-top: 1px solid #334155;
}
.card-actions { display: flex; align-items: center; gap: 8px; }
.select-status {
  background: #0f172a;
  border: 1px solid #334155;
  color: #f8fafc;
  font-size: 0.75rem;
  padding: 4px 6px;
  border-radius: 6px;
}
.btn-delete { background: transparent; border: none; cursor: pointer; }
</style>
```

---

## 🛠️ 2. Mise en place de l'Adapter de Stockage (LSP & DIP)

```typescript
// types/storage.ts
import type { Task } from '~/types/devflow'

/**
 * Contrat d'interface pour le stockage (LSP)
 */
export interface TaskStorageAdapter {
  loadTasks(): Promise<Task[]>
  saveTasks(tasks: Task[]): Promise<void>
}

/**
 * Implémentation LocalStorage
 */
export class LocalStorageTaskAdapter implements TaskStorageAdapter {
  private readonly storageKey = 'devflow_tasks_v1'

  async loadTasks(): Promise<Task[]> {
    if (import.meta.server) return []
    const raw = localStorage.getItem(this.storageKey)
    return raw ? JSON.parse(raw) : []
  }

  async saveTasks(tasks: Task[]): Promise<void> {
    if (import.meta.server) return
    localStorage.setItem(this.storageKey, JSON.stringify(tasks))
  }
}
```
