# Séance 1 : TypeScript pour Vue 3 & Nuxt 4

## 🎯 Objectifs de la séance

* Comprendre la valeur ajoutée de TypeScript dans un environnement de développement professionnel.
* Maîtriser le typage statique : types primitifs, types complexes, interfaces, type aliases et génériques (`Generics`).
* Éviter les pièges courants du typage : `any` vs `unknown`, assertion de type, type narrowing.
* Configurer et exploiter TypeScript en mode strict dans Vue 3 et Nuxt 4.
* Typer rigoureusement les composants Vue 3 avec la Composition API (`defineProps`, `defineEmits`, `ref`, `computed`).
* **Atelier Fil rouge DevFlow** : Modéliser le contrat de données initial du tracker de tâches `DevFlow`.

---

## 📖 1. Pourquoi TypeScript dans un projet pro ?

JavaScript est un langage à typage dynamique : les erreurs de type ne sont détectées qu'à l'exécution (*runtime*), souvent directement entre les mains des utilisateurs finaux en production.

TypeScript apporte un **typage statique optionnel et strict** à JavaScript.

```
┌─────────────────────────────────────────────────────────────┐
│                       Code TypeScript                       │
│             (Vérifié à la compilation / dans l'IDE)         │
└──────────────────────────────┬──────────────────────────────┘
                               │ Compilation / Transpilation
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                      Code JavaScript pur                    │
│             (Exécuté par le navigateur ou Node.js)          │
└─────────────────────────────────────────────────────────────┘
```

### Les bénéfices majeurs en entreprise

1. **Détection précoce des bugs** : Erreurs de typage, accès à des propriétés inexistantes (`undefined is not a function`), fautes de frappe détectées avant même d'exécuter l'application.
2. **Autocomplétion et Intellisense** : L'éditeur de code (VS Code / Cursor / WebStorm) devient un copilote qui suggère les props, méthodes et types exacts.
3. **Refactoring sécurisé** : Renommer une propriété ou modifier une signature de fonction met en surbrillance tous les endroits du projet à adapter.
4. **Auto-documentation vivante** : Les types servent de contrat clair entre développeurs sans documentation obsolète.

{% hint style="info" %}
**Règle d'or en BUT3 :** Tout code JavaScript valide est du TypeScript valide. Vous écrivez du JavaScript enrichi d'annotations de types que le compilateur élimine lors du build de production.
{% endhint %}

---

## 🏗️ 2. Fondations du système de types

### A. Types primitifs et littéraux

```typescript
// Types primitifs
const appName: string = 'DevFlow'
const taskCount: number = 42
const isCompleted: boolean = false
const emptyValue: null = null
const notAssigned: undefined = undefined

// Types littéraux : restreindre à une valeur exacte
let role: 'admin' | 'developer' | 'lead' = 'developer'
role = 'admin' // ✅ Valide
// role = 'manager' // ❌ Erreur TypeScript : Type '"manager"' is not assignable to type '"admin" | "developer" | "lead"'
```

### B. Tableaux et Tuples

```typescript
// Tableaux
const tags: string[] = ['frontend', 'nuxt', 'vue']
const scores: Array<number> = [12, 18, 15]

// Tuples : tableau de taille et de types fixes
const coordinate: [number, number] = [48.8566, 2.3522]
const httpStatus: [number, string] = [200, 'OK']
```

### C. Types personnalisés (`type`) vs Interfaces (`interface`)

* Utilisez `interface` pour définir la structure d'objets extensibles et les contrats de données métier.
* Utilisez `type` (Type Alias) pour les unions, tuples, primitives et types composés.

```typescript
// Interface pour un utilisateur
export interface User {
  id: number
  username: string
  email: string
  avatarUrl?: string // Propriété optionnelle (?)
  role: 'admin' | 'developer' | 'viewer'
  readonly createdAt: Date // Propriété en lecture seule
}

// Type alias pour une union
export type TaskPriority = 'low' | 'medium' | 'high' | 'urgent'
export type TaskStatus = 'todo' | 'in_progress' | 'in_review' | 'done'

// Interface pour une tâche
export interface Task {
  id: string
  title: string
  description: string
  priority: TaskPriority
  status: TaskStatus
  assignedTo?: User | null
  tags: string[]
  estimatedHours: number
  dueDate?: string
}
```

---

## ⚠️ 3. Gestion stricte : Bannir `any`, adopter `unknown`

Le type `any` désactive totalement le compilateur TypeScript. C'est l'équivalent d'abandonner toute sécurité.

### Le piège de `any`

```typescript
// ❌ MAUVAISE PRATIQUE : any annule la vérification
let badData: any = { name: 'Alice' }
badData.nonExistentMethod() // Aucune erreur à la compilation, mais CRASH en production !
```

### La sécurité avec `unknown` et le Type Narrowing

