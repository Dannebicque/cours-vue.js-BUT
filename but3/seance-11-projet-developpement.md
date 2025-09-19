# Séance 11 – Projet – Développement

## 🎯 Objectifs
- Construire page channel avec messages.
- Gérer création et rafraîchissement des messages.

## 📖 Partie théorique

### Relations entre entités

- Channel → Messages → User
- Organisation des données et affichage par channel

### Polling simple

- Rafraîchissement régulier des messages pour simuler le temps réel.
- Utilisation de setInterval ou d’une fonction dédiée.

### UX

- Loaders pour indiquer le chargement
- Toasts pour les notifications
- Gestion des erreurs pour améliorer l’expérience utilisateur

## 💻 Exemples
```vue
<!-- ChannelPage.vue -->
<MessageCard v-for="m in messages" :message="m" />
```

## 📝 Travaux pratiques
- Créer page `/channel/:id`.
- Afficher messages d’un channel.
- Formulaire pour poster message.
- Rafraîchir via polling.

