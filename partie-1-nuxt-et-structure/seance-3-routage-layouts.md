# Séance 3 : Routage, Layouts & Navigation dans Nuxt 4

## 🎯 Objectifs de la séance

* Maîtriser le système de **routage automatique** de Nuxt 4 basé sur l'arborescence du dossier `app/pages/`.
* Naviguer efficacement avec le composant natif **`<NuxtLink>`**, le préchargement automatique et les styles actifs.
* Manipuler les paramètres de route avec `useRoute()`, `useRouter()` et effectuer des redirections programmatiques avec `navigateTo()`.
* Configurer le **référencement naturel (SEO)** et les métadonnées de page avec **`useSeoMeta()`** et **`useHead()`**.
* Concevoir des **Layouts** de mise en page modulaires (`layouts/default.vue`, `layouts/auth.vue`).
* Gérer les erreurs et concevoir une page 404/500 sur-mesure (`app/error.vue`).
* **Atelier Fil rouge DevFlow (Étape 1)** : Mettre en place l'ossature de navigation complète et le SEO de l'application.

---

## 🗺️ 1. Le Routage par Fichiers dans Nuxt 4

Dans Nuxt 4, chaque fichier `.vue` placé dans le répertoire `app/pages/` génère automatiquement une route correspondante. Vous n'avez plus besoin d'écrire de fichier `router.js` manuel.

### Conventions de correspondance des fichiers

| Structure dans `app/pages/` | URL générée | Type de route |
| :--- | :--- | :--- |
| `pages/index.vue` | `/` | Page d'accueil |
| `pages/login.vue` | `/login` | Page statique |
| `pages/tasks/index.vue` | `/tasks` | Liste principale |
| `pages/tasks/[id].vue` | `/tasks/:id` *(ex: `/tasks/TASK-42`)* | Route dynamique avec paramètre |
| `pages/tasks/[id]/edit.vue` | `/tasks/:id/edit` | Sous-route dynamique |
| `pages/docs/[...slug].vue` | `/docs/*` *(ex: `/docs/guide/start`)* | Route catch-all (fourre-tout) |

{% hint style="info" %}
**Règle Nuxt :** Si votre application utilise le dossier `pages/`, assurez-vous que `app/app.vue` contient le composant `<NuxtPage />` pour permettre l'affichage des vues.
{% endhint %}

---

## 🧭 2. Navigation : `<NuxtLink>` vs `<a>`

Le composant `<NuxtLink>` remplace la balise `<a>` standard pour la navigation interne.

### Pourquoi utiliser `<NuxtLink>` ?
1. **Navigation SPA sans rechargement** : Le navigateur ne recharge pas toute la page HTML, seule la vue change.
2. **Smart Prefetching** : Dès qu'un lien `<NuxtLink>` entre dans le champ de vision de l'utilisateur, Nuxt précharge en arrière-plan le code et les données de la page cible pour une navigation instantanée.
3. **Gestion automatique des classes actives** : Ajoute automatiquement les classes CSS `router-link-active` et `router-link-exact-active`.

```vue
<!-- components/NavigationMenu.vue -->
<template>
  <nav class="nav-links">
    <NuxtLink to="/tasks" class="nav-item">
      📋 Mes Tâches
    </NuxtLink>
    <NuxtLink to="/team" class="nav-item">
      👥 Équipe
    </NuxtLink>
    <!-- Lien externe classique géré automatiquement sans préchargement inutile -->
    <NuxtLink to="https://github.com/my-agency/devflow" target="_blank" class="nav-item">
      🐙 GitHub
    </NuxtLink>
  </nav>
</template>

<style scoped>
.nav-item.router-link-exact-active {
  background-color: #38bdf8;
  color: #0f172a;
  font-weight: 700;
}
</style>
```

---

## 🎛️ 3. Navigation Programmatique : `useRoute` & `navigateTo`

Pour naviguer en réponse à une action (soumission de formulaire, sélection dans une liste, redirection suite à une erreur) :

```typescript
// Redirection programmatique recommandée avec navigateTo()
await navigateTo('/tasks')
```

Pour lire les paramètres d'une route dynamique :

```typescript
const route = useRoute()
// Récupération du paramètre d'URL (ex: /tasks/TASK-101 -> taskId = 'TASK-101')
const taskId = computed(() => route.params.id as string)
```

---

## 🔍 4. Référencement Naturel & Métadonnées SEO (`useSeoMeta` & `useHead`)

L'un des avantages majeurs de Nuxt (via le rendu SSR) est de pouvoir générer des pages dont les balises `<head>` sont immédiatement lisibles par les moteurs de recherche (Google, Bing) et les réseaux sociaux (aperçus de liens Open Graph sur Discord, LinkedIn, Slack).

### A. Déclaration déclarative avec `useSeoMeta()`

