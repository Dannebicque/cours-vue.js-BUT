# Séance 4 – Router et navigation

## 🎯 Objectifs
- Maîtriser navigation avancée avec Vue Router.
- Gérer routes dynamiques et sécurisées.

## 📖 Partie théorique

### Routes dynamiques et imbriquées

#### Paramètres de route

Les **routes dynamiques** capturent des valeurs variables dans l'URL (`:id`, `:slug`).

```javascript
// Configuration des routes
const routes = [
  { path: '/task/:id', component: TaskDetail, props: true },
  { path: '/user/:userId/task/:taskId', component: TaskDetail },
  { path: '/profile/:userId?', component: UserProfile }, // Optionnel
]
```

**Accès aux paramètres :**
```javascript
// Dans le composant
<script setup>
import { useRoute } from 'vue-router'
const route = useRoute()
const taskId = route.params.id
</script>
```

#### Routes imbriquées

Organisation hiérarchique avec composants parent/enfant.

```javascript
// Structure imbriquée
const routes = [
  {
    path: '/dashboard',
    component: DashboardLayout,
    children: [
      { path: '', component: DashboardHome },        // /dashboard
      { path: 'tasks', component: TaskList },        // /dashboard/tasks
      { path: 'tasks/:id', component: TaskDetail },  // /dashboard/tasks/123
    ]
  }
]
```

```javascript
<!-- DashboardLayout.vue -->
<template>
  <div class="dashboard">
    <nav>
      <router-link to="/dashboard/tasks">Tâches</router-link>
    </nav>
    <main>
      <router-view></router-view> <!-- Contenu des routes enfants -->
    </main>
  </div>
</template>
```

**Navigation programmatique :**

```javascript
const router = useRouter()

// Différentes façons de naviguer
router.push('/tasks/123')
router.push({ name: 'TaskDetail', params: { id: 123 } })
router.push({ path: '/tasks', query: { status: 'pending' } })
```

### Gestion des erreurs

Pages d'erreur et redirections pour une meilleure expérience utilisateur.

#### Page 404 personnalisée

```javascript
<!-- NotFound.vue -->
<template>
  <div class="error-page">
    <h1>404 - Page non trouvée</h1>
    <p>La page demandée n'existe pas.</p>
    <router-link to="/">Retour à l'accueil</router-link>
  </div>
</template>
```

#### Configuration des routes d'erreur

```javascript
const routes = [
  // Autres routes...
  
  // Redirections
  { path: '/old-url', redirect: '/new-url' },
  
  // Redirection conditionnelle
  { 
    path: '/user/:id', 
    redirect: to => `/profile/${to.params.id}` 
  },
  
  // Page 404 (toujours en dernier)
  { 
    path: '/:pathMatch(.*)*', 
    component: NotFound 
  }
]
```

#### Gestion des erreurs de navigation

```javascript
// Intercepter les erreurs de chargement
router.onError((error) => {
  if (error.message.includes('Loading chunk')) {
    // Erreur de réseau, recharger la page
    window.location.reload()
  }
})
```

#### Routes et meta

Il est possible d'ajouter des **métadonnées** aux routes pour diverses utilisations (authentification, titres, etc.).

```javascript
const routes = [
  {
    path: '/dashboard',
    component: DashboardLayout,
    meta: { requiresAuth: true, title: 'Tableau de bord' },
    children: [
      { path: 'analytics', component: AnalyticsPage, meta: { title: 'Analyse' } },
      { path: 'settings', component: SettingsPage, meta: { title: 'Paramètres' } }
    ]
  }
]
```

Usage dans les composants :

```javascript
<script setup>
import { useRoute } from 'vue-router'
const route = useRoute()
console.log(route.meta.title) // Titre de la page
</script>
```

Le meta peut aussi être utilisé pour des guards ou pour changer le titre de la page dynamiquement.


### Guards

- Fonctions de contrôle d’accès (`beforeEach`, meta).
- Protection des routes sensibles (authentification).

#### Concepts clés :

- **Global Guards** : s’appliquent à toutes les navigations.
- **Per-Route Guards** : spécifiques à certaines routes.
- **In-Component Guards** : définis dans les composants.


#### Guard global :

```javascript
router.beforeEach((to, from, next) => {
  if (to.meta.requiresAuth && !isAuthenticated()) {
    next('/login')
  } else {
    next()
  }
})
```

#### Guard par route :

```javascript
const routes = [
  {
    path: '/admin',
    component: AdminPanel,
    meta: { requiresAuth: true, role: 'admin' },
    beforeEnter: (to, from, next) => {
      // Vérification spécifique à cette route
      if (!user.isAdmin) {
        next('/forbidden')
      } else {
        next()
      }
    }
  }
]
```

#### Guard dans composant :

```javascript
<script setup>
import { onBeforeRouteLeave } from 'vue-router'

const hasUnsavedChanges = ref(false)

// Confirmation avant de quitter
onBeforeRouteLeave((to, from, next) => {
  if (hasUnsavedChanges.value) {
    const leave = confirm('Modifications non sauvées, continuer ?')
    next(leave)
  } else {
    next()
  }
})
</script>
```

#### Mise à jour du title de la page

```javascript
router.afterEach((to) => {
  if (to.meta.title) {
    document.title = to.meta.title
  }
})
```

### Lazy loading (Chargement différé)

Chargement des composants uniquement quand nécessaire pour optimiser les performances.

#### Import dynamique

```javascript
// ❌ Import classique - chargé immédiatement
import Home from '@/views/Home.vue'

// ✅ Import dynamique - chargé à la demande
const routes = [
  {
    path: '/home',
    component: () => import('@/views/Home.vue')
  }
]
```

#### Composant de chargement

```javascript
<!-- App.vue -->
<template>
  <Suspense>
    <router-view></router-view>
    <template #fallback>
      <div class="loading">Chargement...</div>
    </template>
  </Suspense>
</template>
```

## 📝 Travaux pratiques
- Ajouter route `/task/:id`.
- Mettre en place un guard d’accès (auth simulée).
- Créer une page 404 personnalisée.
