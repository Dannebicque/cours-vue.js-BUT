# Séance 1 (correction) : Types et Avatar DevFlow

Ce document présente la correction détaillée de l'atelier pratique de la **Séance 1**.

***

## 🛠️ Exercice 1 : Modélisation des types métier (`types/devflow.ts`)

Voici le fichier complet `types/devflow.ts` (ou `app/types/devflow.ts` sous Nuxt 4) :

```typescript
// types/devflow.ts

/**
 * Niveaux de priorité possibles pour une tâche
 */
export type TaskPriority = 'low' | 'medium' | 'high' | 'urgent'

/**
 * Statuts d'avancement dans le cycle de vie d'une tâche
 */
export type TaskStatus = 'todo' | 'in_progress' | 'in_review' | 'done'

/**
 * Rôles au sein de l'agence DevSquad
 */
export type UserRole = 'lead' | 'frontend' | 'backend' | 'qa'

/**
 * Modèle de données d'un membre de l'équipe
 */
export interface User {
  id: string
  name: string
  email: string
  avatar: string
  role: UserRole
}

/**
 * Modèle de données complet d'une tâche DevFlow
 */
export interface Task {
  id: string
  title: string
  description: string
  status: TaskStatus
  priority: TaskPriority
  assignee: User | null
  tags: string[]
  estimatedHours: number
  createdAt: string
  completedAt?: string
}

/**
 * Options de filtrage pour le tableau de bord
 */
export interface TaskFilterOptions {
  searchQuery: string
  statusFilter: TaskStatus | 'all'
  priorityFilter: TaskPriority | 'all'
  assigneeId: string | 'all'
}
```

### 💡 Points clés d'architecture :

* **Unions de chaînes plutôt qu'Enums numériques** : `type TaskPriority = 'low' | 'medium' | ...` est plus lisible dans les templates, dans le JSON des APIs et dans le débogueur que des enums numériques `TaskPriority.LOW = 0`.
* **Assignee nullable (`User | null`)** : Une tâche peut être non assignée lors de sa création. L'utilisation explicite de `null` oblige le développeur à gérer ce cas lors de l'affichage.

***

## 🛠️ Exercice 2 : Composant typé `UserAvatar.vue`

Voici le composant complet `components/UserAvatar.vue` :

```vue
<!-- components/UserAvatar.vue -->
<script setup lang="ts">
import type { User } from '~/types/devflow'

interface Props {
  // Utilisation de Pick pour n'exiger que les champs nécessaires à l'affichage (ISP)
  user: Pick<User, 'name' | 'avatar' | 'role'> | null
  size?: 'sm' | 'md' | 'lg'
}

// Définition des valeurs par défaut avec withDefaults
const props = withDefaults(defineProps<Props>(), {
  size: 'md'
})

// Calcul des initiales si pas d'avatar image
const initials = computed(() => {
  if (!props.user) return '?'
  return props.user.name
    .split(' ')
    .map(part => part[0])
    .join('')
    .toUpperCase()
    .slice(0, 2)
})

// Dictionnaire de classes pour les tailles
const sizeClasses: Record<'sm' | 'md' | 'lg', string> = {
  sm: 'avatar-sm',
  md: 'avatar-md',
  lg: 'avatar-lg'
}
</script>

<template>
  <div
    :class="['avatar', sizeClasses[props.size]]"
    :title="props.user ? `${props.user.name} (${props.user.role})` : 'Non assigné'"
  >
    <img
      v-if="props.user?.avatar"
      :src="props.user.avatar"
      :alt="props.user.name"
      class="avatar-img"
    >
    <span v-else class="avatar-fallback">
      {{ initials }}
    </span>
  </div>
</template>

<style scoped>
.avatar {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  border-radius: 9999px;
  overflow: hidden;
  background-color: #334155;
  color: #f8fafc;
  font-weight: 700;
  user-select: none;
  flex-shrink: 0;
}

.avatar-sm {
  width: 28px;
  height: 28px;
  font-size: 0.75rem;
}

.avatar-md {
  width: 36px;
  height: 36px;
  font-size: 0.875rem;
}

.avatar-lg {
  width: 48px;
  height: 48px;
  font-size: 1.125rem;
}

.avatar-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.avatar-fallback {
  line-height: 1;
}
</style>
```
