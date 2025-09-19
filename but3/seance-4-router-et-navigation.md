# Séance 4 – Router et navigation

## 🎯 Objectifs
- Maîtriser navigation avancée avec Vue Router.
- Gérer routes dynamiques et sécurisées.

## 📖 Partie théorique

### Routes dynamiques et imbriquées

- Utilisation de paramètres (`/task/:id`).
- Nested routes pour organiser l’UI.

### Guards

- Fonctions de contrôle d’accès (`beforeEach`, meta).
- Protection des routes sensibles (authentification).

### Lazy loading

- Chargement des composants de route à la demande.
- Optimisation des performances.

### Gestion des erreurs

- Pages 404 personnalisées.
- Redirections automatiques selon les cas d’usage.

## 💻 Exemples
```js
const routes = [
  { path: '/', component: Home },
  { path: '/task/:id', component: TaskDetail }
]
```
```js
router.beforeEach((to, from, next) => {
  if (to.meta.requiresAuth && !user.isLoggedIn) next('/login')
  else next()
})
```

## 📝 Travaux pratiques
- Ajouter route `/task/:id`.
- Mettre en place un guard d’accès (auth simulée).
- Créer une page 404 personnalisée.

