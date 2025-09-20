# Séance 2 – Architecture et composables

## 🎯 Objectifs
- Comprendre la struc### Composables

## 📖 Partie théorique

### Organisation du projet

#### Structure d'un projet Vue 3

Organisation recommandée pour un projet Vue 3 professionnel :

```
mon-projet-vue/
├── public/                 # Fichiers statiques
│   ├── index.html         # Template HTML principal
│   └── favicon.ico
├── src/                   # Code source
│   ├── assets/           # Ressources (images, CSS, fonts)
│   │   ├── css/
│   │   ├── images/
│   │   └── fonts/
│   ├── components/       # Composants réutilisables
│   │   ├── common/      # Composants génériques (Button, Modal)
│   │   └── features/    # Composants spécifiques (TaskCard, UserProfile)
│   ├── composables/      # Logique réutilisable (hooks)
│   │   ├── useAuth.js
│   │   ├── useApi.js
│   │   └── useTasks.js
│   ├── stores/          # Gestion d'état Pinia
│   │   ├── auth.js
│   │   ├── tasks.js
│   │   └── index.js
│   ├── services/        # Services API et logique métier
│   │   ├── api.js
│   │   ├── taskService.js
│   │   └── authService.js
│   ├── router/          # Configuration des routes
│   │   └── index.js
│   ├── views/           # Pages principales
│   │   ├── Home.vue
│   │   ├── Login.vue
│   │   └── Dashboard.vue
│   ├── utils/           # Fonctions utilitaires
│   │   ├── helpers.js
│   │   └── constants.js
│   ├── App.vue          # Composant racine
│   └── main.js          # Point d'entrée
├── package.json         # Dépendances et scripts
├── vite.config.js       # Configuration Vite
└── README.md
```

**Conventions de nommage :**

```javascript
<!-- PascalCase pour les composants -->
<TaskCard />
<UserProfile />

<!-- camelCase pour les props et variables -->
<TaskCard :taskData="task" :isEditable="true" />

<!-- kebab-case pour les événements -->
@task-updated="handleUpdate"
@user-logged-in="onLogin"
```


#### Dossiers principaux :

  - `components/` : composants Vue réutilisables
  - `stores/` : gestion d’état globale (Pinia)
  - `composables/` : fonctions réactives réutilisables
  - `services/` : accès aux API et logique métier
  - `views/` : pages principales de l’application



#### Tableau de synthèse des dossiers principaux

| Dossier | Rôle | Contenu | Exemple | Réutilisabilité | Gestion d'état |
|---------|------|---------|---------|-----------------|----------------|
| `components/` | Interface utilisateur | Composants Vue avec template, script, style | `TaskCard.vue`, `Button.vue` | ✅ Haute | État local uniquement |
| `stores/` | État global partagé | Stores Pinia avec state, getters, actions | `useAuthStore.js`, `useTaskStore.js` | ✅ Globale | ✅ État global réactif |
| `composables/` | Logique métier réutilisable | Fonctions avec réactivité Vue | `useTasks.js`, `useAuth.js` | ✅ Très haute | État réactif local |
| `services/` | Accès aux données | Classes/fonctions pour API et logique | `taskService.js`, `apiClient.js` | ✅ Moyenne | ❌ Pas d'état |
| `views/` | Pages de l'application | Composants de page/route | `HomePage.vue`, `ProfilePage.vue` | ❌ Faible | État local de page |

#### Détail des responsabilités

**`components/` - Composants Vue réutilisables**
- **Rôle** : Éléments d'interface utilisateur
- **Contenu** : Template, logique d'affichage, styles
- **Exemples** : `TaskCard.vue`, `Modal.vue`, `Button.vue`
- **Communication** : Props (entrée) et emits (sortie)

**`stores/` - Gestion d'état globale (Pinia)**
- **Rôle** : Centraliser l'état partagé entre composants
- **Contenu** : State, getters, actions
- **Exemples** : `useAuthStore.js`, `useTaskStore.js`
- **Avantages** : Persistance, réactivité globale, DevTools

