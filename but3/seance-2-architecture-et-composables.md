# Séance 2 – Architecture et composables

## 🎯 Objectifs
- Comprendre la structuration d’un projet Vue.
- Découvrir la factorisation de logique avec les composables.

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

```vue
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

### Composables

- Fonctions réutilisables exploitant la réactivité de Vue.
- Permettent de factoriser la logique métier et d’éviter la duplication de code.

### Design patterns

- Smart vs Dumb components :
  - Smart : gèrent la logique, les données et l’état
  - Dumb : affichent uniquement les données reçues
- Container vs Presentational components :
  - Container : connectés au store, gèrent la logique
  - Presentational : affichent l’UI, reçoivent les props

## 💻 Exemples
```js
export function useCounter() {
  const count = ref(0)
  const inc = () => count.value++
  return { count, inc }
}
```
```vue
<!-- Smart: Board.vue -->
<TaskCard v-for="t in tasks" :task="t" />
```

## 📝 Travaux pratiques
- Refactor TP précédent avec un composable `useTasks`.
- Ajouter `useCounter` pour pratiquer.

