---
hidden: true
---

# Séance 5 (correction) : Dashboard Kanban & Modals

Ce document présente la correction détaillée de l'atelier pratique de la **Séance 5**.

***

## 🛠️ 1. Composant Générique `app/components/common/AppModal.vue`

```vue
<!-- app/components/common/AppModal.vue -->
<script setup lang="ts">
const isOpen = defineModel<boolean>('isOpen', { default: false })

interface Props {
  title: string
}

defineProps<Props>()

const closeModal = () => {
  isOpen.value = false
}
</script>

<template>
  <Teleport to="body">
    <Transition name="modal-fade">
      <div v-if="isOpen" class="modal-backdrop" @click.self="closeModal">
        <div class="modal-card" role="dialog" aria-modal="true">
          <header class="modal-header">
            <h3>{{ title }}</h3>
            <button @click="closeModal" class="btn-close" aria-label="Fermer">✕</button>
          </header>

          <div class="modal-body">
            <slot />
          </div>

          <footer v-if="$slots.footer" class="modal-footer">
            <slot name="footer" />
          </footer>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<style scoped>
.modal-backdrop {
  position: fixed;
  inset: 0;
  background: rgba(15, 23, 42, 0.75);
  backdrop-filter: blur(4px);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 50;
  padding: 16px;
}
.modal-card {
  background: #1e293b;
  border: 1px solid #334155;
  border-radius: 16px;
  width: 100%;
  max-width: 520px;
  box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
  display: flex;
  flex-direction: column;
  overflow: hidden;
}
.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px 24px;
  border-bottom: 1px solid #334155;
}
.btn-close {
  background: transparent;
  border: none;
  color: #94a3b8;
  font-size: 1.25rem;
  cursor: pointer;
}
.modal-body { padding: 24px; }
.modal-footer {
  padding: 16px 24px;
  border-top: 1px solid #334155;
  display: flex;
  justify-content: flex-end;
  gap: 12px;
}

.modal-fade-enter-active, .modal-fade-leave-active {
  transition: opacity 0.25s ease, transform 0.25s ease;
}
.modal-fade-enter-from, .modal-fade-leave-to {
  opacity: 0;
  transform: scale(0.95);
}
</style>
```

***

## 🛠️ 2. Composant `app/components/tasks/TaskCard.vue`

```vue
<!-- app/components/tasks/TaskCard.vue -->
<script setup lang="ts">
import type { Task, TaskStatus } from '~/types/devflow'

interface Props {
  task: Task
}

const props = defineProps<Props>()

const emit = defineEmits<{
  (e: 'update-status', taskId: string, newStatus: TaskStatus): void
  (e: 'delete', taskId: string): void
}>()

const statusOptions: { value: TaskStatus; label: string }[] = [
  { value: 'todo', label: 'À faire' },
  { value: 'in_progress', label: 'En cours' },
  { value: 'in_review', label: 'En revue' },
  { value: 'done', label: 'Terminé' }
]
</script>

<template>
  <article class="task-card">
    <header class="card-top">
      <span class="task-id">{{ task.id }}</span>
      <span :class="['priority-badge', `priority-${task.priority}`]">
        {{ task.priority }}
      </span>
    </header>

    <h4 class="task-title">
      <NuxtLink :to="`/tasks/${task.id}`" class="task-link">
        {{ task.title }}
      </NuxtLink>
    </h4>

    <p class="task-description">{{ task.description }}</p>

    <div class="tags-row" v-if="task.tags.length > 0">
      <span v-for="tag in task.tags" :key="tag" class="tag">
        #{{ tag }}
      </span>
    </div>

    <footer class="card-bottom">
      <UserAvatar :user="task.assignee" size="sm" />

      <div class="actions">
        <select
          :value="task.status"
          @change="emit('update-status', task.id, ($event.target as HTMLSelectElement).value as TaskStatus)"
          class="select-status"
        >
          <option
            v-for="opt in statusOptions"
            :key="opt.value"
            :value="opt.value"
          >
            {{ opt.label }}
          </option>
        </select>

        <button
          @click="emit('delete', task.id)"
          class="btn-icon-danger"
          title="Supprimer la tâche"
        >
          🗑️
        </button>
      </div>
    </footer>
  </article>
</template>

<style scoped>
.task-card {
  background: #1e293b;
  border: 1px solid #334155;
  border-radius: 12px;
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 12px;
  transition: transform 0.2s, box-shadow 0.2s;
}
.task-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.3);
  border-color: #475569;
}
.card-top {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.task-id { font-family: monospace; font-size: 0.75rem; color: #94a3b8; }
.priority-badge {
  font-size: 0.7rem;
  font-weight: 700;
  text-transform: uppercase;
  padding: 2px 8px;
  border-radius: 9999px;
}
.priority-urgent { background: #fee2e2; color: #991b1b; }
.priority-high { background: #ffedd5; color: #9a3412; }
.priority-medium { background: #fef9c3; color: #854d0e; }
.priority-low { background: #dcfce7; color: #166534; }

.task-title { font-size: 1rem; line-height: 1.4; }
.task-link { color: #f8fafc; text-decoration: none; }
.task-link:hover { color: #38bdf8; }

.task-description {
  font-size: 0.85rem;
  color: #94a3b8;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
.tags-row { display: flex; flex-wrap: wrap; gap: 6px; }
.tag { font-size: 0.75rem; color: #38bdf8; background: #0f172a; padding: 2px 6px; border-radius: 4px; }

.card-bottom {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: auto;
  padding-top: 8px;
  border-top: 1px solid #334155;
}
.select-status {
  background: #0f172a;
  color: #f8fafc;
  border: 1px solid #334155;
  border-radius: 6px;
  font-size: 0.75rem;
  padding: 4px 6px;
}
.btn-icon-danger {
  background: transparent;
  border: none;
  cursor: pointer;
  padding: 4px;
}
</style>
```

