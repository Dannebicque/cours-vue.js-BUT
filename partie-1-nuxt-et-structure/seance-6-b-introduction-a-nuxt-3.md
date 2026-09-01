# Séance 1-b : Introduction à Nuxt 4

## 🎯 Objectifs

* Découvrir Nuxt et ses apports (SSR, SSG, SEO).
* Migrer une app Vue vers Nuxt.

## Qu'est-ce que Nuxt.Js ?

Nuxt (https://nuxt.com/), vesion 4 aujourd'hui, est un framework construit au‑dessus de Vue 3 qui facilite la création d'applications universelles (fullstack) : sites statiques, applications server‑rendered (SSR) ou applications hybridées. Nuxt vise à fournir une structure et des conventions pour accélérer le développement (routing automatique, layouts, plugins, modules, import automatisés, ...). Sa runtime moderne repose sur Nitro (moteur serveur agnostique) et offre un excellent support pour le rendu côté serveur, le prerendering, le déploiement serverless et l'optimisation des performances.

Points clés de Nuxt 4 :

* Routing automatique via `pages/`.
* Layouts et slots globaux via `layouts/`.
* API server intégrée via `server/` (endpoints API côté serveur).
* Nitro : abstraction du serveur.
* Modules & plugins pour étendre les fonctionnalités (auth, i18n, analytics).
* Support SSR, SSG et ISR (hybride) natif.
* Intégration simple avec Vite, TypeScript et Pinia.

### Comparatif Nuxt vs Vue (grand lignes)

* Vue :
  * Framework UI (lib) pour construire des interfaces réactives côté client.
  * Idéal pour des applications SPA où l'on contrôle la configuration et l'outillage.
  * Nécessite d'ajouter manuellement routing, SSR/SSG, structure de dossiers si besoin.
  * Liberté d'organisation des fichiers.&#x20;
* Nuxt :
  * Framework construit sur Vue, fournit conventions et scaffolding.
  * Ajoute des fonctionnalités prêtes à l'emploi : SSR/SSG, routage automatique, API server.
  * Réduit la configuration initiale, facilite le SEO et le déploiement sur des plateformes serverless.
  * Structure rigide de dossier pour une automatisation importante

**Quand choisir Nuxt ?**

* Projet nécessitant SEO, SSR ou génération statique.
* Besoin d'une structure rapide, modules prêts à l'emploi et déploiement serverless.

**Quand choisir Vue seul ?**

* Projet front uniquement où SSR/SSG n'est pas nécessaire.
* Besoin d'une configuration fine et légère, ou intégration dans une architecture existante.

### Modes de rendu — tableau synthétique

| Mode                             | Comment ça fonctionne ?                                                                                                                                                      | Avantages                                                                 | Inconvénients                                                                                    | Cas d’usage                                                              | Exemple concret                                                                                                         |
| -------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------- |
| **CSR** — Client-Side Rendering  | Le serveur envoie essentiellement le JavaScript. Le navigateur exécute Vue et construit ensuite la page HTML.                                                                | Navigation très fluide après chargement, architecture simple pour une SPA | Premier affichage potentiellement plus lent, SEO moins favorable, dépend fortement du JavaScript | Applications internes, dashboards, back-offices                          | **ENT universitaire**, tableau de bord d’administration, application de gestion où l’utilisateur doit être connecté     |
| **SSR** — Server-Side Rendering  | À chaque requête, le serveur exécute l’application et renvoie directement une page HTML déjà construite. Vue prend ensuite le relais côté navigateur avec l’**hydratation**. | Très bon premier affichage, contenu immédiatement disponible, bon SEO     | Plus de travail côté serveur, gestion client/serveur parfois plus complexe                       | Sites publics avec contenu dynamique, e-commerce, actualités             | Une **fiche produit** dont le prix et le stock changent régulièrement, ou la page publique d’un événement               |
| **SSG** — Static Site Generation | Les pages HTML sont générées **au moment du build**, puis servies telles quelles par un serveur ou un CDN.                                                                   | Extrêmement rapide, très peu de charge serveur, facile à mettre en cache  | Une modification du contenu nécessite généralement une nouvelle génération                       | Documentation, portfolio, site vitrine, contenu peu dynamique            | **Ce support de cours**, un site de documentation, un portfolio d’étudiant                                              |
| **ISR / revalidation**           | On part de pages statiques, mais certaines peuvent être régénérées périodiquement ou après expiration du cache.                                                              | Performances proches du statique avec un contenu relativement frais       | Cache et revalidation à comprendre, comportement dépendant du déploiement                        | Catalogue, actualités peu fréquentes, contenu semi-dynamique             | Un catalogue de **10 000 produits** : les pages sont mises en cache mais peuvent être recalculées toutes les 10 minutes |
| **Edge Rendering**               | Le rendu serveur est effectué sur une infrastructure distribuée géographiquement, au plus près de l’utilisateur.                                                             | Latence réduite pour une audience internationale                          | Runtime parfois limité, dépend fortement de l’hébergeur                                          | Sites internationaux ou applications où quelques dizaines de ms comptent | Un site mondial dont un utilisateur français est servi depuis Paris et un utilisateur japonais depuis Tokyo             |

***

### Structure Nuxt

Pour que Nuxt fonctionne de manière optimale, il faut respecter une certaine structure de dossiers.&#x20;

La documentation ici est très explicite : [https://nuxt.com/docs/4.x/guide/directory-structure/app/app](https://nuxt.com/docs/4.x/guide/directory-structure/app/app)

**/!\ Depuis la version 4, la structure est déportée dans un dossier `app/` pour plus de flexibilité.**

### Modules Nuxt

Nuxt dispose d'un écosystème riche de modules ([https://nuxt.com/modules](https://nuxt.com/modules)) pour ajouter des fonctionnalités courantes facilement, par exemple :

* Authentification (ex : `@nuxtjs/auth-next`)
* Internationalisation (i18n) (ex : \`@nuxtjs/i18n
* Analytics (ex : `@nuxtjs/google-analytics`)
* CMS (ex : `@nuxtjs/content`)
* ...

## Routage avec Nuxt

Le routage dans Nuxt (https://nuxt.com/docs/4.x/getting-started/routing) est automatique via le dossier `pages/`. Chaque fichier `.vue` dans ce dossier devient une route accessible. Il est possible de créer des routes dynamiques, imbriquées, et de configurer des meta‑tags via la propriété `definePageMeta`.

Voici quelques exemples :

* `pages/index.vue` → `/`
* `pages/about.vue` → `/about`
* `pages/blog/index.vue` → `/blog`
* `pages/blog/[id].vue` → `/blog/:id` (route dynamique)
* `pages/dashboard/settings.vue` → `/dashboard/settings` (route imbriquée)
* `pages/user/[...slug].vue` → `/user/*` (catch-all)

`definePageMeta` pour configurer les meta‑tags SEO (https://nuxt.com/docs/4.x/getting-started/seo-meta#usehead)

```js
<script setup>
useHead({ 
  title: 'Page Title',
  meta: [
	{ name: 'description', content: 'Page description for SEO' }
  ]
})
</script>
```

/!\ definePageMeta dans la version 3 de Nuxt.

## Premier projet fil rouge : "DevFlow — Le Mini-Tracker de Tâches d'Équipe"

### Votre premier jour en tant que Développeur Front-End Junior.

> _"Félicitations, vous venez d'être recrutés par l'agence web DevSquad ! Avant de vous confier les projets critiques de nos plus gros clients (ce sera votre projet final), le Lead Tech a une mission pour vous."_

* Le Problème : L'agence gère ses projets sur Jira (c'est trop lourd) et sur Trello (c'est trop limité). Les équipes perdent du temps.
* La Mission : L'agence a décidé de développer son propre outil interne de suivi de tâches. Un outil ultra-rapide, sur-mesure et pensé par des devs, pour des devs.

#### 💡 Le Produit

Nom de code : DevFlow — Le Mini-Tracker de Tâches d'Équipe.

Pas question de faire une simple "To-Do list" d'étudiant de première année. Nous allons concevoir une véritable application métier web.

* Rapide & Réactif : Zéro temps de chargement frustrant.
* Pro & Ergonomique : Une interface moderne, épurée (et avec un mode sombre, évidemment).
* Collaboratif : Des tâches assignées, des statuts qui évoluent, des filtres dynamiques pour retrouver son travail en un clic.

#### 🛠️ Les Fonctionnalités (Votre carnet de commandes)

Pendant les prochaines semaines, voici ce que nous allons implémenter étape par étape :

1. L'Espace Public & Sécurisé : Une page de connexion et un tableau de bord verrouillé pour les membres de l'équipe.
2. Le Dashboard Dynamique : Affichage des tâches sous forme de cartes avec priorités et catégories.
3. Le Moteur de Filtrage : Trier instantanément les tâches par utilisateur, par statut (À faire, En cours, Terminé) ou par urgence.
4. La Gestion d'État : Sauvegarder les préférences de l'utilisateur et maintenir l'application fluide sans recharger la page à chaque clic.

#### 🎯 La Philosophie (Pourquoi DevFlow ?)

DevFlow est votre terrain d'entraînement (Onboarding).

L'objectif de l'agence n'est pas seulement que l'outil fonctionne, mais qu'il soit bien construit. À travers la conception de DevFlow, vous allez apprendre les standards industriels actuels :

* Fini le code brouillon : place à une architecture stricte et professionnelle.
* Fini le "ça marche chez moi" : place à la rigueur (TypeScript) et aux tests.
* Fini le code généré à l'aveugle : vous utiliserez l'IA comme un assistant, mais vous resterez les seuls pilotes à bord.

> _"Une fois que vous maîtriserez la création de DevFlow avec nous, vous serez prêts à voler de vos propres ailes pour la grande mission de fin de semestre."_

## Step 1...

Mettre en place une application Nuxt 4 :

1. Créer un nouveau projet Nuxt 4 (`npm create nuxt@latest`).
2. Posez la structure et assurez vous d'avoir TypeScript en mode strict
3. Définissez une première interface pour des User et ajouté une page pour les afficher
4. Posez une interface pour une tâche

To be continued...&#x20;

