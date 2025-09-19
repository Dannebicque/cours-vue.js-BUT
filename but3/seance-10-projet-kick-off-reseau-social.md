# Séance 10 – Projet – Kick-off réseau social

## 🎯 Objectifs
- Lancer projet Nuxt Social App.
- Mettre en place authentification + channels.

## 📖 Partie théorique

### Présentation du projet

- Mini-réseau social type Slack/Discord.
- Fonctionnalités principales : channels, messages, authentification.

### Architecture

- Frontend : Nuxt
- Backend : API Platform
- Base de données : relationnelle

### Endpoints principaux

- `/auth/login` : authentification
- `/channels` : gestion des salons
- `/messages?channelId=...` : messages filtrés par salon

## 💻 Exemples
```json
POST /auth/login
{
  "email": "alice@example.com",
  "password": "secret"
}
```

## 📝 Travaux pratiques
- Créer projet Nuxt Social App.
- Implémenter login/logout JWT.
- Afficher liste des channels.