**`composables/` - Fonctions réactives réutilisables**
- **Rôle** : Factoriser la logique métier avec réactivité
- **Contenu** : Fonctions utilisant ref, computed, watch
- **Exemples** : `useTasks.js`, `useLocalStorage.js`
- **Avantages** : Testabilité, réutilisabilité, séparation des préoccupations

**`services/` - Accès aux API et logique métier**
- **Rôle** : Interaction avec APIs et traitements de données
- **Contenu** : Classes/fonctions sans réactivité Vue
- **Exemples** : `taskService.js`, `authService.js`, `apiClient.js`
- **Avantages** : Abstraction des APIs, logique pure

**`views/` - Pages principales de l'application**
- **Rôle** : Composants de niveau page liés au routing
- **Contenu** : Layout de page, orchestration de composants
- **Exemples** : `HomePage.vue`, `TaskListPage.vue`
- **Particularité** : Souvent connectés aux routes du routerfixe `use` (ex: `useCounter`, `useAuth`)




### Les composables

#### Qu'est-ce qu'un composable ?

- Fonctions réutilisables exploitant la réactivité de Vue.
- Permettent de factoriser la logique métier et d'éviter la duplication de code.

#### Exemple complet : useTasks

**Créer `src/composables/useTasks.js` :**

```javascript
import { ref, computed } from 'vue'

export function useTasks() {
  // État réactif
  const tasks = ref([
    {
      id: 1,
      title: 'Apprendre Vue 3',
      description: 'Maîtriser la Composition API',
      priority: 'High',
      completed: false,
      createdAt: new Date('2024-09-01')
    },
    {
      id: 2,
      title: 'Créer un projet',
      description: 'Développer une application complète',
      priority: 'Medium',
      completed: true,
      createdAt: new Date('2024-09-15')
    }
  ])

  const loading = ref(false)
  const error = ref(null)

  // Computed properties
  const totalTasks = computed(() => tasks.value.length)
  
  const completedTasks = computed(() => 
    tasks.value.filter(task => task.completed)
  )
  
  const pendingTasks = computed(() => 
    tasks.value.filter(task => !task.completed)
  )
  
  const highPriorityTasks = computed(() =>
    tasks.value.filter(task => task.priority === 'High')
  )

  const completionRate = computed(() => {
    if (totalTasks.value === 0) return 0
    return Math.round((completedTasks.value.length / totalTasks.value) * 100)
  })

  // Méthodes
  const addTask = (taskData) => {
    const newTask = {
      id: Date.now(),
      title: taskData.title,
      description: taskData.description,
      priority: taskData.priority || 'Medium',
      completed: false,
      createdAt: new Date()
    }
    tasks.value.push(newTask)
  }

  const deleteTask = (taskId) => {
    tasks.value = tasks.value.filter(task => task.id !== taskId)
  }

  const toggleComplete = (taskId) => {
    const task = tasks.value.find(task => task.id === taskId)
    if (task) {
      task.completed = !task.completed
    }
  }

  const updateTask = (taskId, updates) => {
    const taskIndex = tasks.value.findIndex(task => task.id === taskId)
    if (taskIndex !== -1) {
      tasks.value[taskIndex] = { ...tasks.value[taskIndex], ...updates }
    }
  }

  const getTasksByPriority = (priority) => {
    return computed(() => 
      tasks.value.filter(task => task.priority === priority)
    )
  }

  const clearCompletedTasks = () => {
    tasks.value = tasks.value.filter(task => !task.completed)
  }

  // Simuler un appel API
  const fetchTasks = async () => {
    try {
      loading.value = true
      error.value = null
      
      // Simulation d'un appel API
      await new Promise(resolve => setTimeout(resolve, 1000))
      
      // En réalité, on ferait : 
      // const response = await fetch('/api/tasks')
      // tasks.value = await response.json()
      
      console.log('Tâches chargées depuis l\'API (simulé)')
    } catch (err) {
      error.value = 'Erreur lors du chargement des tâches'
      console.error(err)
    } finally {
      loading.value = false
    }
  }

  // Retourner l'état et les méthodes
  return {
    // État
    tasks: tasks.value, // ou tasks si on veut la réactivité complète
    loading,
    error,
    
    // Computed
    totalTasks,
    completedTasks,
    pendingTasks,
    highPriorityTasks,
    completionRate,
    
    // Méthodes
    addTask,
    deleteTask,
    toggleComplete,
    updateTask,
    getTasksByPriority,
    clearCompletedTasks,
    fetchTasks
  }
}
```

