# Séance 12 : Intégration Complète & Consolidation DevFlow

## 🎯 Objectifs de la séance

* Finaliser l'intégration de bout en bout de l'application fil rouge **DevFlow v1.0**.
* Valider la chaîne complète : Composants UI $\leftrightarrow$ Composables $\leftrightarrow$ Stores Pinia $\leftrightarrow$ API Nitro $\leftrightarrow$ Middlewares.
* Effectuer un audit de robustesse : gestion des erreurs réseau, mode hors-ligne partiel, messages de feedback (Toasts).
* Préparer la transition vers la **Partie 3 : Qualité & Tests**.

---

## 🏗️ 1. Schéma d'Intégration Globale de DevFlow

```
┌────────────────────────────────────────────────────────────────────────┐
│                               NAVIGATEUR                               │
│                                                                        │
│  Pages (/login, /tasks, /team)                                         │
│       │                                                                │
│  Middlewares de route (auth.ts) ──► Vérifie Pinia useAuthStore         │
│       │                                                                │
│  Composants UI (TaskCard, TaskColumn, AppModal)                        │
│       │                                                                │
│  Stores Pinia (useTaskStore, useAuthStore) + Plugins Persistance       │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ Requêtes HTTP ($fetch / useFetch)
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                          SERVEUR NITRO (Nuxt 4)                        │
│                                                                        │
│  Middlewares HTTP (/server/middleware/...)                             │
│       │                                                                │
│  Handlers d'API (/server/api/tasks/...)                                │
│       │                                                                │
│  Base de données / Stockage persistant                                 │
└────────────────────────────────────────────────────────────────────────┘
```

---

## 🛠️ 2. Checklist d'Intégration & Rendu DevFlow v1.0

Avant de démarrer les séances de tests automatisés, vérifiez que votre projet respecte l'ensemble des critères suivants :

* [ ] **Authentification** : Formulaire de connexion opérationnel avec token simulé et persistance Pinia.
* [ ] **Protection des routes** : Impossible d'accéder à `/tasks` sans être authentifié.
* [ ] **CRUD complet des tâches** : Création via modal, modification de statut par glisser/sélection, suppression avec confirmation, persistance via API Nitro.
* [ ] **Filtres combinés** : Recherche textuelle instantanée, filtrage par statut et priorité sans rechargement de page.
* [ ] **Typage strict** : Zéro erreur TypeScript (`npx vue-tsc --noEmit`).
* [ ] **Clean Architecture** : Aucun appel `$fetch` direct dans les composants de présentation (délégué aux stores ou composables).
