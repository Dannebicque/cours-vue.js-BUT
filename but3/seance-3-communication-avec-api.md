# Séance 3 – Communication avec API

## 🎯 Objectifs
- Appeler une API externe.
- Gérer erreurs, loaders et services dédiés.

## 📖 Partie théorique

### Architecture client/serveur

- Vue/Nuxt côté client, API côté serveur.
- Communication via HTTP (REST, JSON).

### Fetch API vs Axios

- `fetch` : natif, simple, mais moins de fonctionnalités.
- `axios` : plus complet, gestion des interceptors, requêtes simplifiées.

### Gestion des erreurs

- Utilisation de `try/catch` pour capturer les erreurs réseau ou API.
- Affichage de messages d’erreur à l’utilisateur.

### DTO, normalisation et typage

- DTO (Data Transfer Object) : structure des données échangées.
- Normalisation : uniformiser les données reçues.
- Typage : sécuriser les échanges et faciliter la maintenance.

## 💻 Exemples
```js
import axios from 'axios'
const api = axios.create({ baseURL: '/api' })

export async function getTasks() {
  return (await api.get('/tasks')).data
}
```

## 📝 Travaux pratiques
- Créer un service `taskService.js`.
- Mettre en place une API mockée avec JSON Server.
- Connecter la liste de tâches au service API.