#### Utilisation du composable dans un composant

**Dans `TaskManager.vue` :**

```javascript
<template>
  <div class="task-manager">
    <div class="stats">
      <h2>📊 Statistiques</h2>
      <div class="stats-grid">
        <div class="stat-item">
          <span class="stat-number">{{ totalTasks }}</span>
          <span class="stat-label">Total</span>
        </div>
        <div class="stat-item">
          <span class="stat-number">{{ completedTasks.length }}</span>
          <span class="stat-label">Terminées</span>
        </div>
        <div class="stat-item">
          <span class="stat-number">{{ pendingTasks.length }}</span>
          <span class="stat-label">En cours</span>
        </div>
        <div class="stat-item">
          <span class="stat-number">{{ completionRate }}%</span>
          <span class="stat-label">Progression</span>
        </div>
      </div>
    </div>

    <div class="task-form">
      <h3>Ajouter une tâche</h3>
      <input v-model="newTaskTitle" placeholder="Titre">
      <textarea v-model="newTaskDescription" placeholder="Description"></textarea>
      <select v-model="newTaskPriority">
        <option value="Low">Basse</option>
        <option value="Medium">Moyenne</option>
        <option value="High">Haute</option>
      </select>
      <button @click="handleAddTask">Ajouter</button>
    </div>

    <div class="task-list">
      <div v-if="loading" class="loading">Chargement...</div>
      <div v-if="error" class="error">{{ error }}</div>
      
      <div v-for="task in pendingTasks" :key="task.id" class="task-item">
        <h4>{{ task.title }}</h4>
        <p>{{ task.description }}</p>
        <div class="task-actions">
          <button @click="toggleComplete(task.id)">
            {{ task.completed ? 'Rouvrir' : 'Terminer' }}
          </button>
          <button @click="deleteTask(task.id)" class="danger">
            Supprimer
          </button>
        </div>
      </div>
    </div>

    <button @click="clearCompletedTasks" v-if="completedTasks.length > 0">
      Effacer les tâches terminées ({{ completedTasks.length }})
    </button>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useTasks } from '@/composables/useTasks'

// Utilisation du composable
const {
  tasks,
  loading,
  error,
  totalTasks,
  completedTasks,
  pendingTasks,
  completionRate,
  addTask,
  deleteTask,
  toggleComplete,
  clearCompletedTasks,
  fetchTasks
} = useTasks()

// État local du formulaire
const newTaskTitle = ref('')
const newTaskDescription = ref('')
const newTaskPriority = ref('Medium')

// Méthodes locales
const handleAddTask = () => {
  if (newTaskTitle.value.trim()) {
    addTask({
      title: newTaskTitle.value,
      description: newTaskDescription.value,
      priority: newTaskPriority.value
    })
    
    // Reset du formulaire
    newTaskTitle.value = ''
    newTaskDescription.value = ''
    newTaskPriority.value = 'Medium'
  }
}

// Charger les tâches au montage du composant
onMounted(() => {
  fetchTasks()
})
</script>
```

#### Avantages des composables

1. **Réutilisabilité** : Logique partagée entre plusieurs composants
2. **Testabilité** : Fonctions isolées faciles à tester
3. **Lisibilité** : Composants plus propres et focalisés sur l'UI
4. **Maintenance** : Logique centralisée et organiséetion d’un projet Vue.
- Découvrir la factorisation de logique avec les composables.


