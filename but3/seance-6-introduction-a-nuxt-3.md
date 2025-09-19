# Séance 6 – Introduction à Nuxt 3

## 🎯 Objectifs
- Découvrir Nuxt et ses apports (SSR, SSG, SEO).
- Migrer une app Vue vers Nuxt.

## 📖 Partie théorique

### Vue vs Nuxt

- Vue : client-side rendering (CSR) par défaut.
- Nuxt : SSR (Server-Side Rendering), SSG (Static Site Generation), structure professionnelle.

### Structure Nuxt

- Dossiers clés :
	- `pages/` : routage automatique
	- `layouts/` : templates globaux
	- `plugins/` : extensions et outils
	- `server/` : API côté serveur

### Différence CSR/SSR/SSG

- CSR : rendu côté client, rapide mais moins SEO.
- SSR : rendu côté serveur, meilleur SEO et performance.
- SSG : génération statique, ultra rapide et sécurisé.

## 💻 Exemples
```js
// Nuxt auto routing
/pages/index.vue  -> /
/pages/about.vue  -> /about
```

## 📝 Travaux pratiques
- Créer un projet Nuxt (`npx nuxi init`).
- Déplacer les composants Vue dans Nuxt.
- Créer un layout global avec header/footer.