***

## 🛠️ 3. Composant `app/components/tasks/TaskColumn.vue`

```vue
<!-- app/components/tasks/TaskColumn.vue -->
<script setup lang="ts">
import type { Task, TaskStatus } from '~/types/devflow'

interface Props {
  status: TaskStatus
  title: string
  tasks: Task[]
}

const props = defineProps<Props>()

const emit = defineEmits<{
  (e: 'update-status', taskId: string, newStatus: TaskStatus): void
  (e: 'delete', taskId: string): void
}>()
</script>

<template>
  <section class="kanban-column">
    <header class="column-header">
      <div class="column-title-group">
        <h3>{{ title }}</h3>
        <span class="count-pill">{{ tasks.length }}</span>
      </div>
    </header>

    <div class="column-content">
      <TransitionGroup name="task-list" tag="div" class="cards-list">
        <TaskCard
          v-for="task in tasks"
          :key="task.id"
          :task="task"
          @update-status="(id, s) => emit('update-status', id, s)"
          @delete="(id) => emit('delete', id)"
        />
      </TransitionGroup>

      <div v-if="tasks.length === 0" class="empty-column">
        Aucune tâche dans cette colonne
      </div>
    </div>
  </section>
</template>

<style scoped>
.kanban-column {
  background: #0f172a;
  border: 1px solid #1e293b;
  border-radius: 16px;
  display: flex;
  flex-direction: column;
  min-height: 500px;
}
.column-header {
  padding: 16px;
  border-bottom: 1px solid #1e293b;
}
.column-title-group {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.count-pill {
  background: #1e293b;
  color: #38bdf8;
  font-size: 0.8rem;
  font-weight: 700;
  padding: 2px 10px;
  border-radius: 9999px;
}
.column-content { padding: 16px; flex: 1; }
.cards-list { display: flex; flex-direction: column; gap: 12px; }
.empty-column {
  border: 2px dashed #1e293b;
  border-radius: 12px;
  padding: 32px 16px;
  text-align: center;
  color: #64748b;
  font-size: 0.875rem;
}

.task-list-move,
.task-list-enter-active,
.task-list-leave-active {
  transition: all 0.3s ease;
}
.task-list-enter-from,
.task-list-leave-to {
  opacity: 0;
  transform: translateY(15px);
}
</style>
```

***

## 🛠️ 4. Assemblage Final `app/pages/tasks/index.vue`

