# Séance 7 – Nuxt & API

## 🎯 Objectifs
- Consommer des données dans Nuxt.
- Gérer l’authentification avec middleware.

## 📖 Partie théorique

### Data fetching

- Utilisation de `useFetch` et `useAsyncData` pour récupérer les données.
- Gestion des états de chargement et d’erreur.

### Middleware de route

- Protection des routes avec des middlewares (authentification).
- Redirection automatique si non authentifié.

### Création d’API côté serveur

- Utilisation du dossier `server/api` pour créer des endpoints personnalisés.

## 💻 Exemples
```js
const { data, pending, error } = await useFetch('/api/tasks')
```
```js
export default defineNuxtRouteMiddleware((to, from) => {
  if (!isAuthenticated()) return navigateTo('/login')
})
```

## 📝 Travaux pratiques
- Créer page `/login`.
- Stocker token JWT dans Pinia/localStorage.
- Protéger `/dashboard` avec middleware.

