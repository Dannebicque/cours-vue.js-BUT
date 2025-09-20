# Séance 1 – Introduction & rappel Vue

## 🎯 Objectifs
- Vérifier et réactiver les acquis de BUT2.
- Mettre en place un projet clean (environnement, conventions).

## 📖 Partie théorique

### Directives Vue et structure de projet

#### Directives Vue de base

Les **directives** sont des attributs spéciaux avec le préfixe `v-` qui appliquent un comportement réactif au DOM.

```vue
<template>
  <div>
    <!-- v-if : affichage conditionnel -->
    <p v-if="user.isLoggedIn">Bienvenue {{ user.name }} !</p>
    <p v-else>Veuillez vous connecter</p>
    
    <!-- v-show : visibilité conditionnelle (CSS display) -->
    <div v-show="showDetails" class="details">Détails supplémentaires</div>
    
    <!-- v-for : boucles -->
    <ul>
      <li v-for="task in tasks" :key="task.id">
        {{ task.title }} - {{ task.status }}
      </li>
    </ul>
    
    <!-- v-model : liaison bidirectionnelle -->
    <input v-model="searchQuery" placeholder="Rechercher...">
    <p>Recherche : {{ searchQuery }}</p>
    
    <!-- v-bind (:) : liaison d'attributs -->
    <button 
      :class="{ active: isActive, disabled: isDisabled }"
      :disabled="isDisabled"
      @click="toggleActive"
    >
      {{ isActive ? 'Actif' : 'Inactif' }}
    </button>
    
    <!-- v-on (@) : gestion d'événements -->
    <button @click="handleClick" @keyup.enter="handleEnter">
      Cliquer ou Entrée
    </button>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'

const user = reactive({
  isLoggedIn: true,
  name: 'Alice'
})

const showDetails = ref(false)
const searchQuery = ref('')
const isActive = ref(false)
const isDisabled = ref(false)

const tasks = ref([
  { id: 1, title: 'Tâche 1', status: 'En cours' },
  { id: 2, title: 'Tâche 2', status: 'Terminée' }
])

const toggleActive = () => {
  isActive.value = !isActive.value
}

const handleClick = () => {
  console.log('Bouton cliqué')
}

const handleEnter = () => {
  console.log('Entrée pressée')
}
</script>
```

### Rappel Vue 3 : Composition API

#### ref() - Réactivité pour les primitives

`ref()` crée une référence réactive pour les valeurs primitives (string, number, boolean).

```vue
<template>
  <div>
    <p>Compteur : {{ count }}</p>
    <button @click="increment">+1</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const count = ref(0)

const increment = () => {
  count.value++ // Accès à la valeur via .value
}
</script>
```

#### reactive() - Réactivité pour les objets

`reactive()` rend un objet entièrement réactif, toutes ses propriétés deviennent réactives.

```vue
<template>
  <div>
    <p>{{ user.name }} - {{ user.age }} ans</p>
    <button @click="birthday">Anniversaire</button>
  </div>
</template>

<script setup>
import { reactive } from 'vue'

const user = reactive({
  name: 'Alice',
  age: 25
})

const birthday = () => {
  user.age++ // Pas besoin de .value pour les objets reactive
}
</script>
```

#### computed() - Propriétés calculées

`computed()` crée des valeurs dérivées qui se mettent à jour automatiquement quand leurs dépendances changent.

```vue
<template>
  <div>
    <input v-model="firstName" placeholder="Prénom">
    <input v-model="lastName" placeholder="Nom">
    <p>Nom complet : {{ fullName }}</p>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const firstName = ref('')
const lastName = ref('')

const fullName = computed(() => {
  return `${firstName.value} ${lastName.value}`.trim()
})
</script>
```

#### watch() - Surveillance des changements

`watch()` permet d'exécuter du code quand une valeur réactive change.

```vue
<script setup>
import { ref, watch } from 'vue'

const count = ref(0)
const message = ref('')

// Surveiller une seule valeur
watch(count, (newValue, oldValue) => {
  console.log(`Compteur: ${oldValue} → ${newValue}`)
})

// Surveiller plusieurs valeurs
watch([count, message], ([newCount, newMessage], [oldCount, oldMessage]) => {
  console.log('Valeurs changées:', { newCount, newMessage })
})

// Watch avec options
watch(count, (newVal) => {
  if (newVal > 10) {
    alert('Compteur dépassé !')
  }
}, { immediate: true }) // Exécute immédiatement
</script>
```

