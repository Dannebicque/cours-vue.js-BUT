# Séance 1 – Introduction & rappel Vue

## 🎯 Objectifs
- Vérifier et réactiver les acquis de BUT2.
- Mettre en place un projet clean (environnement, conventions).

## 📖 Partie théorique
### Rappel Vue 3 : Composition API
- Utilisation de `ref`, `reactive`, `computed`, `watch` pour la réactivité.

### Props, emits et slots
- Mécanismes de communication entre composants :
	- `props` pour transmettre des données du parent à l'enfant.
	- `emits` pour remonter des événements de l'enfant au parent.
	- `slots` pour l'injection de contenu personnalisé.

### Rôle d’un store global
- Permet d'éviter le prop drilling (passage de props sur plusieurs niveaux).
- Centralise l'état partagé entre plusieurs composants.

### Introduction Pinia et comparaison avec Vuex
- Pinia : store officiel pour Vue 3, basé sur la Composition API.
- Plus simple, typé, et performant que Vuex.
- Vuex : historique, utilisé avec l'Option API, moins adapté à Vue 3.

## 💻 Exemples
```js
const count = ref(0)
const double = computed(() => count.value * 2)
watch(count, (nv, ov) => console.log(nv, ov))
```
```vue
<!-- Parent.vue -->
<TaskCard :title="'Ma tâche'" @delete-task="onDelete" />
```

## 📝 Travaux pratiques
- Initialiser projet avec `create-vue`.
- Créer `TaskCard.vue` (props simples).
- Mettre en place `useTasksStore` avec ajout/suppression de tâches.

