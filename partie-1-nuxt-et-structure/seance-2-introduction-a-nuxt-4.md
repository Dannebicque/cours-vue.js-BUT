# Séance 2 : Introduction à Nuxt 4 & Fondations

## 🎯 Objectifs de la séance

* Comprendre la valeur ajoutée d'un meta-framework fullstack comme **Nuxt 4** par rapport à une Single Page Application (SPA) Vue 3 classique.
* Maîtriser les **5 modes de rendu** du web moderne (CSR, SSR, SSG, ISR/SWR, Edge) et savoir choisir le bon mode selon les contraintes métier et SEO.
* Découvrir la nouvelle architecture standardisée de **Nuxt 4** avec le répertoire central `app/`.
* Comprendre le mécanisme des **Auto-Imports** de Nuxt et ses conventions.
* **Atelier Fil rouge DevFlow** : Initialiser le projet `devflow` avec Nuxt 4, TypeScript en mode strict et configuration de base.

---

## 🚀 1. Qu'est-ce que Nuxt 4 ?

Vue 3 est une excellente bibliothèque pour construire des interfaces utilisateur réactives côté client. Cependant, pour une application d'envergure professionnelle, vous devez configurer manuellement :
* Le routeur (`vue-router`) et la gestion des URL.
* Le rendu côté serveur (**SSR**) pour le SEO et le premier affichage rapide (**First Contentful Paint**).
* La compilation et l'optimisation des assets (Vite).
* Les routes d'API serveur et l'authentification sécurisée.

**Nuxt 4** est un **meta-framework fullstack** basé sur Vue 3. Il apporte une structure conventionnelle, une couche serveur unifiée grâce au moteur **Nitro**, et automatise l'ensemble des tâches répétitives.

```
┌────────────────────────────────────────────────────────────┐
│                           Nuxt 4                           │
├──────────────────────────────┬─────────────────────────────┤
│  Client (Vue 3 + Vite)       │  Serveur (Moteur Nitro)     │
│  - Composition API & TS      │  - Endpoints API server/    │
│  - Auto-imports de composants│  - Middlewares serveur      │
│  - Routage par fichiers      │  - Rendu SSR ultra-rapide   │
│  - Layouts et Transitions    │  - Déploiement serverless   │
└──────────────────────────────┴─────────────────────────────┘
```

---

## 📊 2. Les Modes de Rendu dans le Web Moderne

Le choix du mode de rendu a un impact direct sur la performance, les coûts d'hébergement, la fraîcheur des données et le référencement naturel (**SEO**).

### Tableau comparatif exhaustif

| Mode | Fonctionnement | Avantages | Inconvénients | Cas d'usage types |
| :--- | :--- | :--- | :--- | :--- |
| **CSR** *(Client-Side Rendering)* | Le serveur renvoie un HTML minimal et le bundle JS. Le navigateur construit tout le DOM. | Navigation instantanée après chargement, coût serveur quasi nul. | Premier affichage plus lent (*blank page*), SEO complexe sans pré-rendu. | Dashboards privés, back-offices, SaaS authentifiés (ex: Trello, Jira). |
| **SSR** *(Server-Side Rendering)* | À chaque requête, le serveur génère le HTML complet à la volée. Vue prend le relais via l'**hydratation**. | Excellent SEO, contenu immédiatement visible, métadonnées dynamiques. | Charge CPU sur le serveur, temps de réponse (*TTFB*) dépendant de la base de données. | E-commerce, sites d'actualités, réseaux sociaux, plateformes d'offres. |
| **SSG** *(Static Site Generation)* | Le HTML de toutes les pages est pré-généré **au moment du build** (`nuxi generate`). | Vitesse maximale sur CDN mondial, sécurité absolue, zéro charge serveur. | Nécessite un rebuild complet pour mettre à jour le contenu. | Documentation, sites vitrines, blogs, supports de cours GitBook. |
| **ISR / SWR** *(Incremental Static / Stale-While-Revalidate)* | Pages statiques régénérées périodiquement en tâche de fond après expiration d'un cache. | Performances du statique tout en gardant des données fraîches. | Complexité de cache et léger décalage de fraîcheur de données. | Catalogues de 50 000 produits, portails d'annonces immobilières. |
| **Edge Rendering** | Le code SSR s'exécute sur des serveurs distribués au plus près géographique de l'utilisateur (CDN Edge). | Latence réseau minimale (< 20 ms) pour une audience mondiale. | Environnement d'exécution restreint (V8 isolates sans Node.js complet). | Applications SaaS internationales, plateformes de streaming mondiales. |