### Les services

#### Qu'est-ce qu'un service ?

- Classes ou fonctions dédiées à l'accès aux API et à la logique métier.
- Séparent la logique de données de l'interface utilisateur.

#### Exemples de services

1. **Service API** : Gère les appels HTTP vers une API externe.
   ```js
   // src/services/api.js
   import axios from 'axios'

   const apiClient = axios.create({
     baseURL: 'https://api.example.com',
     headers: {
       'Content-Type': 'application/json'
     }
   })

   export default {
     getTasks() {
       return apiClient.get('/tasks')
     },
     createTask(task) {
       return apiClient.post('/tasks', task)
     },
     // Autres méthodes...
   }
   ```

2. **Service de gestion des tâches** : Contient la logique métier liée aux tâches.
   ```js
   // src/services/taskService.js
   import api from './api'

   export const fetchTasks = async () => {
     const response = await api.getTasks()
     return response.data
   }

   export const createTask = async (task) => {
     const response = await api.createTask(task)
     return response.data
   }

   // Autres méthodes...
   ```

#### Avantages des services

- **Séparation des préoccupations** : Distingue la logique métier de l'UI.
- **Réutilisabilité** : Services utilisables dans différents composants ou composables.
- **Testabilité** : Logique métier isolée, facilitant les tests unitaires.
- **Maintenance** : Centralisation de la logique métier et des interactions API.
- **Scalabilité** : Facilité d'ajout de nouvelles fonctionnalités sans impacter l'UI.
- **Abstraction** : Masque les détails d'implémentation des API.
- **Gestion des erreurs** : Centralise la gestion des erreurs liées aux API.
- **Performance** : Optimise les appels API (caching, batching).
- **Documentation** : Facilite la compréhension de la logique métier.
- **Collaboration** : Simplifie le travail en équipe en définissant des interfaces claires.
- **Flexibilité** : Permet de changer l'implémentation sans affecter les consommateurs.


### Design patterns

- Smart vs Dumb components :
  - Smart : gèrent la logique, les données et l’état
  - Dumb : affichent uniquement les données reçues
- Container vs Presentational components :
  - Container : connectés au store, gèrent la logique
  - Presentational : affichent l’UI, reçoivent les props

#### Smart vs Dumb components


**Smart Component (TaskManager.vue)**
- Gère la logique métier : utilise le composable useTasks()
- État local : formulaire avec newTaskTitle
- Computed properties : completedTasks, completionRate
- Méthodes avec logique : addNewTask(), handleToggleComplete(), handleDeleteTask()
- Orchestration : coordonne plusieurs TaskCard

**Dumb Component (TaskCard.vue)**
- Affichage pur : reçoit uniquement task via props
- Aucune logique métier : juste présentation des données
- Émissions d'événements : toggle-complete, delete sans traitement
- Validation de props : assure la structure des données reçues
- Styles intégrés : focus sur l'apparence

**Avantages expliqués**
1. Réutilisabilité : TaskCard peut être utilisé partout
2. Testabilité : Logique séparée, tests plus faciles
3. Maintenance : Responsabilités claires
4. Lisibilité : Code mieux organisé
  
Le pattern montre clairement la séparation entre :

**Smart** = Logique + Données + État
**Dumb** = Affichage + Props + Events

#### Container vs Presentational Components

Pattern similaire mais avec une approche plus axée sur la séparation des données.

**Container Components**
- Connectés aux sources de données (store, API, composables)
- Gèrent les effets de bord et la logique d'état
- Fournissent les données aux composants présentationnels

