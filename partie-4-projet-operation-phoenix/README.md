# Projet en Autonomie : "Opération Phoenix" (Séances 15 à 24)

## 🏢 Contexte : Bienvenue chez DevSquad

Vous travaillez en équipe de 3 à 4 développeurs au sein de l'agence web **DevSquad**. Après avoir complété votre phase d'onboarding sur le projet interne **DevFlow** (Séances 1 à 14), le comité de direction vous confie la réalisation en autonomie complète d'un nouveau produit **SaaS d'entreprise**.

---

## 🎯 1. Choix du Sujet SaaS (1 au choix par équipe)

Chaque équipe choisit l'un des trois sujets suivants lors de la Séance 15 :

### 📦 Option A : "StockMaster Pro" — SaaS Logistique & Gestion de Stock
* **Cible :** PME et e-commerçants gérant plusieurs entrepôts.
* **Fonctionnalités clés :**
  * Suivi des mouvements d'entrées/sorties en temps réel avec alertes de rupture.
  * Gestion des fournisseurs, bons de commande et réapprovisionnements.
  * Tableau de bord analytique des rotations de stocks avec graphiques.

### 🎪 Option B : "FestivApp" — Plateforme d'Organisation de Festival
* **Cible :** Organisateurs d'événements culturels et musicaux.
* **Fonctionnalités clés :**
  * Programmation des artistes par scène et par créneau horaire avec détection de conflits.
  * Gestion du catering, des bénévoles et des accréditations VIP.
  * Billetterie interactive et dashboard de suivi des ventes en direct.

### 🎓 Option C : "SkillQuest" — Plateforme SaaS Ludo-Éducative
* **Cible :** Centres de formation et écoles supérieures.
* **Fonctionnalités clés :**
  * Création de parcours de compétences gamifiés avec niveaux et badges.
  * Moteur de quiz interactifs avec timer et calcul de score dynamique.
  * Leaderboard en direct et tableau de bord de progression des étudiants.

---

## 🚀 2. Les 4 Jalons Obligatoires (Validation GitHub)

La progression de votre équipe est jalonnée de 4 revues de code obligatoires :

```
S15 ──► [ Jalon 1 (S16) : Le Contrat ] ──► [ Jalon 2 (S18) : Le Moteur ] ──► [ Jalon 3 (S20) : Le Bouclier ] ──► [ Jalon 4 (S24) : La Livraison ]
```

### 📍 Jalon 1 (Séance 16) — Le Contrat Technique
* **Objectifs :** Initialisation du dépôt GitHub, configuration Nuxt 4 + TypeScript strict, arborescence propre (`app/`, `server/`), modélisation complète des interfaces (`types/`).
* **Livrable :** Pull Request `PR #1 : Architecture & Types` validée par une revue de code entre pairs.

### 📍 Jalon 2 (Séance 18) — Le Moteur Fonctionnel
* **Objectifs :** Routes API Nitro (`server/api/`), State Management global avec Pinia, Data Fetching (`useFetch`) et formulaires interactifs.
* **Livrable :** Release / Tag GitHub `v0.5-beta` avec fonctionnalités principales opérationnelles.

### 📍 Jalon 3 (Séance 20) — Le Bouclier & Qualité
* **Objectifs :** Tests unitaires (Vitest) sur les fonctions critiques et stores, middlewares de route, audit de sécurité et tenue rigoureuse du journal `AI_PROMPT_BOOK.md`.
* **Livrable :** Pull Request `PR #2 : Tests & Hardening`.

### 📍 Jalon 4 (Séance 24) — La Livraison & Client Demo
* **Objectifs :** Build de production optimisé, SEO / Meta-tags, README complet et **Soutenance orale en conditions réelles** face au jury client (15 min de démo + 10 min de questions techniques).
* **Livrable :** Tag GitHub `v1.0.0-final`, dépôt public propre et déploiement en ligne.

---

## 📋 3. Grille d'Évaluation Détaillée (/20 points)

| Axe | Barème | Critères d'exigence |
| :--- | :---: | :--- |
| **1. Architecture, Code Clean & TypeScript** | **/5 pts** | Respect strict des conventions Nuxt 4 (`app/`), absence totale de `any`, découpage SOLID, composables modulaires. |
| **2. Implémentation Technique & Fonctionnelle** | **/5 pts** | Fonctionnalités du SaaS complètes et fluides, stores Pinia pertinents, gestion propre des routes/middlewares, ergonomie UI soignée. |
| **3. Stratégie de Test & Qualité** | **/4 pts** | Suite de tests Vitest verte et pertinente sur le code critique (stores, composables, calculs), zéro test fantoche. |
| **4. Esprit Critique, Usage IA & Soutenance** | **/6 pts** | Journal de bord `AI_PROMPT_BOOK.md` documenté, maîtrise totale de chaque ligne de code en direct (zéro boîte noire), qualité de la démo orale. |
