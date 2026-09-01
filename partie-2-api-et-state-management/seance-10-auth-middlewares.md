# Séance 10 : Middlewares de Route & Authentification

## 🎯 Objectifs de la séance

* Comprendre le fonctionnement des **Route Middlewares** dans Nuxt 4.
* Distinguer les middlewares **anonymes**, **nommés** (`middleware/`) et **globaux** (`.global.ts`).
* Mettre en place une protection de routes avec redirection sécurisée (`navigateTo()`).
* Intégrer un flux d'authentification JWT complet avec Pinia et Nuxt.
* **Fil rouge DevFlow** : Verrouiller l'accès au tableau de bord `/tasks` aux utilisateurs authentifiés.

---

## 🛡️ 1. Les Middlewares de Route dans Nuxt 4

Les middlewares de route s'exécutent avant chaque transition de navigation (côté serveur lors du premier chargement, puis côté client lors des navigations SPA).

```
Requête Navigation ──► [ Middleware Auth ] ──┬── Authentifié ──► Affiche la Page (/tasks)
                                             │
                                             └── Non Authentifié ─► Redirige vers /login
```

### Les 3 types de middlewares :
1. **Middlewares globaux** (`app/middleware/auth.global.ts`) : S'exécutent sur **toutes** les routes de l'application.
2. **Middlewares nommés** (`app/middleware/auth.ts`) : Assignés explicitement sur des pages choisies via `definePageMeta({ middleware: 'auth' })`.
3. **Middlewares anonymes / inline** : Définis directement au sein de `definePageMeta` dans le composant de page.

---

## 🔐 2. Exemple Complet : Middleware d'Authentification

### Étape A : Écriture du middleware (`app/middleware/auth.ts`)

```typescript
// app/middleware/auth.ts
import { useAuthStore } from '~/stores/authStore'

export default defineNuxtRouteMiddleware((to, from) => {
  const authStore = useAuthStore()

  // Si l'utilisateur n'est pas connecté et tente d'accéder à une page protégée
  if (!authStore.isAuthenticated) {
    // Redirection vers le login avec mémorisation de la page de destination
    return navigateTo({
      path: '/login',
      query: { redirect: to.fullPath }
    })
  }
})
```

### Étape B : Protection de la page des tâches

```vue
<!-- app/pages/tasks/index.vue -->
<script setup lang="ts">
// Active le middleware auth sur cette page
definePageMeta({
  middleware: 'auth'
})
</script>
```

---

## 🛠️ 3. Travaux Pratiques

1. Créer le middleware `app/middleware/auth.ts`.
2. Protéger l'ensemble des pages `/tasks`, `/tasks/[id]` et `/team`.
3. Permettre la redirection automatique vers la page précédemment demandée après une connexion réussie.
4. Ajouter un bouton "Déconnexion" dans le layout `default` qui vide le store Pinia et redirige vers `/login`.
