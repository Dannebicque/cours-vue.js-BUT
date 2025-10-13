# Séance 9 – Tests front

## 🎯 Objectifs

- Écrire des tests unitaires et E2E.
- Découvrir Playwright.

## 📖 Partie théorique

### Introduction aux tests

Les tests sont essentiels pour garantir la qualité et la fiabilité du code. Ils permettent de détecter les régressions, de vérifier le comportement attendu et d'assurer une meilleure maintenabilité du projet. Dans cette séance, nous allons explorer les différents types de tests et les outils associés.

Les tests viennent s'intégrer dans le cycle de développement, souvent via l'intégration continue (CI), pour automatiser la vérification du code à chaque modification et avant chaque déploiement.

Il existe plusieurs types de tests :

1. Tests unitaires (Unit tests)
2. Tests d’intégration (Integration tests)
3. Tests de bout en bout (E2E - End to End)

### Tests unitaires (Unit tests) avec Vitest

- Tests unitaires des composants Vue.
- Vérification du rendu et des props.
- Tests des composables et fonctions utilitaires.

### Tests d’intégration (Integration tests) avec Vitest

- Tests des interactions entre plusieurs composants.
- Vérification du comportement global d’une fonctionnalité.

### E2E avec Cypress ou Playwright

- Tests de bout en bout simulant des scénarios utilisateurs.
- Vérification du fonctionnement global de l’application.

### Pyramide des tests

- Structure recommandée :
  - Tests unitaires (base)
  - Tests d’intégration (milieu)
  - Tests E2E (sommet)


## 📝 Travaux pratiques
- Tester un composant simple (`TaskCard`).
- Écrire un test E2E, avec Cypress (login → ajout tâche).