{% hint style="success" %}
**La force de Nuxt 4 :** Vous pouvez configurer des modes de rendu différents pour chaque route de votre application grâce à l'option `routeRules` dans `nuxt.config.ts` (Rendu Hybride).
{% endhint %}

---

## 📁 3. Nouvelle structure de dossiers Nuxt 4 : Le dossier `app/`

Dans Nuxt 4, pour séparer proprement le code front-end du code serveur Nitro et de la configuration racine, l'ensemble du code applicatif Vue est regroupé dans le dossier **`app/`**.

```
devflow/
├── app/                          # 🌟 Coeur applicatif front-end Nuxt 4
│   ├── app.vue                   # Composant racine de l'application
│   ├── error.vue                 # Page d'erreur personnalisée (404, 500)
│   ├── assets/                   # Fichiers sources non compilés (CSS, images)
│   ├── components/               # Composants Vue (auto-importés)
│   ├── composables/              # Logique réactive réutilisable (auto-importée)
│   ├── layouts/                  # Layouts de mise en page (default.vue, auth.vue)
│   ├── middleware/               # Middlewares de route (auth.ts)
│   ├── pages/                    # Routage automatique basé sur les fichiers
│   └── types/                    # Définitions TypeScript métier
│
├── server/                       # ⚙️ Moteur serveur Nitro (Backend interne)
│   ├── api/                      # Routes d'API serveur (/api/tasks)
│   └── middleware/               # Middlewares serveur HTTP
│
├── public/                       # Fichiers statiques servis à la racine (/favicon.ico)
├── nuxt.config.ts                # Configuration globale du projet Nuxt
├── package.json                  # Dépendances et scripts npm
└── tsconfig.json                 # Configuration TypeScript
```

---

## ⚡ 4. Le Système des Auto-Imports

Nuxt 4 auto-importe automatiquement les fonctions et composants essentiels. Vous n'avez plus besoin d'écrire des dizaines de lignes d'imports au début de chaque fichier.

### Ce qui est auto-importé par défaut :
1. **Les primitives Vue 3** : `ref`, `reactive`, `computed`, `watch`, `onMounted`, `nextTick`, etc.
2. **Les utilitaires Nuxt** : `useRoute`, `useRouter`, `useFetch`, `useAsyncData`, `navigateTo`, `useHead`, `useSeoMeta`.
3. **Tous les composants** placés dans `app/components/`.
4. **Tous les composables** placés dans `app/composables/`.
5. **Tous les utilitaires** placés dans `app/utils/`.

---

## 🛠️ 5. Atelier Pratique : Lancement du Fil Rouge "DevFlow"

### Votre mission chez DevSquad :

1. **Initialiser le projet Nuxt 4** avec la commande officielle `nuxi init devflow`.
2. **Configurer `nuxt.config.ts`** :
   * Activer le typage strict (`typescript: { strict: true, typeCheck: true }`).
   * Configurer les métadonnées HTML de base, de manière globale, (`title`, `description`, `lang: 'fr'`).
   * Activer les DevTools Nuxt.
3. **Créer l'arborescence `app/`** et configurer `app/app.vue` avec `<NuxtLayout>` et `<NuxtPage />`.
4. **Lancer le serveur de développement** (`npm run dev`) et explorer l'interface des **Nuxt DevTools** (`Shift + Alt + D`).

{% hint style="tip" %}
La solution détaillée et commentée de cet atelier est disponible dans la page [Séance 2 (correction)](seance-2-correction.md).
{% endhint %}

---

## 🎯 Auto-évaluation & Checklist

* [ ] Je sais expliquer la différence fondamentale entre Nuxt 4 et Vue 3 seul.
* [ ] Je sais justifier le choix entre SSR, SSG et CSR pour une fonctionnalité donnée.
* [ ] J'ai compris le rôle du dossier `app/` dans Nuxt 4.
* [ ] Mon projet `devflow` est initialisé avec `compatibilityVersion: 4` et `typescript.strict: true`.
* [ ] Mon serveur de développement démarre sans erreur.
