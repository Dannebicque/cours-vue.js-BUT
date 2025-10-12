# Séance 6 – Introduction à Nuxt 3

## 🎯 Objectifs

- Découvrir Nuxt et ses apports (SSR, SSG, SEO).
- Migrer une app Vue vers Nuxt.

## Qu'est-ce que Nuxt.Js ?

Nuxt (https://nuxt.com/), vesion 4 aujourd'hui, est un framework construit au‑dessus de Vue 3 qui facilite la création d'applications universelles (fullstack) : sites statiques, applications server‑rendered (SSR) ou applications hybridées. Nuxt vise à fournir une structure et des conventions pour accélérer le développement (routing automatique, layouts, plugins, modules, import automatisés, ...). Sa runtime moderne repose sur Nitro (moteur serveur agnostique) et offre un excellent support pour le rendu côté serveur, le prerendering, le déploiement serverless et l'optimisation des performances.

Points clés de Nuxt 3 / 4 :

- Routing automatique via `pages/`.
- Layouts et slots globaux via `layouts/`.
- API server intégrée via `server/` (endpoints API côté serveur).
- Nitro : abstraction du serveur.
- Modules & plugins pour étendre les fonctionnalités (auth, i18n, analytics).
- Support SSR, SSG et ISR (hybride) natif.
- Intégration simple avec Vite, TypeScript et Pinia.

### Comparatif Nuxt vs Vue (grand lignes)

- Vue :
  - Framework UI (lib) pour construire des interfaces réactives côté client.
  - Idéal pour des applications SPA où l'on contrôle la configuration et l'outillage.
  - Nécessite d'ajouter manuellement routing, SSR/SSG, structure de dossiers si besoin.

- Nuxt :
  - Framework construit sur Vue, fournit conventions et scaffolding.
  - Ajoute des fonctionnalités prêtes à l'emploi : SSR/SSG, routage automatique, API server.
  - Réduit la configuration initiale, facilite le SEO et le déploiement sur des plateformes serverless.

**Quand choisir Nuxt ?**

- Projet nécessitant SEO, SSR ou génération statique.
- Besoin d'une structure rapide, modules prêts à l'emploi et déploiement serverless.

**Quand choisir Vue seul ?**

- Projet front uniquement où SSR/SSG n'est pas nécessaire.
- Besoin d'une configuration fine et légère, ou intégration dans une architecture existante.

### Modes de rendu — tableau synthétique

| Mode | Description courte | Avantages | Inconvénients | Cas d'usage |
|------|--------------------|----------|---------------|-------------|
| CSR (Client‑Side Rendering) | Le rendu HTML est généré côté client par le navigateur après téléchargement des JS | Interactions rapides après initial load ; développement simple | Mauvais SEO, temps au premier rendu plus long | SPA internes, dashboards authentifiés |
| SSR (Server‑Side Rendering) | Le serveur renvoie du HTML pré‑rendu pour chaque requête | Meilleur SEO, premier rendu rapide, partage social (meta) | Charge serveur plus importante, complexité (hydration) | Sites publics, e‑commerce, blogs dynamiques |
| SSG (Static Site Generation) | Pages générées à la build en HTML statique (prérendu) | Très performant, sécurisé, scale gratuit (CDN) | Moins adapté au contenu très dynamique sans revalidation | Documentation, blogs, landing pages |
| ISR (Incremental Static Regeneration) / Revalidation | SSG + possibilité de régénérer des pages après build (à la demande ou périodiquement) | Meilleure cohérence entre perf et dynamique | Complexité additionnelle, dépend du provider | Sites commerce avec catalogue mis à jour souvent |
| Edge Rendering / SSR@Edge | Rendu SSR exécuté côté edge (CDN) proche de l'utilisateur | Très faible latence, meilleure SEO globalement | Support dépendant du provider et du runtime | Sites internationaux, latence critique |

---

### Structure Nuxt

Pour que Nuxt fonctionne de manière optimale, il faut respecter une certaine structure de dossiers. Voici les principaux répertoires et fichiers :

- `pages/` : routage automatique (chaque fichier = route)
- `layouts/` : templates globaux (ex : default, auth)
- `components/` : composants réutilisables
- `composables/` : composables auto‑importés
- `plugins/` : registres et initialisations (ex : axios, i18n)
- `server/` : endpoints server (API) et middleware
- `app.vue` / `nuxt.config.ts` : configuration globale

La documentation ici est très explicite : https://nuxt.com/docs/4.x/guide/directory-structure/app/app

**/!\ Depuis la version 4, la structure est déportée dans un dossier `app/` pour plus de flexibilité.**

### Modules Nuxt

Nuxt dispose d'un écosystème riche de modules (https://nuxt.com/modules) pour ajouter des fonctionnalités courantes facilement, par exemple :

- Authentification (ex : `@nuxtjs/auth-next`)
- Internationalisation (i18n) (ex : `@nuxtjs/i18n
- Analytics (ex : `@nuxtjs/google-analytics`)
- CMS (ex : `@nuxtjs/content`)
- ...

## Routage avec Nuxt

Le routage dans Nuxt (https://nuxt.com/docs/4.x/getting-started/routing) est automatique via le dossier `pages/`. Chaque fichier `.vue` dans ce dossier devient une route accessible. Il est possible de créer des routes dynamiques, imbriquées, et de configurer des meta‑tags via la propriété `definePageMeta`.

Voici quelques exemples :

- `pages/index.vue` → `/`
- `pages/about.vue` → `/about`
- `pages/blog/index.vue` → `/blog`
- `pages/blog/[id].vue` → `/blog/:id` (route dynamique)
- `pages/dashboard/settings.vue` → `/dashboard/settings` (route imbriquée)
- `pages/user/[...slug].vue` → `/user/*` (catch-all)
  
`definePageMeta` pour configurer les meta‑tags SEO (https://nuxt.com/docs/4.x/api/utils/define-page-meta)

```js
<script setup>
definePageMeta({
  title: 'Page Title',
  meta: [
	{ name: 'description', content: 'Page description for SEO' }
  ]
})
</script>
```

## Exercice

Migrer une application Vue 3 existante vers Nuxt 4 :

1. Créer un nouveau projet Nuxt 4 (`npm create nuxt@latest`).
2. Déplacer et adapter vos fichiers
3. Les composables, services et stores Pinia doivent être placés dans `composables/`, `services/` et `stores/` respectivement.
4. Les views (pages) doivent être placées dans `pages/` avec le routage automatique. Vous pourrez retirer les imports et configurations de Vue Router.

La sécurité, avec Nuxt, (authentification, autorisation) sera vue dans la séance suivante.
