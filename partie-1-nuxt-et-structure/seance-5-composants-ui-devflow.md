# Séance 5 : Composants UI & Dashboard DevFlow

## 🎯 Objectifs de la séance

* Concevoir une architecture de **composants UI modulaires, réutilisables et accessibles** dans Nuxt 4.
* Exploiter les fonctionnalités modernes de Vue 3.4+ / Nuxt 4, notamment la macro **`defineModel()`** pour les liaisons bidirectionnelles élégantes.
* Intégrer des **transitions fluides et micro-animations** avec `<Transition>` et `<TransitionGroup>`.
* Valider l'intégrité du code et la conformité du typage statique avec **`npx vue-tsc --noEmit`**.
* **Atelier Fil rouge DevFlow (Étape 3)** : Finaliser le tableau de bord Kanban interactif avec modal de création et filtres dynamiques.

---

## 🧩 1. Découpage Atomique de l'Interface DevFlow

Pour respecter le principe de responsabilité unique (**SRP**), nous décomposons le tableau de bord en plusieurs composants spécialisés :

```
app/components/
 ├── common/
 │   ├── AppModal.vue            # Modal réutilisable avec slot et defineModel(isOpen)
 │   └── UserAvatar.vue          # Avatar utilisateur avec initiales fallback
 └── tasks/
     ├── TaskCard.vue            # Carte unitaire de tâche (actions, statut, lien)
     ├── TaskColumn.vue          # Colonne Kanban (titre, compteur, liste animée)
     └── TaskFormModal.vue       # Formulaire de création / édition d'une tâche
```

---

## ⚡ 2. Simplifier la Réactivité avec `defineModel()`

Avant Vue 3.4, créer un composant avec liaison bidirectionnelle (`v-model`) nécessitait de déclarer une prop `modelValue` et d'émettre un événement `update:modelValue`.

La macro **`defineModel()`** simplifie drastiquement cette syntaxe :

```vue
<!-- Dans le composant Enfant -->
<script setup lang="ts">
const isOpen = defineModel<boolean>('isOpen', { default: false })
</script>

<!-- Dans le composant Parent -->
<template>
  <AppModal v-model:isOpen="isModalOpen" title="Créer une tâche" />
</template>
```

---

## 🎬 3. Micro-animations avec `<TransitionGroup>`

Dans un tableau Kanban ou une liste de tâches, l'ajout, le déplacement ou la suppression d'une tâche doivent être visuellement fluides grâce aux transitions automatiques de Vue :

```vue
<template>
  <TransitionGroup name="task-list" tag="div" class="cards-container">
    <TaskCard v-for="task in tasks" :key="task.id" :task="task" />
  </TransitionGroup>
</template>

<style scoped>
.task-list-move,
.task-list-enter-active,
.task-list-leave-active {
  transition: all 0.3s ease;
}
.task-list-enter-from,
.task-list-leave-to {
  opacity: 0;
  transform: translateY(15px);
}
</style>
```

---

## 🛠️ 4. Atelier Pratique : Fil Rouge "DevFlow" (Étape 3)

### Votre mission chez DevSquad :

1. **Créer le composant `app/components/common/AppModal.vue`** :
   * Gérer l'ouverture/fermeture via `defineModel<boolean>('isOpen')`.
   * Utiliser `<Teleport to="body">` pour injecter la boîte de dialogue à la racine du DOM.
   * Proposer un slot principal pour le formulaire et un slot optionnel `footer` pour les boutons d'action.
   * Ajouter une animation d'apparition douce (`<Transition>`).

2. **Créer le composant `app/components/tasks/TaskCard.vue`** :
   * Props : `task: Task`.
   * Emits : `update-status(id, newStatus)` et `delete(id)`.
   * Affichage : identifiant, badge de priorité stylisé, titre cliquable vers `/tasks/${task.id}`, description abrégée, tags, avatar du responsable et menu de changement rapide de statut.

3. **Créer le composant `app/components/tasks/TaskColumn.vue`** :
   * Props : `status: TaskStatus`, `title: string`, `tasks: Task[]`.
   * Emits : `update-status` et `delete`.
   * Affichage : en-tête avec titre et compteur de tâches, liste des cartes animée avec `<TransitionGroup>`, état vide (*empty state*) élégant.

4. **Assembler le Dashboard Kanban dans `app/pages/tasks/index.vue`** :
   * Disposer les 4 colonnes côte à côte : *📌 À Faire*, *⚡ En Cours*, *👀 En Revue*, *✅ Terminé*.
   * Ajouter un bouton d'action *"+ Nouvelle Tâche"* qui ouvre la modal.
   * Gérer la création d'une tâche et l'insertion immédiate dans la liste réactive via `useTasks().addTask()`.

5. **Auditer la qualité TypeScript** :
   * Exécuter `npx vue-tsc --noEmit` dans le terminal et corriger les éventuelles erreurs de typage.

{% hint style="tip" %}
La solution détaillée et commentée de cet atelier est disponible dans la page [Séance 5 (correction)](seance-5-correction.md).
{% endhint %}

---

## 🎯 Auto-évaluation & Checklist

* [ ] J'ai compris le fonctionnement de `defineModel()` pour créer des composants personnalisés avec `v-model`.
* [ ] J'ai découpé mon interface en composants modulaires `AppModal`, `TaskCard` et `TaskColumn`.
* [ ] Mon tableau Kanban anime fluidement l'ajout et le déplacement de tâches.
* [ ] La commande `npx vue-tsc --noEmit` s'exécute sans aucune erreur.
