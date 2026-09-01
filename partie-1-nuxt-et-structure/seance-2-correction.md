---
hidden: true
---

# Séance 2 (correction) : Initialisation et Setup Nuxt 4

Ce document présente la correction détaillée de l'atelier pratique de la **Séance 2**.

***

## 🛠️ Étape 1 : Initialisation du projet

```bash
# Initialiser le projet Nuxt 4
npx nuxi@latest init devflow

# Se positionner dans le dossier
cd devflow

# Installer les dépendances
npm install
```

***

## 🛠️ Étape 2 : Configuration `nuxt.config.ts`

Voici le fichier `nuxt.config.ts` complet et commenté :

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  // Active l'arborescence et les fonctionnalités modernes de Nuxt 4
  future: {
    compatibilityVersion: 4,
  },

  // Configuration TypeScript stricte
  typescript: {
    strict: true,
    typeCheck: false
  },

  // Configuration des balises méta globales
  app: {
    head: {
      title: 'DevFlow — Tracker de tâches d\'équipe',
      htmlAttrs: {
        lang: 'fr'
      },
      meta: [
        { charset: 'utf-8' },
        { name: 'viewport', content: 'width=device-width, initial-scale=1' },
        { name: 'description', content: 'Application professionnelle de gestion et suivi de tâches de l\'agence DevSquad.' }
      ],
      link: [
        { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }
      ]
    }
  },

  // DevTools activés pour le développement local
  devtools: { enabled: true }
})
```

***

## 🛠️ Étape 3 : Composant racine `app/app.vue`

Créez le dossier `app/` et placez-y le fichier `app/app.vue` :

```vue
<!-- app/app.vue -->
<template>
  <div class="app-root">
    <!-- NuxtLayout gère l'injection du layout actif (layouts/default.vue par défaut) -->
    <NuxtLayout>
      <!-- NuxtPage injecte le composant correspondant à l'URL courante -->
      <NuxtPage />
    </NuxtLayout>
  </div>
</template>

<style>
/* Variables CSS globales et tokens de design */
:root {
  --font-family: 'Inter', system-ui, -apple-system, sans-serif;
  --bg-primary: #0f172a;
  --bg-surface: #1e293b;
  --text-primary: #f8fafc;
  --text-secondary: #94a3b8;
  --color-brand: #38bdf8;
  --border-color: #334155;
}

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: var(--font-family);
  background-color: var(--bg-primary);
  color: var(--text-primary);
  min-height: 100vh;
}
</style>
```

***

## 🛠️ Étape 4 : Lancement et vérification

```bash
npm run dev
```

Ouvrez votre navigateur sur `http://localhost:3000`. Utilisez le raccourci `Shift + Alt + D` pour explorer les **DevTools Nuxt** (arborescence des composants, routing, state, performances).
