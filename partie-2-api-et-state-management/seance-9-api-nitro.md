# Séance 9 : API Server Nitro & Routes `server/`

## 🎯 Objectifs de la séance

* Comprendre le rôle du moteur serveur **Nitro** intégré à Nuxt 4.
* Concevoir des routes d'API REST fullstack dans le dossier `server/api/`.
* Gérer les méthodes HTTP (`GET`, `POST`, `PATCH`, `DELETE`) avec les conventions de nommage (`.get.ts`, `.post.ts`).
* Valider les payloads entrants avec `readBody()`, `getQuery()` et `createError()`.
* **Fil rouge DevFlow** : Implémenter le backend interne complet pour la gestion des tâches.

---

## ⚡ 1. Introduction à Nitro

**Nitro** est le moteur de serveur web nouvelle génération qui propulse Nuxt. Il transforme votre projet front-end en une application véritablement fullstack sans avoir besoin d'installer un serveur Express ou Fastify séparé.

```
server/
 ├── api/
 │   ├── tasks/
 │   │   ├── index.get.ts        # GET /api/tasks (Liste)
 │   │   ├── index.post.ts       # POST /api/tasks (Création)
 │   │   ├── [id].get.ts         # GET /api/tasks/:id (Détail)
 │   │   ├── [id].patch.ts       # PATCH /api/tasks/:id (Mise à jour)
 │   │   └── [id].delete.ts      # DELETE /api/tasks/:id (Suppression)
 │   └── auth/
 │       └── login.post.ts       # POST /api/auth/login
 └── middleware/
     └── log.ts                  # Middleware HTTP serveur
```

---

## 🛠️ 2. Écriture des Handlers Serveur (`defineEventHandler`)

### A. Route GET avec paramètres de requête : `server/api/tasks/index.get.ts`

```typescript
// server/api/tasks/index.get.ts
import type { Task } from '~/types/devflow'

// Base de données en mémoire (ou connexion ORM Prisma/Drizzle)
let tasksDatabase: Task[] = [
  {
    id: 'TASK-1',
    title: 'Initialiser le dépôt GitHub',
    description: 'Structure de base',
    status: 'done',
    priority: 'high',
    assignee: null,
    tags: ['Setup'],
    estimatedHours: 1,
    createdAt: new Date().toISOString()
  }
]

export default defineEventHandler((event) => {
  // Récupération des query parameters (ex: /api/tasks?status=done)
  const query = getQuery(event)

  if (query.status) {
    return tasksDatabase.filter(t => t.status === query.status)
  }

  return tasksDatabase
})
```

### B. Route POST avec validation du body : `server/api/tasks/index.post.ts`

```typescript
// server/api/tasks/index.post.ts
import type { Task } from '~/types/devflow'

export default defineEventHandler(async (event) => {
  // Lecture sécurisée du corps de la requête
  const body = await readBody<Partial<Task>>(event)

  // Validation minimale
  if (!body.title || !body.priority) {
    throw createError({
      statusCode: 400,
      statusMessage: 'Le titre et la priorité sont obligatoires.'
    })
  }

  const newTask: Task = {
    id: `TASK-${Date.now().toString().slice(-4)}`,
    title: body.title,
    description: body.description || '',
    status: body.status || 'todo',
    priority: body.priority,
    assignee: body.assignee || null,
    tags: body.tags || [],
    estimatedHours: body.estimatedHours || 1,
    createdAt: new Date().toISOString()
  }

  // Sauvegarde
  tasksDatabase.unshift(newTask)

  // Code HTTP 201 Created
  setResponseStatus(event, 201)
  return newTask
})
```

---

## 🛠️ 3. Travaux Pratiques

1. Créer le dossier `server/api/tasks/` dans votre projet Nuxt 4.
2. Implémenter les 4 endpoints CRUD complets (`index.get.ts`, `index.post.ts`, `[id].patch.ts`, `[id].delete.ts`).
3. Tester les endpoints avec Nuxt DevTools, Postman ou `curl`.
4. Raccorder le composable front-end `useTasks` ou le store Pinia directement sur ces routes internes `/api/tasks`.
