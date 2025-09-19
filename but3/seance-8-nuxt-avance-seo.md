# Séance 8 – Nuxt avancé & SEO

## 🎯 Objectifs
- Approfondir middleware et SEO.
- Optimiser application Nuxt.

## 📖 Partie théorique

### Middleware global vs local

- Middleware global : s’applique à toutes les routes.
- Middleware local : spécifique à certaines pages.

### SEO avec useHead

- Utilisation de `useHead` pour gérer le titre et les balises meta.
- Amélioration du référencement naturel.

### Optimisation

- Lazy loading des composants pour améliorer les performances.
- Code splitting pour réduire la taille des bundles.

## 💻 Exemples
```js
useHead({
  title: 'Profil',
  meta: [
    { name: 'description', content: 'Page profil utilisateur' }
  ]
})
```

## 📝 Travaux pratiques
- Créer une page profil SSR (`useAsyncData`).
- Ajouter SEO avec `useHead`.

