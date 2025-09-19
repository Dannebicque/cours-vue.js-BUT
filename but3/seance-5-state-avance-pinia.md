# Séance 5 – State avancé (Pinia)

## 🎯 Objectifs
- Aller plus loin avec Pinia.
- Persister l’état dans le navigateur.

## 📖 Partie théorique

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