**Exemple de Container Component :**
```javascript
<!-- TaskListContainer.vue (Container) -->
<template>
  <TaskListPresentation
    :tasks="tasks"
    :loading="loading"
    :error="error"
    :filters="availableFilters"
    :current-filter="currentFilter"
    @filter-change="updateFilter"
    @task-action="handleTaskAction"
  />
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useTaskStore } from '@/stores/taskStore'
import TaskListPresentation from './TaskListPresentation.vue'

// Connexion au store (source de données)
const taskStore = useTaskStore()
const { tasks, loading, error } = taskStore

// État local du container
const currentFilter = ref('all')
const availableFilters = [
  { value: 'all', label: 'Toutes' },
  { value: 'pending', label: 'En cours' },
  { value: 'completed', label: 'Terminées' }
]

// Logique de gestion des données
const updateFilter = (newFilter) => {
  currentFilter.value = newFilter
  taskStore.filterTasks(newFilter)
}

const handleTaskAction = ({ action, taskId }) => {
  switch (action) {
    case 'complete':
      taskStore.completeTask(taskId)
      break
    case 'delete':
      taskStore.deleteTask(taskId)
      break
    case 'edit':
      taskStore.startEditTask(taskId)
      break
  }
}

// Chargement initial des données
onMounted(() => {
  taskStore.fetchTasks()
})
</script>
```

**Presentational Components**
- Reçoivent toutes les données via props
- Émettent des événements pour toute interaction
- Aucune connaissance du contexte métier

**Exemple de Presentational Component :**
```javascript
<!-- TaskListPresentation.vue (Presentational) -->
<template>
  <div class="task-list-wrapper">
    <!-- Filtres -->
    <div class="filter-bar">
      <button
        v-for="filter in filters"
        :key="filter.value"
        @click="$emit('filter-change', filter.value)"
        :class="{ active: filter.value === currentFilter }"
      >
        {{ filter.label }}
      </button>
    </div>

    <!-- États de chargement/erreur -->
    <div v-if="loading" class="loading-state">
      <span class="spinner"></span>
      Chargement des tâches...
    </div>

    <div v-else-if="error" class="error-state">
      <span class="error-icon">⚠️</span>
      {{ error }}
    </div>

    <!-- Liste des tâches -->
    <div v-else-if="tasks.length" class="task-grid">
      <div
        v-for="task in tasks"
        :key="task.id"
        class="task-item"
        @click="$emit('task-action', { action: 'view', taskId: task.id })"
      >
        <h4>{{ task.title }}</h4>
        <p>{{ task.description }}</p>
        
        <div class="task-actions" @click.stop>
          <button @click="$emit('task-action', { action: 'complete', taskId: task.id })">
            {{ task.completed ? 'Rouvrir' : 'Terminer' }}
          </button>
          <button @click="$emit('task-action', { action: 'edit', taskId: task.id })">
            Modifier
          </button>
          <button @click="$emit('task-action', { action: 'delete', taskId: task.id })">
            Supprimer
          </button>
        </div>
      </div>
    </div>

    <!-- État vide -->
    <div v-else class="empty-state">
      <span class="empty-icon">📝</span>
      <h3>Aucune tâche</h3>
      <p>Commencez par créer votre première tâche !</p>
    </div>
  </div>
</template>

<script setup>
// Props pures (pas de logique métier)
defineProps({
  tasks: Array,
  loading: Boolean,
  error: String,
  filters: Array,
  currentFilter: String
})

// Événements émis (délégation vers le container)
defineEmits(['filter-change', 'task-action'])
</script>

<style scoped>
.task-list-wrapper { /* styles */ }
.filter-bar button.active { background: #007bff; color: white; }
.loading-state, .error-state, .empty-state { 
  text-align: center; 
  padding: 2rem; 
}
</style>
```

#### Avantages de ces patterns

**Pour Smart/Dumb :**
- **Réutilisabilité** : Les Dumb components sont facilement réutilisables
- **Testabilité** : Logique séparée de l'affichage
- **Maintenance** : Responsabilités claires

**Pour Container/Presentational :**
- **Séparation des préoccupations** : Données vs présentation
- **Flexibilité** : Changement de source de données sans impact sur l'UI
- **Performance** : Optimisations possibles au niveau container(images, CSS, fonts)

## 📝 Travaux pratiques
- Refactor TP précédent avec un composable `useTasks`.
- Ajouter `useCounter` pour pratiquer.

