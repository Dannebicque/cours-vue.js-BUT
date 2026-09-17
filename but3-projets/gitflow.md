# GitFlow et Bonnes pratiques Git

## GitFlow

GitFlow est une stratégie de gestion de version basée sur Git qui permet de gérer les différentes branches d'un projet de manière structurée. Elle est particulièrement adaptée aux projets avec des cycles de développement réguliers et des releases planifiées.

GitFlow recommende l'utilisation de plusieurs branches principales :

- **main** : la branche principale qui contient le code en production. Il n'est pas recommandé de travailler directement sur cette branche.
- **develop** : la branche de développement où toutes les fonctionnalités sont intégrées avant d'être fusionnées dans la branche main.
- **feature/** : des branches de fonctionnalités qui sont créées à partir de la branche develop pour développer de nouvelles fonctionnalités. Une fois la fonctionnalité terminée, elle est fusionnée dans develop.
- **release/** : des branches de release qui sont créées à partir de la branche develop lorsque le code est prêt pour une nouvelle version. Ces branches permettent de préparer la release, de corriger les bugs et d'effectuer les tests avant de fusionner dans main et develop.
- **hotfix/** : des branches de correction de bugs qui sont créées à partir de la branche main pour corriger rapidement des problèmes critiques en production. Une fois le bug corrigé, la branche hotfix est fusionnée dans main et develop.

Dans notre configuration :

- La branche **main** est protégée et ne peut pas être modifiée directement. Toutes les modifications doivent passer par des Pull Requests. Seul le QA peut valider les Pull Requests sur cette branche.
- La branche **develop** est également protégée et ne peut pas être modifiée directement. Toutes les modifications doivent passer par des Pull Requests. Seul le PO peut valider les Pull Requests sur cette branche.
- Les autres branches (feature, release, hotfix) sont créées à partir de develop et peuvent être fusionnées dans develop via des Pull Requests. Les développeurs peuvent créer ces branches pour travailler sur de nouvelles fonctionnalités ou corriger des bugs.

Un guide très détaillé : [https://www.atlassian.com/fr/git/tutorials/comparing-workflows/gitflow-workflow](https://www.atlassian.com/fr/git/tutorials/comparing-workflows/gitflow-workflow)

## Bonnes pratiques Git

Les messages des commits, il respecteront le [Conventionnal Commits](https://www.conventionalcommits.org/en/v1.0.0/) pour faciliter la lecture de l'historique des commits et la génération automatique de changelogs. Les commit seront en **Anglais** et respecteront le format suivant :

```
<type>(<scope>): <subject>
```

### Les types les plus courants

- feat: Ajout d'une nouvelle fonctionnalité (correspond à une modification mineure).
- fix: Correction d'un bug (correspond à un patch).
- docs: Modification ou ajout de documentation (README, commentaires, etc.).
- style: Changements qui n'affectent pas le sens du code (espaces, formatage, point-virgules, CSS...).
- refactor: Refactorisation du code (ni correction de bug, ni ajout de fonctionnalité).
- perf: Amélioration des performances.
- test: Ajout ou modification de tests unitaires/fonctionnels.
- chore: Modification des outils de build, configuration, tâches de maintenance (ex: mise à jour des packages npm).
  