`useSeoMeta()` est la méthode recommandée pour déclarer l'ensemble des balises méta standards, Open Graph et Twitter Cards avec un typage TypeScript strict :

```vue
<!-- app/pages/tasks/index.vue -->
<script setup lang="ts">
useSeoMeta({
  title: 'Tableau de bord des tâches — DevFlow',
  description: 'Suivi en temps réel des développements de l\'agence DevSquad.',
  ogTitle: 'Tableau de bord — DevFlow',
  ogDescription: 'Gérez et priorisez les tâches de votre équipe.',
  ogImage: '/images/og-devflow-dashboard.png',
  twitterCard: 'summary_large_image'
})
</script>
```

### B. Métadonnées dynamiques (liées aux paramètres d'URL ou aux données)

Lorsque le titre ou la description dépendent d'une donnée dynamique (ex: le titre d'une tâche chargée depuis l'URL), passez des fonctions fléchées (*getters*) à `useSeoMeta()` :

```vue
<!-- app/pages/tasks/[id].vue -->
<script setup lang="ts">
const route = useRoute()
const taskId = computed(() => route.params.id as string)

// Les getters permettent à Nuxt de recalculer le titre dès que taskId change
useSeoMeta({
  title: () => `Tâche ${taskId.value} — DevFlow`,
  ogTitle: () => `Détail de la tâche ${taskId.value}`,
  description: () => `Consultez les détails, assignations et commentaires de la tâche ${taskId.value}.`,
  ogImage: '/images/og-task-card.png'
})
</script>
```

### C. Gestion avancée du `<head>` avec `useHead()`

Pour injecter des scripts externes, des polices web ou des feuilles de styles spécifiques à une page :

```typescript
useHead({
  htmlAttrs: {
    lang: 'fr'
  },
  link: [
    { rel: 'canonical', href: 'https://devflow.devsquad.pro/tasks' }
  ]
})
```

---

## 📐 5. Les Layouts (Gabarits de Mise en Page)

Les Layouts situés dans `app/layouts/` permettent de factoriser la structure récurrente des pages (Barre de navigation, Header, Footer, Sidebar).

Pour appliquer un layout non-défaut à une page :

```vue
<!-- app/pages/login.vue -->
<script setup lang="ts">
definePageMeta({
  layout: 'auth'
})
</script>
```

---

## 🚫 6. Gestion des Erreurs : `app/error.vue`

Nuxt intercepte automatiquement les erreurs non gérées et les 404 pour afficher `app/error.vue`. La fonction `clearError({ redirect: '/...' })` permet de réinitialiser l'état d'erreur et de rediriger l'utilisateur proprement.

---

## 🛠️ 7. Atelier Pratique : Fil Rouge "DevFlow" (Étape 1)

### Votre mission chez DevSquad :

1. **Créer les deux layouts** dans `app/layouts/` :
   * `default.vue` : Sidebar avec logo DevFlow, liens `<NuxtLink>` vers `/tasks` et `/team`, encart utilisateur connecté, zone principale `<slot />`.
   * `auth.vue` : Mise en page épurée, centrée sur fond dégradé sombre avec une boîte de dialogue `<slot />`.

2. **Créer les pages principales avec leur SEO** dans `app/pages/` :
   * `app/pages/index.vue` : Page d'accueil avec redirection automatique vers `/tasks`.
   * `app/pages/login.vue` : Formulaire de connexion utilisant le layout `auth` via `definePageMeta({ layout: 'auth' })` et un `useSeoMeta` adapté.
   * `app/pages/tasks/index.vue` : Page principale du tracker avec métadonnées SEO complètes.
   * `app/pages/tasks/[id].vue` : Page de détail d'une tâche affichant l'identifiant dynamique et un titre SEO calculé dynamiquement.
   * `app/pages/team.vue` : Page présentant la liste statique des membres de l'équipe de l'agence.

3. **Créer la page d'erreur personnalisée** dans `app/error.vue` (gestion des codes 404 et 500 avec bouton de retour à l'accueil via `clearError`).

{% hint style="tip" %}
La solution détaillée et commentée de cet atelier est disponible dans la page [Séance 3 (correction)](seance-3-correction.md).
{% endhint %}

---

## 🎯 Auto-évaluation & Checklist

* [ ] Je sais comment créer une route dynamique avec la syntaxe `[param].vue`.
* [ ] Je sais configurer des métadonnées SEO statiques et dynamiques avec `useSeoMeta()`.
* [ ] Je sais basculer d'un layout à un autre avec `definePageMeta({ layout: '...' })`.
* [ ] J'utilise `<NuxtLink>` pour toutes mes navigations internes.
* [ ] J'ai implémenté `app/error.vue` avec la fonction `clearError()`.
* [ ] Le squelette complet de navigation de DevFlow est opérationnel.