`unknown` indique qu'une valeur est inconnue à l'avance (ex: retour d'une API externe ou `JSON.parse`). TypeScript impose de vérifier le type avant de pouvoir manipuler la variable.

```typescript
// ✅ BONNE PRATIQUE : unknown force la vérification
function processApiResponse(rawData: unknown) {
  // rawData.title // ❌ Erreur : 'rawData' is of type 'unknown'

  // Type Narrowing (Raffinement de type)
  if (typeof rawData === 'object' && rawData !== null && 'title' in rawData) {
    const task = rawData as { title: string }
    console.log('Titre de la tâche :', task.title) // ✅ Valide et sécurisé
  }
}
```

---

## 🧬 4. Les Génériques (`Generics`)

Les génériques permettent d'écrire des fonctions, des classes et des interfaces réutilisables qui fonctionnent avec n'importe quel type tout en conservant un typage strict.

### Exemple : Wrapper de réponse API standardisé

```typescript
// T est un paramètre de type générique
export interface ApiResponse<T> {
  data: T
  status: number
  message: string
  timestamp: number
}

// Utilisation avec différents types
type UserListResponse = ApiResponse<User[]>
type TaskDetailResponse = ApiResponse<Task>

// Fonction générique utilitaire
function wrapInResponse<T>(payload: T): ApiResponse<T> {
  return {
    data: payload,
    status: 200,
    message: 'Success',
    timestamp: Date.now()
  }
}
```

---

## ⚙️ 5. Configuration stricte : `tsconfig.json`

Dans un projet Nuxt 4, le fichier `tsconfig.json` étend la configuration générée automatiquement par Nuxt (`.nuxt/tsconfig.json`).

```json
{
  "extends": "./.nuxt/tsconfig.json",
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true
  }
}
```

{% hint style="warning" %}
**Exigence d'évaluation :** Lors des rendus et soutenances, l'option `strict: true` doit être activée sans générer d'erreurs de build (`npx vue-tsc --noEmit`).
{% endhint %}

---

## 🧩 6. TypeScript avec Vue 3 & Nuxt 4 (Composition API)

Dans Vue 3 avec `<script setup lang="ts">`, le typage est totalement intégré.

### A. Typage des Props (`defineProps`)

```vue
<!-- components/TaskBadge.vue -->
<script setup lang="ts">
import type { TaskPriority } from '~/types/devflow'

// Définition de l'interface des Props
interface Props {
  priority: TaskPriority
  showIcon?: boolean
}

// Définition avec valeurs par défaut grâce à withDefaults
const props = withDefaults(defineProps<Props>(), {
  showIcon: true
})
</script>

<template>
  <span :class="['badge', `badge-${props.priority}`]">
    <span v-if="props.showIcon">🏷️</span>
    {{ props.priority.toUpperCase() }}
  </span>
</template>
```

### B. Typage des Événements (`defineEmits`)

```vue
<!-- components/TaskCard.vue -->
<script setup lang="ts">
import type { Task, TaskStatus } from '~/types/devflow'

interface Props {
  task: Task
}

const props = defineProps<Props>()

// Typage strict des événements émis
const emit = defineEmits<{
  (e: 'update-status', taskId: string, newStatus: TaskStatus): void
  (e: 'delete', taskId: string): void
  (e: 'select', task: Task): void
}>()
</script>
```

### C. Typage des `ref()` et `computed()`

```typescript
import { ref, computed } from 'vue'
import type { Task, User } from '~/types/devflow'

// 1. Primitive avec inférence automatique
const count = ref(0) // Inférence : Ref<number>

// 2. Objet complexe avec union / null
const currentTask = ref<Task | null>(null)
const selectedUsers = ref<User[]>([])

// 3. Computed typé
const isTaskUrgent = computed<boolean>(() => {
  return currentTask.value?.priority === 'urgent'
})
```

---

## 🛠️ 7. Atelier Pratique : Fil Rouge "DevFlow" (Étape 0)

### Contexte de la mission DevSquad

En tant que nouvelle recrue chez **DevSquad**, vous devez concevoir la modélisation typée de **DevFlow**, notre tracker de tâches interne.

### Exercice 1 : Modélisation des types métier

Dans votre projet, créez un fichier `types/devflow.ts` et déclarez les types suivants avec les contraintes associées :

1. **`TaskPriority`** : union de types littéraux `'low' | 'medium' | 'high' | 'urgent'`.
2. **`TaskStatus`** : union de types littéraux `'todo' | 'in_progress' | 'in_review' | 'done'`.
3. **`User`** : interface décrivant un utilisateur (`id`, `name`, `email`, `avatar`, `role` parmi `'lead' | 'frontend' | 'backend' | 'qa'`).
4. **`Task`** : interface décrivant une tâche (`id`, `title`, `description`, `status`, `priority`, `assignee` nullable, `tags`, `estimatedHours`, `createdAt`, `completedAt` optionnel).
5. **`TaskFilterOptions`** : interface pour stocker l'état des filtres du dashboard (`searchQuery`, `statusFilter`, `priorityFilter`, `assigneeId`).

### Exercice 2 : Composant typé `UserAvatar.vue`

Créez le composant `components/UserAvatar.vue` :
* **Props** :
  * `user` : information partielle de l'utilisateur (nom, avatar, rôle) ou `null`.
  * `size` : `'sm' | 'md' | 'lg'` avec `'md'` par défaut via `withDefaults`.
* **Fonctionnement** :
  * Si l'avatar (URL) est renseigné, afficher l'image.
  * Sinon, calculer et afficher les **initiales** de l'utilisateur en majuscules (ex: "Alexandre Dubois" $\rightarrow$ "AD").
  * Si `user` est `null`, afficher "?" avec un style neutre.

{% hint style="tip" %}
La solution détaillée et commentée de cet atelier est disponible dans la page [Séance 1 (correction)](seance-1-correction.md).
{% endhint %}

---

## 🎯 Auto-évaluation & Checklist

Avant de passer à la séance suivante, assurez-vous d'avoir validé :

* [ ] Je sais expliquer la différence entre `any` et `unknown`.
* [ ] Je sais typer les props et les emits d'un composant Vue avec `<script setup lang="ts">`.
* [ ] J'ai créé le fichier `types/devflow.ts` avec les interfaces `User`, `Task` et `TaskFilterOptions`.
* [ ] Mon composant `UserAvatar.vue` compile sans erreur TypeScript en mode strict.
