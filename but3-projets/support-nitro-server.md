# Support : Nitro Server

Deux sources :&#x20;

[Documentation Nitro – Quick Start](https://nitro.build/docs/quick-start?utm_source=chatgpt.com)\
[Documentation Nuxt – Server](https://nuxt.com/docs/4.x/getting-started/server?utm_source=chatgpt.com)

## Introduction à Nitro avec Nuxt

Dans Nuxt, **Nitro est le moteur de la partie serveur**.

> **Nitro est le moteur serveur utilisé par Nuxt. Il permet notamment de créer des API et d'exécuter du code uniquement côté serveur.**

***

## Premier exemple Nitro

Créons :

```
server/
└── api/
    └── hello.ts
```

avec :

```
export default defineEventHandler(() => {
  return {
    message: 'Hello depuis le serveur !'
  }
})
```

Nuxt/Nitro transforme automatiquement ce fichier en :

```
GET /api/hello
```

C'est une caractéristique importante de Nitro : les fichiers définissent les routes (comme pour les pages de Nuxt). Nuxt analyse automatiquement `server/api`, `server/routes`, `server/middleware`, etc.

Depuis une page Vue :

```
<script setup lang="ts">
const { data } = await useFetch('/api/hello')
</script>

<template>
  <p>{{ data?.message }}</p>
</template>
```

Architecture :

```
┌───────────────────────────────┐
│         Navigateur            │
│                               │
│ Vue / Nuxt                    │
│                               │
│ useFetch('/api/hello')        │
└───────────────┬───────────────┘
                │ HTTP
                ▼
┌───────────────────────────────┐
│            Nitro              │
│                               │
│ server/api/hello.ts           │
│                               │
│ return { message: "..." }     │
└───────────────────────────────┘
```

**nous venons de créer une API sans Express, Symfony ou API Platform.**

{% hint style="info" %}
Notez au passage l'usage de useFetch et non pas fetch : [https://nuxt.com/docs/4.x/api/composables/use-fetch](https://nuxt.com/docs/4.x/api/composables/use-fetch)
{% endhint %}

## Ce que fait `defineEventHandler`

Analysons le code :

```
export default defineEventHandler((event) => {

})
```

Une requête arrive :

```
GET /api/hello
```

Nitro crée un **événement HTTP** :

```
HTTP Request
     │
     ▼
   event
     │
     ▼
defineEventHandler()
     │
     ▼
HTTP Response
```

Nitro repose notamment sur [**h3**](https://www.cloudflare.com/fr-fr/learning/performance/what-is-http3/), la couche HTTP utilisée pour les handlers serveur.&#x20;

## Retourner du JSON

Un point volontairement très simple :

```
export default defineEventHandler(() => {
  return {
    id: 1,
    name: 'Ada Lovelace'
  }
})
```

Pas besoin de :

```
JSON.stringify(...)
```

ni de construire manuellement :

```
Content-Type: application/json
```

Le handler peut directement retourner un objet ou un tableau et Nitro gère la réponse HTTP.

On peut faire :

```
export default defineEventHandler(() => {
  return [
    { id: 1, name: 'Ada' },
    { id: 2, name: 'Alan' },
    { id: 3, name: 'Grace' }
  ]
})
```

Puis :

```
<script setup lang="ts">
const { data: users } = await useFetch('/api/users')
</script>
```

## Comprendre `server/api`

Comme le routage de Nuxt, il est possible d'avoir des syntaxes du type :

```
server/
└── api/
    ├── users.get.ts
    ├── users.post.ts
    └── users/
        └── [id].get.ts
```

Ce qui permet conceptuellement d'obtenir :

```
GET  /api/users
POST /api/users
GET  /api/users/42
```

En se basant sur l'API REST :

```
GET       lire
POST      créer
PUT       remplacer
PATCH     modifier
DELETE    supprimer
```

## Exemple avec paramètre

```
server/api/users/[id].get.ts
```

```
export default defineEventHandler((event) => {

  const id = getRouterParam(event, 'id')

  return {
    id,
    name: `Utilisateur ${id}`
  }

})
```

Requête :

```
GET /api/users/42
```

Réponse :

```
{
  "id": "42",
  "name": "Utilisateur 42"
}
```

## Une requête POST

On introduit ensuite le body HTTP.

```
server/api/users.post.ts
```

```
export default defineEventHandler(async (event) => {

  const body = await readBody(event)

  return {
    message: 'Utilisateur créé',
    user: body
  }

})
```

Et côté Vue :

```
await $fetch('/api/users', {
  method: 'POST',

  body: {
    name: 'Ada',
    email: 'ada@example.com'
  }
})
```

On peut reconstruire le chemin suivant :

```
Vue
 │
 │ POST /api/users
 │
 │ { name: "Ada" }
 ▼
Nitro
 │
 │ readBody(event)
 ▼
Traitement
 │
 ▼
JSON
 │
 ▼
Vue
```

## Avantage : protéger des secrets

Mauvaise solution :

```
Navigateur
 │
 │ API_KEY
 ▼
API externe
```

Bonne solution :

```
Navigateur
 │
 │ /api/weather
 ▼
Nitro
 │
 │ API_KEY 🔐
 ▼
API météo
```

Par exemple :

```
export default defineEventHandler(async () => {

  const config = useRuntimeConfig()

  return await $fetch('https://api.example.com/weather', {
    headers: {
      Authorization: `Bearer ${config.weatherApiKey}`
    }
  })

})
```

Le navigateur connaît :

```
/api/weather
```

mais **jamais `weatherApiKey`**.

## Nitro comme Backend For Frontend (BFF)

Imaginons :

```
                ┌─────────────┐
                │ API météo   │
                └──────▲──────┘
                       │
                       │
┌─────────┐      ┌─────┴─────┐
│   Vue   │─────►│   Nitro   │
│  Nuxt   │      │    BFF    │
└─────────┘      └─────┬─────┘
                       │
                       ▼
                ┌─────────────┐
                │ API Spotify │
                └─────────────┘
```

Nitro peut :

```
appeler plusieurs API
        ↓
transformer les données
        ↓
filtrer les données
        ↓
ajouter de l'authentification
        ↓
mettre en cache
        ↓
renvoyer exactement ce dont Vue a besoin
```

On passe alors de :

```
Frontend → plein d'API
```

à :

```
Frontend
   ↓
Nitro
 ↓ ↓ ↓
services
```

***

## Mais alors… Nitro remplace Symfony ?

**Pas nécessairement.**

Architecture classique avec Symfony/ApiPlatform :

```
Vue / Nuxt
     │
     │ HTTP
     ▼
Symfony / API Platform
     │
     ▼
Base de données
```

Avec Nitro :

```
Vue
 │
 ▼
Nitro
 │
 ▼
BDD
```

est parfaitement possible.

Mais aussi :

```
Vue
 │
 ▼
Nitro
 │
 ▼
Symfony / API Platform
 │
 ▼
BDD
```

Pourquoi faire cela ?

Nitro devient le **BFF** :

```
                 Symfony
                    ▲
                    │
Vue ───► Nitro ─────┼────► API externe
                    │
                    └────► autre service
```

***

## Quand utiliser quoi ?

| Besoin                                  | Solution possible    |
| --------------------------------------- | -------------------- |
| Petite API liée à l'application Nuxt    | Nitro                |
| Masquer une clé API                     | Nitro                |
| Proxy vers une API externe              | Nitro                |
| BFF                                     | Nitro                |
| Formulaire simple                       | Nitro                |
| Endpoint léger                          | Nitro                |
| Application métier complexe             | Symfony              |
| Domaine métier important                | Symfony              |
| Beaucoup d'entités / relations          | Symfony + Doctrine   |
| API partagée par plusieurs applications | Symfony / API dédiée |

## Les middleware serveur

```
server/
└── middleware/
    └── log.ts
```

```
export default defineEventHandler((event) => {
  console.log(
    'Requête :',
    getRequestURL(event).pathname
  )
})
```

Celui-ci est exécuté avant les routes serveur. La documentation Nuxt distingue d'ailleurs clairement ces middleware serveur des route middleware de la partie Vue/Nuxt.

Architecture :

```
Request
   │
   ▼
middleware
   │
   ▼
route API
   │
   ▼
Response
```

On peut leur demander :

> À quoi pourrait servir un middleware ?

```
logs
authentification
contrôle d'accès
headers
mesure de performances
contexte utilisateur
```

## Et la base de données ?

Je ne la développerais pas forcément dans le premier cours, mais je montrerais que ceci devient possible :

```
Vue
 │
 ▼
Nitro
 │
 ▼
ORM / driver
 │
 ▼
PostgreSQL
```

Donc une application Nuxt peut réellement devenir :

```
┌─────────────────────────────────────┐
│                Nuxt                 │
│                                     │
│  ┌──────────────┐ ┌──────────────┐ │
│  │     Vue      │ │    Nitro     │ │
│  │              │ │              │ │
│  │ interface    │ │ API          │ │
│  │ composants   │ │ auth         │ │
│  │ pages        │ │ données      │ │
│  └──────────────┘ └───────┬──────┘ │
└────────────────────────────┼────────┘
                             │
                             ▼
                         Database
```

## Pourquoi Nitro existe séparément de Nuxt ?

Nitro n'est pas simplement « le dossier `server` de Nuxt ».

C'est un projet indépendant de l'écosystème JS :

```
             Nuxt
          ┌──────────┐
          │   Vue    │
          │          │
          │  Nitro   │
          └──────────┘
               ▲
               │
        moteur serveur
```

Nitro peut d'ailleurs être utilisé **sans Nuxt**. Son site le présente aujourd'hui comme un outil permettant de construire des serveurs déployables sur Node.js, Bun, Deno ou différentes plateformes serverless. [nitro.build](https://nitro.build/?utm_source=chatgpt.com)

C'est une distinction importante :

> **Nuxt utilise Nitro, mais Nitro n'a pas besoin de Nuxt.**

## Synthèse

```
                         NUXT

       CLIENT                         SERVEUR

┌────────────────────┐       ┌────────────────────┐
│                    │       │                    │
│       Vue          │       │       Nitro        │
│                    │       │                    │
│ pages              │ HTTP  │ API                │
│ components         │──────►│ middleware         │
│ composables        │       │ services           │
│                    │◄──────│ secrets            │
│                    │ JSON  │ accès données      │
└────────────────────┘       └─────────┬──────────┘
                                      │
                           ┌──────────┼──────────┐
                           ▼          ▼          ▼
                          BDD      Symfony     APIs
```

Avec **5 idées à retenir** :

1. **Nuxt n'est pas seulement Vue** : il possède une partie serveur.
2. **Nitro est le moteur serveur de Nuxt.**
3. `server/api/` permet de créer très simplement des endpoints HTTP.
4. Le code Nitro s'exécute côté serveur : il peut donc utiliser des **secrets, bases de données et services externes**.
5. Nitro peut servir de petit backend ou de **BFF devant un backend plus important comme Symfony**.
