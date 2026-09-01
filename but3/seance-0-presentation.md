# Séance 0 : Présentation de la ressource en BUT3

{% hint style="success" %}
## Ce cours est co-rédigé avec une IA, sur une idée et une proposition humaine. L'usage de l'IA est autorisé dans ce module.
{% endhint %}

## 🚀 Nuxt 4, TypeScript & Architecture Web Pro

Ce module de 24 séances de 1h30 a pour objectif  : vous faire basculer du statut d'étudiant développeur à celui de développeur front-end professionnel, capable de concevoir des applications robustes, testées et maintenables, tout en maîtrisant l'usage critique des intelligences artificielles.

### 🗺️ Vue d'Ensemble du Parcours

| **Phase** | **Volume**      | **Format**                                | **Objectif Principal**                                                                |
| --------- | --------------- | ----------------------------------------- | ------------------------------------------------------------------------------------- |
| Partie 1  | Séances 1 à 6   | Cours & TP Guidés (Fil rouge "DevFlow")   | Poser les bases de Nuxt 4 et structurer le code avec TypeScript.                      |
| Partie 2  | Séances 7 à 12  | Cours & TP Guidés (Fil rouge "DevFlow")   | Maîtriser le Data Fetching, les API internes et la gestion d'état avec Pinia.         |
| Partie 3  | Séances 13 à 14 | Ateliers Pratiques                        | Assurer la qualité via les tests unitaires (Vitest) et l'esprit critique face à l'IA. |
| Partie 4  | Séances 15 à 24 | Projet en Autonomie ("Opération Phoenix") | Mettre en pratique l'ensemble sur un SaaS métier complet par équipes.                 |

### 🧵 Le Double Fil Conducteur

1.  Le Fil Rouge d'Apprentissage ("DevFlow" - S1 à S14) :

    Un mini-projet de gestion de tâches construit et corrigé pas à pas avec vous en TD/TP pour expérimenter chaque brique technique (routing, types, stores, tests).
2.  Le Projet Final Gamifié ("Opération Phoenix" - S15 à S24) :

    Une mise en situation en agence (« DevSquad ») où votre équipe doit concevoir, tester et déployer en autonomie un SaaS d'entreprise (au choix : gestion de stock, organisation de festival ou plateforme ludo-éducative).

### 🎯 Les Jalons du Projet Final (Validation sur GitHub)

Le projet final est rythmé par 4 points d'étape obligatoires :

* Jalon 1 (Séance 16) — Le Contrat :
  * Initialisation du dépôt, configuration stricte de TypeScript (`tsconfig`), mise en place de l'arborescence et modélisation des types de données.
  * _Livrable :_ Pull Request initiale validée par un pair (Code Review).
* Jalon 2 (Séance 18) — Le Moteur :
  * Connexion aux flux de données (`useFetch`), création des routes API Nitro (`server/`) et centralisation de l'état avec Pinia.
  * _Livrable :_ Tag de version sur GitHub (`v0.5-beta`).
* Jalon 3 (Séance 20) — Le Bouclier :
  * Écriture des tests unitaires (Vitest) sur le code critique, audit de sécurité et rédaction du Journal de bord IA (`AI_PROMPT_BOOK.md`).
  * _Livrable :_ Pull Request de "Hardening & Tests".
* Jalon 4 (Séance 24) — La Livraison :
  * Optimisation, build de production, documentation technique et Soutenance orale ("Client Demo") en conditions réelles.

## Ressources

