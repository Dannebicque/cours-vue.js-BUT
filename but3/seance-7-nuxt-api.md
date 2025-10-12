# Séance 7 – Nuxt & API

## 🎯 Objectifs
- Consommer des données dans Nuxt.
- Gérer l’authentification avec middleware.

## 📖 Partie théorique

### Data fetching

Source : https://nuxt.com/docs/4.x/getting-started/data-fetching

Nuxt propose plusieurs helpers pour récupérer des données de façon déclarative et compatible SSR/SSG : `useFetch` et `useAsyncData` sont les plus courants. Ils sont auto‑importés et s'intègrent au cycle de rendu (server ou client) de Nuxt.

Principes clés :

- Les appels faits pendant la phase serveur sont exécutés avant l'envoi du HTML (meilleur SEO et fast first paint).
- Les helpers gèrent automatiquement l'état `pending` / `error` et la réhydratation côté client.
- Les résultats peuvent être mis en cache et revalidés (revalidation) selon la configuration.

#### useFetch

https://nuxt.com/docs/4.x/getting-started/data-fetching#usefetch

Exemple :

```javascript
// 1) useFetch : wrapper autour de fetch/$fetch, simple et pratique
const { data, pending, error } = await useFetch('/api/tasks')

// data.value contient le JSON ; pending.value indique le chargement
```

#### useAsyncData

https://nuxt.com/docs/4.x/getting-started/data-fetching#useasyncdata

Exemple : 

```javascript
// 2) useAsyncData : utile pour nommer/mettre en cache explicitement
const { data: tasks } = await useAsyncData('tasks', () => $fetch('/api/tasks'))

// useAsyncData permet de définir une clé, un transform, et des options de server/lazy
```

#### Quand utiliser l'un ou l'autre

- `useFetch` : très pratique pour des appels simples dans le setup d'un composant/page.
- `useAsyncData` : préférable si vous voulez contrôler la clé de cache, le transform ou la revalidation.

Autres utilitaires liés :

- `useState()` : stocke un état réactif côté serveur/client (idéal pour partager une valeur entre pages sans Pinia). Exemple :

```javascript
const count = useState('count', () => 0) // persisté dans l'instance Nuxt (par requête/utilisateur)
```

- `useNuxtApp().$fetch` ou `$fetch` : utilitaires pour faire des requêtes internes ou externes.

#### Server API (endpoints)

Nuxt offre un dossier `server/api/` pour créer des endpoints server‑side. Exemple :

```javascript
// server/api/tasks.get.js
export default defineEventHandler(async (event) => {
  // logique serveur (lecture DB, appel externe, auth)
  return [{ id: 1, title: 'Tâche' }]
})
```

Puis côté client :

```javascript
const { data } = await useFetch('/api/tasks')
```

Bonnes pratiques et pièges courants :

- Préférer `$fetch`/`useFetch` pour les appels internes aux endpoints Nuxt (optimisations internes).
- Faire attention aux appels indésirables côté client (watch, effets) — utiliser `lazy: true` si nécessaire.
- Gérer les erreurs et l'état de chargement pour afficher des loaders ou des messages clairs.
- Pour des pages statiques (SSG), vérifier si le contenu doit être revalidé (ISR-like) et configurer la revalidation via `useAsyncData` ou des hooks de build.

### Middleware de route

source : https://nuxt.com/docs/4.x/getting-started/routing#route-middleware

- Nuxt permet de définir des middlewares pour protéger des routes (authentification, autorisation).

Ecrire le middleware dans `middleware/auth.js` (par exemple) :

```javascript
export default defineNuxtRouteMiddleware((to, from) => {
  const isAuthenticated = useAuthStore().isAuthenticated
  if (!isAuthenticated && to.path !== '/login') {
    return navigateTo('/login')
  }
})
```

- Appliquer le middleware globalement dans `nuxt.config.ts` ou localement dans une page avec `definePageMeta`.
  
```javascript
export default definePageMeta({
  middleware: 'auth'
})
```

Il est aussi possible d'utiliser des modules (https://nuxt.com/modules?category=Security&sortBy=stars) pour gérer l'authentification plus facilement ou de manière plus complète, en intégrant des providers OAuth, JWT, etc.


## 📝 Travaux pratiques

- Reconnectér les appels API de l’application Vue 3 précédente en Nuxt.
- Créer page `/login`.
- Stocker token JWT dans Pinia/localStorage.
- Protéger l'ajout d'une tâche avec middleware.