#### Cycle de vie des composants

Les hooks de cycle de vie permettent d'exécuter du code à des moments précis de la vie du composant.

```vue
<template>
  <div>
    <p>{{ data }}</p>
  </div>
</template>

<script setup>
import { ref, onMounted, onUpdated, onUnmounted } from 'vue'

const data = ref(null)
let interval = null

// Quand le composant est monté dans le DOM
onMounted(() => {
  console.log('Composant monté')
  // Charger des données
  fetchData()
  // Démarrer un timer
  interval = setInterval(() => {
    console.log('Timer actif')
  }, 1000)
})

// Quand le composant est mis à jour
onUpdated(() => {
  console.log('Composant mis à jour')
})

// Quand le composant va être détruit
onUnmounted(() => {
  console.log('Composant détruit')
  // Nettoyer les ressources
  if (interval) {
    clearInterval(interval)
  }
})

const fetchData = async () => {
  // Simulation d'un appel API
  data.value = 'Données chargées'
}
</script>
```

### Props, emits et slots

#### Props - Transmission de données du parent vers l'enfant

Les **props** permettent de passer des données du composant parent vers le composant enfant.

```vue
<!-- TaskCard.vue (composant enfant) -->
<template>
  <div class="task-card">
    <h3>{{ task.title }}</h3>
    <p>{{ task.description }}</p>
    <span class="priority" :class="priorityClass">{{ task.priority }}</span>
    <p>Assigné à : {{ assignedTo }}</p>
  </div>
</template>

<script setup>
import { computed } from 'vue'

// Définition des props avec validation
const props = defineProps({
  task: {
    type: Object,
    required: true,
    validator: (task) => task.title && task.description
  },
  assignedTo: {
    type: String,
    default: 'Non assigné'
  }
})

// Utilisation des props dans une computed
const priorityClass = computed(() => {
  return `priority-${props.task.priority.toLowerCase()}`
})
</script>
```

```vue
<!-- TaskList.vue (composant parent) -->
<template>
  <div>
    <TaskCard 
      v-for="task in tasks" 
      :key="task.id"
      :task="task" 
      :assigned-to="task.assignee"
    />
  </div>
</template>

<script setup>
const tasks = [
  { 
    id: 1, 
    title: 'Développer la homepage', 
    description: 'Créer la page d\'accueil', 
    priority: 'High',
    assignee: 'Alice'
  }
]
</script>
```

#### Emits - Remontée d'événements de l'enfant vers le parent

Les **emits** permettent au composant enfant de notifier le parent d'un événement.

```vue
<!-- TaskCard.vue (composant enfant) -->
<template>
  <div class="task-card">
    <h3>{{ task.title }}</h3>
    <p>{{ task.description }}</p>
    <div class="actions">
      <button @click="markAsComplete">Terminer</button>
      <button @click="deleteTask" class="danger">Supprimer</button>
      <button @click="editTask">Modifier</button>
    </div>
  </div>
</template>

<script setup>
// Définition des props
const props = defineProps(['task'])

// Définition des événements émis
const emit = defineEmits(['task-completed', 'task-deleted', 'task-edited'])

const markAsComplete = () => {
  // Émet un événement avec des données
  emit('task-completed', {
    taskId: props.task.id,
    completedAt: new Date()
  })
}

const deleteTask = () => {
  // Émet un événement simple
  emit('task-deleted', props.task.id)
}

const editTask = () => {
  // Émet un événement avec l'objet complet
  emit('task-edited', props.task)
}
</script>
```

