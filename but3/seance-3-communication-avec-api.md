# Séance 3 – Communication avec API

## 🎯 Objectifs
- Appeler une API externe.
- Gérer erreurs, loaders et services dédiés.

## 📖 Partie théorique

### Architecture client/serveur

- Vue/Nuxt côté client, API côté serveur.
- Communication via HTTP (REST, JSON, voire GraphQL).

### Fetch API vs Axios

#### Fetch
- `fetch` : natif, simple, mais moins de fonctionnalités.

**Exemple :**

```javascript
fetch('http://localhost:3001/tasks')
  .then(response => response.json())
  .then(data => {
    console.log(data);
  });
```

#### Axios

- `axios` : plus complet, gestion des interceptors, requêtes simplifiées.

**Exemple :**

```javascript
import axios from 'axios';

axios.get('http://localhost:3001/tasks')
  .then(response => {
    console.log(response.data);
  });
```

### Gestion des erreurs

- Utilisation de `try/catch` pour capturer les erreurs réseau ou API.

**Exemple avec Fetch :**

```javascript
async function fetchTasks() {
  try {
    const response = await fetch('http://localhost:3001/tasks');
    if (!response.ok) {
      throw new Error('Erreur lors de la récupération des tâches');
    }
    const data = await response.json();
    console.log(data);
  } catch (error) {
    // Affichage d’un message d’erreur à l’utilisateur
    alert('Une erreur est survenue : ' + error.message);
  }
}
```

Dans cet exemple, si la requête échoue (problème réseau, serveur indisponible, etc.), le bloc `catch` permet d’afficher un message d’erreur à l’utilisateur.

**Exemple simple en Vue.js :**

```javascript
<template>
  <div>
    <button @click="fetchTasks">Charger les tâches</button>
    <div v-if="errorMessage" class="error">
      {{ errorMessage }}
    </div>
    <ul v-if="tasks.length">
      <li v-for="task in tasks" :key="task.id">{{ task.title }}</li>
    </ul>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const tasks = ref([])
const errorMessage = ref('')

async function fetchTasks() {
  errorMessage.value = ''
  try {
    const response = await fetch('http://localhost:3001/tasks')
    if (!response.ok) {
      throw new Error('Erreur lors de la récupération des tâches')
    }
    tasks.value = await response.json()
  } catch (error) {
    errorMessage.value = 'Une erreur est survenue : ' + error.message
  }
}
</script>

<style>
.error {
  color: red;
  margin: 1em 0;
}
</style>
```

Cet exemple affiche un message d’erreur à l’utilisateur si la récupération des tâches échoue.

### Loaders

Pour afficher un loader pendant l'appel à l'API, il suffit d'ajouter une variable d'état `isLoading` et de l'utiliser dans le template :

```javascript
<template>
  <div>
    <button @click="fetchTasks">Charger les tâches</button>
    <div v-if="isLoading" class="loader">Chargement...</div>
    <div v-if="errorMessage" class="error">
      {{ errorMessage }}
    </div>
    <ul v-if="tasks.length && !isLoading">
      <li v-for="task in tasks" :key="task.id">{{ task.title }}</li>
    </ul>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const tasks = ref([])
const errorMessage = ref('')
const isLoading = ref(false)

async function fetchTasks() {
  errorMessage.value = ''
  isLoading.value = true
  try {
    const response = await fetch('http://localhost:3001/tasks')
    if (!response.ok) {
      throw new Error('Erreur lors de la récupération des tâches')
    }
    tasks.value = await response.json()
  } catch (error) {
    errorMessage.value = 'Une erreur est survenue : ' + error.message
  } finally {
    isLoading.value = false
  }
}
</script>

<style>
.loader {
  color: #007bff;
  font-weight: bold;
  margin: 1em 0;
}
.error {
  color: red;
  margin: 1em 0;
}
</style>
```

Ici, le loader s'affiche tant que la requête est en cours (`isLoading` à `true`), puis disparaît dès que la réponse est reçue ou qu'une erreur survient.

### Services dédiés

- Création d'un service API pour centraliser les appels HTTP.

## 📝 Travaux pratiques

### Étape 1 : Installation de JSON Server

JSON Server permet de créer une API REST complète à partir d'un simple fichier JSON.

**Installation :**
```bash
# Installation globale
npm install -g json-server

# Ou installation locale dans le projet
npm install --save-dev json-server
```

### Étape 2 : Création du fichier de données

**Créer `db.json` à la racine du projet :**
```json
{
  "tasks": [
    {
      "id": 1,
      "title": "Apprendre Vue 3",
      "description": "Maîtriser la Composition API et les nouveautés",
      "priority": "High",
      "completed": false,
      "createdAt": "2024-09-01T10:00:00Z",
      "dueDate": "2024-09-30T23:59:59Z"
    },
    {
      "id": 2,
      "title": "Créer un projet",
      "description": "Développer une application de gestion de tâches",
      "priority": "Medium",
      "completed": true,
      "createdAt": "2024-09-15T14:30:00Z",
      "dueDate": "2024-10-15T23:59:59Z"
    },
    {
      "id": 3,
      "title": "Déployer l'application",
      "description": "Mettre en ligne sur Netlify ou Vercel",
      "priority": "Low",
      "completed": false,
      "createdAt": "2024-09-20T09:15:00Z",
      "dueDate": "2024-11-01T23:59:59Z"
    }
  ],
  "users": [
    {
      "id": 1,
      "name": "Alice Dupont",
      "email": "alice@example.com",
      "role": "admin"
    },
    {
      "id": 2,
      "name": "Bob Martin",
      "email": "bob@example.com",
      "role": "user"
    }
  ]
}
```

### Étape 3 : Configuration et démarrage de JSON Server

**Ajouter les scripts dans `package.json` :**
```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "api": "json-server --watch db.json --port 3001",
    "dev:full": "concurrently \"npm run dev\" \"npm run api\""
  }
}
```

**Pour exécuter simultanément Vue et l'API :**
```bash
# Installer concurrently pour exécuter plusieurs commandes
npm install --save-dev concurrently

# Lancer Vue et l'API en même temps
npm run dev:full

# Ou séparément :
# Terminal 1 : Vue
npm run dev

# Terminal 2 : API JSON Server
npm run api
```

### Étape 4 : URLs disponibles

Une fois JSON Server démarré sur le port 3001, les endpoints suivants sont disponibles :

**Endpoints automatiques :**
```
GET    /tasks           # Récupérer toutes les tâches
GET    /tasks/1         # Récupérer la tâche avec l'ID 1
POST   /tasks           # Créer une nouvelle tâche
PUT    /tasks/1         # Mettre à jour la tâche avec l'ID 1
PATCH  /tasks/1         # Mise à jour partielle de la tâche avec l'ID 1
DELETE /tasks/1         # Supprimer la tâche avec l'ID 1

GET    /users           # Récupérer tous les utilisateurs
GET    /users/1         # Récupérer l'utilisateur avec l'ID 1
```

**Paramètres de requête supportés :**
```
GET /tasks?completed=true           # Filtrer par statut
GET /tasks?priority=High            # Filtrer par priorité
GET /tasks?_sort=createdAt&_order=desc  # Trier par date (desc)
GET /tasks?_limit=10&_page=2        # Pagination
GET /tasks?q=Vue                    # Recherche textuelle
```

### Étape 5 : Appels API dans Vue

- Utilisez ces API pour lier vos tâches dans votre application Vue.
- Gérez les états de chargement et d'erreur avec des loaders et des messages.
- Créez des services dédiés pour organiser vos appels API.