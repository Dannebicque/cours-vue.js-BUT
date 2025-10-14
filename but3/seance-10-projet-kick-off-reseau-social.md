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


## Structure de l'API REST

### Channel (/api/channels)

- name : string
- slug : string

### Publication (/api/publications)

- Auteur : Member
- Channel : Channel
- titre : string
- body : string (contenu)
- createdAt : datetime
- updatedAt : datetime

### Comment (/api/comments)

- auteur : Member
- Publication : Publication
- body : string (contenu)
- createdAt : datetime
- updatedAt : datetime

### Member (/api/members)

- displayName : string
- email : string
- password : string (non retourné par l'API)
- avatar : string (URL de l'avatar)
- createdAt : datetime

### Reaction (/api/reactions)

- type : string (like, love, etc.)
- auteur : Member
- publication : Publication (optionnel)
- comment : Comment (optionnel)
- createdAt : datetime

### Media (/api/media)

- owner : Member
- originalName : string
- mimeType : string
- size : integer (taille en octets)
- path : string (URL du média)
- createdAt : datetime
- publication : Publication (optionnel)
- comment : Comment (optionnel)