```vue
<!-- TaskList.vue (composant parent) -->
<template>
  <div>
    <TaskCard 
      v-for="task in tasks" 
      :key="task.id"
      :task="task"
      @task-completed="handleTaskCompleted"
      @task-deleted="handleTaskDeleted"
      @task-edited="handleTaskEdited"
    />
  </div>
</template>

<script setup>
import { ref } from 'vue'

const tasks = ref([
  { id: 1, title: 'Tâche 1', description: 'Description 1' },
  { id: 2, title: 'Tâche 2', description: 'Description 2' }
])

const handleTaskCompleted = (data) => {
  console.log(`Tâche ${data.taskId} terminée le ${data.completedAt}`)
  // Mettre à jour le statut de la tâche
}

const handleTaskDeleted = (taskId) => {
  tasks.value = tasks.value.filter(task => task.id !== taskId)
}

const handleTaskEdited = (task) => {
  console.log('Éditer la tâche:', task)
  // Ouvrir un modal d'édition par exemple
}
</script>
```

#### Slots - Injection de contenu personnalisé

Les **slots** permettent de créer des composants avec du contenu personnalisable. Le composant parent peut injecter du HTML/contenu dans des emplacements définis par le composant enfant.

**Slot simple :**

```vue
<!-- Modal.vue -->
<template>
  <div class="modal-overlay" v-if="show">
    <div class="modal">
      <header class="modal-header">
        <h2>{{ title }}</h2>
        <button @click="close">✕</button>
      </header>
      <div class="modal-body">
        <slot></slot> <!-- Contenu personnalisable -->
      </div>
      <footer class="modal-footer">
        <slot name="actions">
          <!-- Contenu par défaut si aucun slot "actions" fourni -->
          <button @click="close">Fermer</button>
        </slot>
      </footer>
    </div>
  </div>
</template>

<script setup>
defineProps(['show', 'title'])
const emit = defineEmits(['close'])

const close = () => emit('close')
</script>
```

```vue
<!-- Utilisation du Modal -->
<template>
  <div>
    <button @click="showModal = true">Ouvrir modal</button>
    
    <Modal 
      :show="showModal" 
      title="Confirmer la suppression"
      @close="showModal = false"
    >
      <!-- Contenu du slot par défaut -->
      <p>Êtes-vous sûr de vouloir supprimer cette tâche ?</p>
      <p><strong>{{ taskToDelete.title }}</strong></p>
      
      <!-- Contenu du slot nommé "actions" -->
      <template #actions>
        <button @click="confirmDelete" class="danger">Supprimer</button>
        <button @click="showModal = false">Annuler</button>
      </template>
    </Modal>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const showModal = ref(false)
const taskToDelete = ref({ title: 'Ma tâche importante' })

const confirmDelete = () => {
  console.log('Tâche supprimée')
  showModal.value = false
}
</script>
```

**Slots nommés :**

```vue
<!-- LayoutComponent.vue -->
<template>
  <div class="layout">
    <header>
      <slot name="header"></slot>
    </header>
    <main>
      <slot></slot> <!-- slot par défaut -->
    </main>
    <footer>
      <slot name="footer"></slot>
    </footer>
  </div>
</template>
```

```vue
<!-- Parent.vue -->
<template>
  <LayoutComponent>
    <template #header>
      <h1>Titre de la page</h1>
    </template>
    
    <p>Contenu principal</p>
    
    <template #footer>
      <p>© 2024 Mon site</p>
    </template>
  </LayoutComponent>
</template>
```

### Communication entre composants - Résumé

- **Props** : Parent → Enfant (données descendantes)
- **Emits** : Enfant → Parent (événements remontants)  
- **Slots** : Parent → Enfant (contenu HTML personnalisé)

Cette combinaison permet une communication flexible et maintenable entre les composants Vue.

### Bonnes pratiques

#### Organisation du code

**Structuration des composants :**

```vue
<!-- Ordre recommandé dans un composant Vue -->
<template>
  <!-- Template clair et lisible -->
</template>

<script setup>
// 1. Imports
import { ref, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import TaskCard from '@/components/TaskCard.vue'

// 2. Props
const props = defineProps({
  initialData: Object
})

// 3. Emits
const emit = defineEmits(['update', 'delete'])

// 4. Variables réactives
const tasks = ref([])
const isLoading = ref(false)

// 5. Computed
const filteredTasks = computed(() => {
  return tasks.value.filter(task => task.isVisible)
})

// 6. Methods
const handleUpdate = () => {
  emit('update', tasks.value)
}

// 7. Lifecycle hooks
onMounted(() => {
  fetchTasks()
})
</script>

<style scoped>
/* Styles spécifiques au composant */
</style>
```

