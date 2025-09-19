# Séance 2 – Architecture et composables

## 🎯 Objectifs
- Comprendre la structuration d’un projet Vue.
- Découvrir la factorisation de logique avec les composables.

## 📖 Partie théorique

### Organisation du projet

- Dossiers principaux :
  - `components/` : composants Vue réutilisables
  - `stores/` : gestion d’état globale (Pinia)
  - `composables/` : fonctions réactives réutilisables
  - `services/` : accès aux API et logique métier
  - `views/` : pages principales de l’application

### Composables

- Fonctions réutilisables exploitant la réactivité de Vue.
- Permettent de factoriser la logique métier et d’éviter la duplication de code.

### Design patterns

- Smart vs Dumb components :
  - Smart : gèrent la logique, les données et l’état
  - Dumb : affichent uniquement les données reçues
- Container vs Presentational components :
  - Container : connectés au store, gèrent la logique
  - Presentational : affichent l’UI, reçoivent les props

## 💻 Exemples
```js
export function useCounter() {
  const count = ref(0)
  const inc = () => count.value++
  return { count, inc }
}
```
```vue
<!-- Smart: Board.vue -->
<TaskCard v-for="t in tasks" :task="t" />
```

## 📝 Travaux pratiques
- Refactor TP précédent avec un composable `useTasks`.
- Ajouter `useCounter` pour pratiquer.

