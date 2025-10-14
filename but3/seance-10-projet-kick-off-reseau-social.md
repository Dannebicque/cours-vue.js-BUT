# Séance 10 – Projet – Kick-off réseau social

## 🎯 Objectifs
- Lancer projet Nuxt/VueJs Social App.
- Mettre en place authentification + channels.

## 📖 Partie théorique

### Présentation du projet

- Mini-réseau social type Slack/Discord/...
- Fonctionnalités principales : channels, publications (et réponses), authentification, système de like.

### Architecture

- Frontend : Nuxt ou VueJs
- Backend : API Platform (fourni)

### Endpoints principaux

- `/auth/login` : authentification
- `/channels` : gestion des salons
- `/publications?channelId=...` : messages filtrés par salon
- ... (voir documentation API fournie)

## 📝 Travaux pratiques

- Créer projet Nuxt/VueJs Social App.
- Mettre en place les différentes pages (login, channels, publications, ...).
- Toutes les fonctionnalités utiles et nécessaires pour ce type d'application
- Implémenter l'authentification et toutes les fonctionnalités utiles dans ce processus (login, logout, gestion du token, mot de passe perdu, inscription, ...).

## Evaluation du projet

- Critères : fonctionnalités, qualité du code, UI/UX, tests, accessibilité.
- Livrables : code source (sur un git), démonstration (capture vidéo).