```vue
<!-- app/pages/tasks/index.vue -->
<script setup lang="ts">
import type { TaskPriority, TaskStatus } from '~/types/devflow'

const {
  tasks,
  totalTasks,
  completedTasks,
  urgentTasks,
  completionRate,
  addTask,
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

const isModalOpen = ref(false)
const newTaskTitle = ref('')
const newTaskDesc = ref('')
const newTaskPriority = ref<TaskPriority>('medium')

const handleCreateTask = () => {
  if (!newTaskTitle.value.trim()) return

  addTask({
    title: newTaskTitle.value,
    description: newTaskDesc.value,
    status: 'todo',
    priority: newTaskPriority.value,
    assignee: null,
    tags: ['DevFlow'],
    estimatedHours: 2
  })

  newTaskTitle.value = ''
  newTaskDesc.value = ''
  newTaskPriority.value = 'medium'
  isModalOpen.value = false
}

const todoTasks = computed(() => filteredTasks.value.filter(t => t.status === 'todo'))
const inProgressTasks = computed(() => filteredTasks.value.filter(t => t.status === 'in_progress'))
const inReviewTasks = computed(() => filteredTasks.value.filter(t => t.status === 'in_review'))
const doneTasks = computed(() => filteredTasks.value.filter(t => t.status === 'done'))
</script>

<template>
  <div class="dashboard-page">
    <div class="dashboard-header">
      <div>
        <h2>Tableau de bord DevFlow</h2>
        <p class="subtitle">Suivi d'avancement des développements de l'agence DevSquad</p>
      </div>

      <button @click="isModalOpen = true" class="btn-primary">
        + Nouvelle Tâche
      </button>
    </div>

    <!-- Grille Kanban -->
    <main class="kanban-board">
      <TaskColumn
        status="todo"
        title="📌 À Faire"
        :tasks="todoTasks"
        @update-status="updateTaskStatus"
        @delete="deleteTask"
      />
      <TaskColumn
        status="in_progress"
        title="⚡ En Cours"
        :tasks="inProgressTasks"
        @update-status="updateTaskStatus"
        @delete="deleteTask"
      />
      <TaskColumn
        status="in_review"
        title="👀 En Revue"
        :tasks="inReviewTasks"
        @update-status="updateTaskStatus"
        @delete="deleteTask"
      />
      <TaskColumn
        status="done"
        title="✅ Terminé"
        :tasks="doneTasks"
        @update-status="updateTaskStatus"
        @delete="deleteTask"
      />
    </main>

    <!-- Modal de création avec defineModel -->
    <AppModal v-model:isOpen="isModalOpen" title="Créer une nouvelle tâche">
      <form @submit.prevent="handleCreateTask" class="task-form">
        <div class="form-group">
          <label>Titre de la tâche *</label>
          <input
            v-model="newTaskTitle"
            type="text"
            required
            placeholder="Ex: Implémenter le responsive mobile"
            class="form-input"
          />
        </div>

        <div class="form-group">
          <label>Description</label>
          <textarea
            v-model="newTaskDesc"
            rows="3"
            placeholder="Précisez les critères d'acceptation..."
            class="form-textarea"
          />
        </div>

        <div class="form-group">
          <label>Priorité</label>
          <select v-model="newTaskPriority" class="form-select">
            <option value="low">Basse</option>
            <option value="medium">Moyenne</option>
            <option value="high">Haute</option>
            <option value="urgent">Urgente</option>
          </select>
        </div>

        <div class="form-actions">
          <button type="button" @click="isModalOpen = false" class="btn-secondary">
            Annuler
          </button>
          <button type="submit" class="btn-primary">
            Créer la tâche
          </button>
        </div>
      </form>
    </AppModal>
  </div>
</template>

<style scoped>
.dashboard-page { display: flex; flex-direction: column; gap: 24px; }
.dashboard-header { display: flex; justify-content: space-between; align-items: center; }
.subtitle { color: #94a3b8; font-size: 0.9rem; }
.btn-primary {
  background: #38bdf8;
  color: #0f172a;
  border: none;
  font-weight: 700;
  padding: 10px 20px;
  border-radius: 8px;
  cursor: pointer;
}
.btn-secondary {
  background: #334155;
  color: #f8fafc;
  border: none;
  padding: 10px 16px;
  border-radius: 8px;
  cursor: pointer;
}
.kanban-board {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 20px;
}
.task-form { display: flex; flex-direction: column; gap: 16px; }
.form-group { display: flex; flex-direction: column; gap: 6px; }
.form-group label { font-size: 0.85rem; color: #94a3b8; }
.form-input, .form-textarea, .form-select {
  background: #0f172a;
  border: 1px solid #334155;
  border-radius: 8px;
  color: #f8fafc;
  padding: 10px;
}
.form-actions { display: flex; justify-content: flex-end; gap: 12px; margin-top: 12px; }
</style>
```
