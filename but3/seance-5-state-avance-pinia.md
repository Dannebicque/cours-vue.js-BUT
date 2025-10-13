# Séance 5 – State avancé (Pinia)

## 🎯 Objectifs
- Mettre en place un store global avec Pinia.
- Persister l’état dans le navigateur.

## 📖 Qu'est-ce qu'un store en Vue.js ?

Un **store** est un conteneur centralisé pour l'état (données) partagé d'une application. Plutôt que de faire transiter des données via des props entre de nombreux composants (prop drilling), le store fournit une "source unique de vérité" (single source of truth) accessible depuis n'importe quel composant.

### Objectifs pédagogiques de cette section

- Comprendre le rôle d'un store dans une application SPA.
- Connaître les concepts clés (state, getters, actions, mutations/actions selon l'implémentation).
- Savoir quand et pourquoi utiliser un store.

### Principaux concepts

- State : l'ensemble des données partagées (par ex. utilisateur courant, liste d'items, paramètres UI).
- Getters (ou computed) : fonctions dérivées qui lisent le state et renvoient des valeurs calculées.
- Actions/Mutations : fonctions (souvent asynchrones) qui modifient le state ou appellent des APIs.

### Pourquoi utiliser un store ?

- Centralisation : évite les incohérences et facilite le debug.
- Réutilisabilité : plusieurs composants peuvent lire/écrire le même état sans duplication, cela réduit les appels de données.
- Prévisibilité : les changements d'état sont centralisés et prévisibles.
- Testabilité : la logique métier est isolée dans des actions testables.
- Persistance : simple à combiner avec localStorage/IndexedDB pour sauvegarder la session.

### Bonnes pratiques

- Garder le store simple : ne pas y placer du code UI.
- Nommer clairement les getters/actions.
- Gérer les erreurs et les états de chargement dans le store.

### Edge cases / pièges

- Ne pas tout placer dans le store : certains états restent locaux au composant (e.g. champ de formulaire non validé).
- Persistance : attention aux champs sensibles (tokens) — stocker en localStorage nécessite des précautions de sécurité.

## Pinia 

