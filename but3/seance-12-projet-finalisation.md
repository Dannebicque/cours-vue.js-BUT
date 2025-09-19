# Séance 12 – Projet – Finalisation

## 🎯 Objectifs
- Améliorer UX (réactions, thèmes).
- Finaliser application et préparer démo.

## 📖 Partie théorique

### Réactions sur messages

- Endpoint : `POST /messages/{id}/reactions` pour ajouter des réactions.
- Affichage des réactions sur chaque message.

### Dark mode avec Tailwind

- Utilisation de la configuration Tailwind pour activer le mode sombre.
- Ajout d’un toggle pour basculer entre les modes.

### Bonnes pratiques

- Factorisation des composables et stores pour faciliter la maintenance.
- Organisation du code pour la réutilisabilité et la clarté.

## 💻 Exemples
```js
// Tailwind config
module.exports = {
  darkMode: 'class'
}
```

## 📝 Travaux pratiques
- Ajouter réactions (👍 ❤️).
- Dark/light mode avec toggle.
- Démo interne entre groupes.