* [Documentation Vue 3](https://vuejs.org/guide/introduction.html)
* [Documentation Nuxt](https://nuxt.com/docs/getting-started/introduction)
* [Documentation Api Platform](https://api-platform.com/docs/)
* [Documentation Pinia](https://pinia.vuejs.org/)

## 📋 Grille d'Évaluation — Projet Final "Opération Phoenix"

Barème global : /20 points _(Découpé en 4 axes majeurs)_

#### 1. Architecture, Code Clean & TypeScript (5 points / 20)

_Évalue la rigueur structurelle et la maîtrise du typage._

* Respect des conventions Nuxt 4 (1.5 pt) : Arborescence irréprochable (séparation stricte entre `pages/`, `components/`, `composables/`, `server/`). Pas de logique métier fourrée dans les composants UI.
* Maîtrise de TypeScript (2 pt) : Utilisation rigoureuse du dossier `types/`. Typage strict des props, des émits, des stores et des retours d'API. Absence totale de types `any` injustifiés.
* Modularité & Composables (1.5 pt) : Extraction pertinente de la logique métier dans des composables réutilisables et propres.

> _Détail de notation :_
>
> * _5/5 :_ Code irréprochable, typage strict et élégant, architecture digne d'une agence pro.
> * _3/5 :_ Architecture globale respectée mais quelques oublis de typage ou présence de `any` de complaisance.
> * _1/5 :_ Code spaghetti, pas de types personnalisés, arborescence non respectée.

#### 2. Implémentation Technique & Fonctionnelle (5 points / 20)

_Évalue la capacité à faire fonctionner l'application SaaS demandée._

* Flux de Données & State Management (2 pt) : Utilisation pertinente de `useFetch` / `useAsyncData` et gestion propre de l'état global avec Pinia (persistance, actions, getters).
* Sécurité & Navigation (1.5 pt) : Mise en place correcte des middlewares de route (protection des espaces administratifs/privés) et gestion propre des erreurs.
* Qualité de l'UI & UX (1.5 pt) : Intégration propre de Tailwind CSS, ergonomie du tableau de bord, et soin apporté à l'expérience utilisateur.

> _Détail de notation :_
>
> * _5/5 :_ Application fluide, fonctionnalités du SaaS entièrement opérationnelles, gestion fine du SSR et de l'état.
> * _3/5 :_ Application fonctionnelle mais bugs mineurs sur le state ou ergonomie perfectible.
> * _1/5 :_ L'application plante au chargement, routes non sécurisées, absence de store Pinia pertinent.

#### 3. Stratégie de Test & Qualité (4 points / 20)

_Évalue l'exigence de robustesse sur le code critique._

* Pertinence des tests unitaires (2 pt) : Vitest est utilisé à bon escient. Les tests ciblent le code critique (composables métiers, fonctions de calcul, stores Pinia) et non du simple markup statique.
* Robustesse et exécution (2 pt) : Les tests passent avec succès sans erreurs, et les étudiants savent expliquer ce qu'ils testent et pourquoi.

> _Détail de notation :_
>
> * _4/4 :_ Tests ciblés et pertinents sur la logique métier, suite de tests verte et commentée.
> * _2/4 :_ Tests présents mais superficiels (tests "fantoches" générés machinalement sans réelle valeur ajoutée).
> * _0/4 :_ Aucun test unitaire présent.

#### 4. Esprit Critique, Usage de l'IA & Soutenance (6 points / 20)

_Évalue la posture professionnelle, l'honnêteté intellectuelle et la défense du code._

* Le Journal de Bord IA - `AI_PROMPT_BOOK.md` (2 pt) : Présence d'un journal documentant les prompts complexes utilisés, les pièges/hallucinations de l'IA rencontrés et corrigés par l'équipe.
* Maîtrise du code lors de la démo (2 pt) : Capacité à expliquer n'importe quelle ligne de code du projet en direct lors de la soutenance (zéro boîte noire acceptée).
* Qualité de la soutenance & Rendu GitHub (2 pt) : Respect des jalons intermédiaires sur GitHub, propreté des Pull Requests, qualité de la démo live et du README technique.

> _Détail de notation :_
>
> * _6/6 :_ Recul critique exemplaire sur l'IA, journal détaillé, code parfaitement maîtrisé et soutenance convaincante.
> * _3/6 :_ Incapacité à expliquer une partie du code généré par l'IA ou journal de bord minimaliste.
> * _0/6 :_ Projet "boîte noire" total (l'équipe ne comprend pas ce que l'IA a codé) ou absence de journal de bord.
