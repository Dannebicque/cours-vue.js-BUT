# Séance 5 – State avancé (Pinia)

## 🎯 Objectifs
- Aller plus loin avec Pinia.
- Persister l’état dans le navigateur.

## 📖 Partie théorique

### Rôle d’un store global
- Permet d'éviter le prop drilling (passage de props sur plusieurs niveaux).
- Centralise l'état partagé entre plusieurs composants.

### Introduction Pinia et comparaison avec Vuex
- Pinia : store officiel pour Vue 3, basé sur la Composition API.
- Plus simple, typé, et performant que Vuex.
- Vuex : historique, utilisé avec l'Option API, moins adapté à Vue 3.

### Stores complexes

- Actions asynchrones pour gérer les appels API.
- Modules imbriqués pour organiser l’état.

### Persistance de l’état

- Utilisation de `localStorage`, `sessionStorage`, ou `IndexedDB` pour sauvegarder l’état.
- Restauration automatique à l’ouverture de l’application.

### Synchronisation inter-onglets

- Utilisation de BroadcastChannel API pour synchroniser l’état entre plusieurs onglets ouverts.

## 💻 Exemples
```js
watch(() => user.value, (newVal) => {
  localStorage.setItem('user', JSON.stringify(newVal))
}, { deep: true })
```

## 📝 Travaux pratiques
- Persistance d’un store utilisateur (mock login).
- Synchronisation de la session entre plusieurs onglets.