Pinia (https://pinia.vuejs.org/) est le store officiel recommandé pour Vue 3. Il s'appuie sur la Composition API et propose une API légère et expressive.

Points forts de Pinia :

- API simple et proche de la Composition API (hooks-like).
- Support natif du typage TypeScript.
- Plugins faciles à ajouter (persist, devtools, plugins pour SSR).
- Pas de notion lourde de "mutations" : les actions modifient directement le state (moins de verbosité qu'avec Vuex).

Cas d'usage typiques :

- Authentification et gestion de session.
- Données partagées entre vues (panier, préférences utilisateur).
- Caching d'appels API et logique métier centralisée.

## Exemple d'un store Pinia

```javascript
// stores/auth.js
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const useAuthStore = defineStore('auth', () => {
  const user = ref(null)
  const token = ref(null)
  const loading = ref(false)
  const error = ref(null)

  const isAuthenticated = computed(() => !token.value)

  const login = async (credentials) => {
    loading.value = true
    error.value = null
    try {
      // Exemple simplifié - remplacer par appel réel
      await new Promise(resolve => setTimeout(resolve, 500))
      token.value = 'fake-jwt-token'
      user.value = { id: 1, name: 'Alice' }
      
      // Persister (on verra plus tard avec un plugin)
      localStorage.setItem('token', token.value)
      localStorage.setItem('user', JSON.stringify(user.value))
      return true
    } catch (err) {
      error.value = 'Erreur de connexion'
      return false
    } finally {
      loading.value = false
    }
  }

  const logout = () => {
    token.value = null
    user.value = null
    localStorage.removeItem('token')
    localStorage.removeItem('user')
  }

  return {
    user,
    token,
    loading,
    error,
    isAuthenticated,
    login,
    logout
  }
})
```

**Utilisation dans un composant :**

```javascript
<script setup>
import { useAuthStore } from '@/stores/auth'

const auth = useAuthStore()

const handleLogin = async () => {
  const ok = await auth.login({ email: 'alice@example.com', password: 'secret' })
  if (!ok) {
    alert(auth.error)
  }
}
</script>
```

### Stores, composables et services

Il est important de distinguer les rôles des stores, des composables et des services dans une application Vue.js :

- **Store** : conteneur d’état global réactif (single source of truth) partagé par plusieurs composants (ex. Pinia).
- **Service** : module / classe responsable d’une logique métier ou d’accès aux APIs ; souvent non‑réactif et sans état UI.
- **Composable** : fonction réutilisable basée sur la Composition API qui encapsule de la logique réactive liée à un comportement ou une fonctionnalité.

## Tableau de synthèse — store vs service vs composable

Voici un tableau compact pour comparer rapidement les trois concepts.

| Concept     | But principal | Portée / cycle de vie | Réactivité | Accès depuis | Exemple court | Quand l'utiliser |
|-------------|---------------|-----------------------|-----------:|--------------|---------------|------------------|
| Store (Pinia) | Contenir et partager l'état applicatif (single source of truth) | Global, vie de l'app, persistable | Oui (state réactif, getters) | Tous composants, composables, services | `useAuthStore()` (user, token, actions) | État partagé (auth, panier, préférences), besoin de persistance/consistance |
| Service | Encapsuler la logique métier / appels API | Module réutilisable, généralement stateless | Non (retourne Promises/données) | Stores, composables, composants | `taskService.fetchTasks()` (fetch/axios wrapper) | Isolation des appels API et logique métier, tests unitaires |
| Composable | Factoriser de la logique réactive liée à une fonctionnalité | Scoped / feature-level, réutilisable | Oui (utilise `ref`/`reactive`) | Composants et autres composables | `useToggle()`, `useForm()` | Logique UI réutilisable, hooks locaux, encapsulation de comportements |

### Recommandations rapides

- Architecture recommandée : Services (API) → Stores (état & cache) → Composables (logique UI réactive).  
- Tests : tester les services (unitaires), les actions de store (intégration/unité) et les composables (unitaires).  
- Persistance & sécurité : gérer la persistance dans les stores ou via plugins dédiés ; ne pas stocker de secrets sans précautions.

### Persister l'état avec Pinia

Pinia propose des plugins pour persister automatiquement l'état dans le `localStorage` ou `sessionStorage`. Voici un exemple avec `pinia-plugin-persistedstate` (https://prazdevs.github.io/pinia-plugin-persistedstate/).

```javascript
// main.js
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import piniaPersistedstate from 'pinia-plugin-persistedstate'
import App from './App.vue'
const app = createApp(App)
const pinia = createPinia()
pinia.use(piniaPersistedstate)
app.use(pinia)
app.mount('#app')
```

```javascript
// stores/auth.js
import { defineStore } from 'pinia'
import { ref } from 'vue'

export const useAuthStore = defineStore('auth', () => {
  const user = ref(null)
  const token = ref(null)

  function login(newUser, newToken) {
    user.value = newUser
    token.value = newToken
  }

  function logout() {
    user.value = null
    token.value = null
  }

  return {
    user,
    token,
    login,
    logout
  }
}, {
  persist: true // active la persistance automatique
})
```

```javascript
// Dans un composant
<script setup>
import { useAuthStore } from '@/stores/auth'
const auth = useAuthStore()
</script>
```

Avec cette configuration, l'état du store `auth` sera automatiquement sauvegardé dans le `localStorage` et restauré au rechargement de la page.

## Exercice

- Créez un store Pinia pour gérer une liste de tâches (CRUD).
- Ajoutez la persistance dans le `localStorage`.
- Utilisez ce store dans un composant pour afficher et modifier les tâches.
- Indiquez sur les différentes pages le nombre de tâches restantes (getter).
