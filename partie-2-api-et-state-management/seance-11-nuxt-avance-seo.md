# Séance 11 : Nuxt Avancé, SEO & Optimisations

## 🎯 Objectifs de la séance

* Maîtriser les balises méta dynamiques et le référencement naturel (**SEO**) avec `useHead()` et `useSeoMeta()`.
* Mettre en place un **Sitemap XML** et un fichier `robots.txt` dynamiques via Nitro.
* Optimiser les performances : lazy loading de composants, code splitting et gestion des images avec `@nuxt/image`.
* Comprendre les stratégies de mise en cache et de revalidation (**ISR / SWR**).

---

## 🔍 1. Maîtriser le SEO avec `useSeoMeta`

`useSeoMeta()` offre une autocomplétion TypeScript complète pour l'ensemble des balises standard, Open Graph (Facebook/LinkedIn) et Twitter Cards.

```vue
<!-- app/pages/tasks/[id].vue -->
<script setup lang="ts">
const route = useRoute()
const { data: task } = await useFetch(`/api/tasks/${route.params.id}`)

useSeoMeta({
  title: () => `${task.value?.title ?? 'Tâche'} — DevFlow`,
  ogTitle: () => task.value?.title ?? 'Détail de la tâche',
  description: () => task.value?.description ?? 'Suivi de tâche interne DevSquad.',
  ogDescription: () => task.value?.description ?? 'Détail de la tâche.',
  ogImage: '/images/og-devflow-preview.png',
  twitterCard: 'summary_large_image'
})
</script>
```

---

## ⚡ 2. Optimisation des Performances

### A. Lazy Loading des Composants Lourds

Nuxt préfixe automatiquement tous les composants d'un préfixe `Lazy` pour les charger uniquement lorsqu'ils sont affichés :

```vue
<template>
  <!-- Le composant StatsChart ne sera téléchargé par le navigateur que si showStats est vrai ! -->
  <LazyStatsChart v-if="showStats" :data="metrics" />
</template>
```

### B. Optimisation des Images (`@nuxt/image`)

```vue
<template>
  <!-- Génère automatiquement du WebP/AVIF avec redimensionnement et lazy loading natif -->
  <NuxtImg
    src="/avatars/alex.jpg"
    width="80"
    height="80"
    format="webp"
    loading="lazy"
    alt="Alexandre Dubois"
  />
</template>
```

---

## 🛠️ 3. Travaux Pratiques

1. Ajouter `useSeoMeta` dynamique sur toutes les pages de `DevFlow`.
2. Générer une route serveur `server/api/sitemap.xml.ts` qui liste dynamiquement toutes les tâches publiques.
3. Remplacer les composants de modale par des versions `LazyAppModal` pour réduire le bundle initial.