#### Conventions de nommage

```javascript
// Variables et fonctions : camelCase
const userName = ref('')
const isLoggedIn = ref(false)
const handleUserLogin = () => {}

// Constantes : UPPER_SNAKE_CASE
const API_BASE_URL = 'https://api.example.com'
const MAX_RETRY_ATTEMPTS = 3

// Composants : PascalCase
// TaskCard.vue, UserProfile.vue, NavigationMenu.vue

// Fichiers : kebab-case
// user-service.js, task-helpers.js, api-config.js
```

#### Performance et réactivité

```vue
<script setup>
import { ref, computed, shallowRef, markRaw } from 'vue'

// ✅ Bon : ref pour les primitives
const count = ref(0)
const message = ref('')

// ✅ Bon : reactive pour les objets
const user = reactive({
  name: 'Alice',
  preferences: {}
})

// ✅ Bon : computed pour les valeurs dérivées
const displayName = computed(() => {
  return user.name.toUpperCase()
})

// ✅ Bon : shallowRef pour les gros objets non-nested
const largeDataset = shallowRef([/* milliers d'items */])

// ✅ Bon : markRaw pour les objets non-réactifs
const thirdPartyLibrary = markRaw(new SomeLibrary())

// ❌ Éviter : ref pour les gros objets
// const bigObject = ref({ /* complex nested object */ })

// ❌ Éviter : reactive pour les primitives  
// const count = reactive(0) // ❌
</script>
```

#### Gestion des erreurs

```vue
<script setup>
import { ref } from 'vue'

const data = ref(null)
const error = ref(null)
const loading = ref(false)

const fetchData = async () => {
  try {
    loading.value = true
    error.value = null
    
    const response = await fetch('/api/data')
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}: ${response.statusText}`)
    }
    
    data.value = await response.json()
  } catch (err) {
    console.error('Erreur lors du chargement:', err)
    error.value = err.message || 'Erreur inconnue'
  } finally {
    loading.value = false
  }
}
</script>
```

#### Accessibilité et UX

```vue
<template>
  <!-- ✅ Bon : attributs d'accessibilité -->
  <button 
    :disabled="isLoading"
    :aria-label="buttonLabel"
    @click="handleClick"
  >
    <span v-if="isLoading" aria-hidden="true">⏳</span>
    {{ isLoading ? 'Chargement...' : 'Valider' }}
  </button>
  
  <!-- ✅ Bon : feedback utilisateur -->
  <div v-if="error" role="alert" class="error-message">
    {{ error }}
  </div>
  
  <!-- ✅ Bon : états de chargement -->
  <div v-if="loading" class="loading-spinner" aria-label="Chargement en cours">
    <!-- Spinner -->
  </div>
</template>
```

## 📝 Travaux pratiques

### Objectif
Créer une application de gestion de tâches simple pour réviser les concepts Vue 3 de base.

### Étape 1 : Initialisation du projet

**Créer un nouveau projet Vue 3 :**
```bash
# Créer le projet avec l'outil officiel
npm create vue@latest task-manager

# Naviguer dans le dossier
cd task-manager

# Installer les dépendances
npm install

# Lancer le serveur de développement
npm run dev
```

**Configuration recommandée lors de la création :**
- ✅ TypeScript → Non (pour cette séance)
- ✅ JSX → Non  
- ✅ Vue Router → Non (pour cette séance)
- ✅ Pinia → Non (vu plus tard)
- ✅ Vitest → Non
- ✅ Playwright → Non
- ✅ ESLint → Oui


### Étape  : Test et amélioration

**Fonctionnalités à tester :**
1. Ajout de nouvelles tâches
2. Validation du formulaire (champs requis)
3. Marquage des tâches comme terminées  
4. Suppression des tâches
5. Filtrage par statut
6. Compteurs dynamiques

**Améliorations possibles :**
- Ajouter une date de création
- Permettre l'édition des tâches
- Sauvegarder dans le localStorage
- Ajouter des animations CSS

