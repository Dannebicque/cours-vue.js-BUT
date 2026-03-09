# Séance 10 : Projet – Kick-off réseau social

## 🎯 Objectifs

* Lancer projet Nuxt/VueJs Social App.
* Mettre en place authentification + channels.

## 📖 Partie théorique

### Présentation du projet

* Mini-réseau social type Slack/Discord/...
* Fonctionnalités principales : channels, publications (et réponses), authentification, système de like.

### Architecture

* Frontend : Nuxt ou VueJs
* Backend : API Platform (fourni)

### Endpoints principaux

* `/auth/login` : authentification
* `/channels` : gestion des salons
* `/publications?channelId=...` : messages filtrés par salon
* ... (voir documentation API fournie)

## 📝 Travaux pratiques

* Créer projet Nuxt/VueJs Social App.
* Mettre en place les différentes pages (login, channels, publications, ...).
* Toutes les fonctionnalités utiles et nécessaires pour ce type d'application
* Implémenter l'authentification et toutes les fonctionnalités utiles dans ce processus (login, logout, gestion du token, mot de passe perdu, inscription, ...).

## Evaluation du projet

* Critères : fonctionnalités, qualité du code, UI/UX, tests, accessibilité.
  * Mise en place de tests unitaires (un service, un composant ou un store)
  * Mise en place d'un test ent-to-end (la page de login, l'ajout d'une publication)
* Livrables : code source (sur un git), démonstration.

{% hint style="danger" %}
Rendu le 15/03/2026, par mail :&#x20;

* Github
* Site en ligne (fournir des accès de démonstration) ou vidéo de démonstration des fonctionnalités
{% endhint %}

### Grille d'évaluation

| Critères                                                   |   | Points                                                 |
| ---------------------------------------------------------- | - | ------------------------------------------------------ |
| Tests unitaires (service, composant ou store)              |   | 2                                                      |
| Tests E2E (page login + ajout d'une publication)           |   | 2                                                      |
| Accessibilité (AA)                                         |   | 1                                                      |
| UX (ergonomie, facilité d'utilisation, cohérence)          |   | 0. non/absent, 1. OK, dans la consigne. 2 Exceptionnel |
| Authentification, création de compte, mot de passe perdu   |   | 2 (tout présent ou pas)                                |
| Ajouter, modification, suppression d'une publication       |   | 0. non/absent, 1. OK, dans la consigne. 2 Exceptionnel |
| Gestion des réactions publications, commentaires           |   | 2                                                      |
| Gestion des médias                                         |   | 2                                                      |
| Gestion des commentaires (ajouter, suppression, affichage) |   | 0. non/absent, 1. OK, dans la consigne. 2 Exceptionnel |
| Gestion des channels                                       |   | 2                                                      |
| Recherche/filtres                                          |   | 0. non/absent, 1. OK, dans la consigne. 2 Exceptionnel |

## Lien de l'API fournie

L'API est commune à tous les groupes et dispose des endpoints REST classiques pour chaque ressource. Ainsi pour gérer les accès sans mélanger vos données un "slug" unique vous sera attribué (fourni par l'enseignant) et à utiliser dans toutes les URLs

* Base URL : `https://wra506d.davidannebicque.ovh/api/{votre-slug}/...`

Un code d'inscription vous sera également fourni pour créer des comptes utilisateurs (à intégrer dans dans vos formulaires d'inscription).

La base de données est pré-remplie avec des utilisateurs, channels et publications pour les tests. Libre à vous d'ajouter/modifier/supprimer des données via l'API.

Une interface Swagger est également disponible pour explorer/tester l'API : `https://wra506d.davidannebicque.ovh/api` et une interface vous permet de récupérer un token JWT pour un utilisateur existant : `https://wra506d.davidannebicque.ovh/`.

## URL globales

* **Connexion API** : `https://wra506d.davidannebicque.ovh/api/login` en envoyant en post les champs `email` et `password`, vous donnera un token JWT à utiliser dans les headers `Authorization: Bearer {token}` pour les appels suivants.
* **Récupération de l'utilisateur courant** : `https://wra506d.davidannebicque.ovh/api/{slug}/users/me` avec le token JWT dans les headers, vous donnera les informations de l'utilisateur connecté.
* **Inscription API** (sans slug) : `https://wra506d.davidannebicque.ovh/api/register` en envoyant en post les champs `displayName`, `email`, `password` et `codeInscription`, vous permettra de créer un nouvel utilisateur dans le bon workspace (slug).
* **Inscription API** (avec slug) : `https://wra506d.davidannebicque.ovh/api/{slug}/register` en envoyant en post les champs `displayName`, `email`, `password`, vous permettra de créer un nouvel utilisateur dans le bon workspace (slug).

## Structure de l'API REST

### Channel (/api/channels)

* name : string
* slug : string
* tags : tableau, optionnel
* description: text, optionnel
* image : text (url), optionnel

### Publication (/api/publications)

* Author : User (géré par l'API)
* Channel : Channel (à transmettre sous forme d'IRI)
* title : string
* body : string (contenu)
* createdAt : datetime (géré par l'API)
* updatedAt : datetime (géré par l'API)

### Comment (/api/comments)

* Author : User (géré par l'API)
* Publication : Publication (à transmettre sous forme d'IRI)
* body : string (contenu)
* createdAt : datetime (géré par l'API)
* updatedAt : datetime (géré par l'API)

### User (/api/users)

* displayName : string
* email : string
* password : string (non retourné par l'API)
* avatar : string (URL de l'avatar)
* createdAt : datetime (géré par l'API)

### Reaction (/api/reactions)

* type : string (like, love, etc.)
* Author : User (géré par l'API)
* publication : Publication (optionnel)
* comment : Comment (optionnel)
* createdAt : datetime (géré par l'API)

### Media (/api/media)

* owner : User (géré par l'API)
* originalName : string
* mimeType : string
* size : integer (taille en octets)
* path : string (URL du média)
* createdAt : datetime (géré par l'API)
* publication : Publication (optionnel)
* comment : Comment (optionnel)

## Appel API avec un token JWT

### en VueJs avec fetch

```javascript
const token = 'votre_token_jwt_ici';

fetch('https://wra506d.davidannebicque.ovh/api/ws-xx/channels', {
  headers: {
    'Authorization': `Bearer ${token}`
  }
})
.then(response => response.json())
.then(data => console.log(data));
```

### en NuxtJs avec useFetch

```javascript
const token = 'votre_token_jwt_ici';

const { data, error } = await useFetch('https://wra506d.davidannebicque.ovh/api/ws-xx/channels', {
  headers: {
    'Authorization': `Bearer ${token}`
  }
});
```
