# Séance 3 (correction) : Squelette et Layouts DevFlow

Ce document présente la correction détaillée de l'atelier pratique de la **Séance 3**.

---

## 🛠️ 1. Layout Principal (`app/layouts/default.vue`)

```vue
<!-- app/layouts/default.vue -->
<template>
  <div class="layout-default">
    <!-- Barre latérale fixe -->
    <aside class="sidebar">
      <div class="brand">
        <span class="logo">⚡</span>
        <h2>DevFlow</h2>
      </div>

      <nav class="menu">
        <NuxtLink to="/tasks" class="menu-link">
          <span>📋</span> Tableau Kanban
        </NuxtLink>
        <NuxtLink to="/team" class="menu-link">
          <span>👥</span> Membres de l'équipe
        </NuxtLink>
      </nav>

      <div class="user-panel">
        <div class="user-info">
          <p class="user-name">Alexandre Dubois</p>
          <span class="user-badge">Lead Tech</span>
        </div>
        <NuxtLink to="/login" class="logout-btn">Déconnexion</NuxtLink>
      </div>
    </aside>

    <!-- Zone principale avec Slot de contenu -->
    <main class="main-content">
      <header class="topbar">
        <h1>Espace Agence DevSquad</h1>
      </header>

      <div class="page-container">
        <!-- Le contenu de chaque page s'injecte ici -->
        <slot />
      </div>
    </main>
  </div>
</template>

<style scoped>
.layout-default {
  display: flex;
  min-height: 100vh;
}
.sidebar {
  width: 260px;
  background: #1e293b;
  border-right: 1px solid #334155;
  display: flex;
  flex-direction: column;
  padding: 24px 16px;
}
.brand {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 32px;
  font-size: 1.25rem;
  color: #38bdf8;
}
.menu {
  display: flex;
  flex-direction: column;
  gap: 8px;
  flex: 1;
}
.menu-link {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 10px 14px;
  color: #94a3b8;
  text-decoration: none;
  border-radius: 8px;
  font-weight: 500;
  transition: background 0.2s, color 0.2s;
}
.menu-link:hover, .menu-link.router-link-active {
  background: #334155;
  color: #f8fafc;
}
.user-panel {
  border-top: 1px solid #334155;
  padding-top: 16px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}
.user-name { font-weight: 600; font-size: 0.9rem; }
.user-badge { font-size: 0.75rem; color: #38bdf8; }
.logout-btn { color: #f87171; font-size: 0.8rem; text-decoration: none; }
.main-content {
  flex: 1;
  display: flex;
  flex-direction: column;
}
.topbar {
  padding: 16px 32px;
  background: #0f172a;
  border-bottom: 1px solid #334155;
}
.page-container {
  padding: 32px;
  flex: 1;
}
</style>
```

---

## 🛠️ 2. Layout Authentification (`app/layouts/auth.vue`)

```vue
<!-- app/layouts/auth.vue -->
<template>
  <div class="layout-auth">
    <div class="auth-box">
      <slot />
    </div>
  </div>
</template>

<style scoped>
.layout-auth {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: radial-gradient(circle at 50% 50%, #1e293b 0%, #0f172a 100%);
  padding: 20px;
}
.auth-box {
  width: 100%;
  max-width: 420px;
  background: #1e293b;
  border: 1px solid #334155;
  border-radius: 16px;
  padding: 32px;
  box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.5);
}
</style>
```

---

## 🛠️ 3. Page de Connexion (`app/pages/login.vue`)

```vue
<!-- app/pages/login.vue -->
<script setup lang="ts">
// Assignation du layout d'authentification
definePageMeta({
  layout: 'auth'
})

useSeoMeta({
  title: 'Connexion — DevFlow',
  description: 'Connectez-vous à votre espace agence DevSquad.'
})

const email = ref('alex@devsquad.pro')
const password = ref('secret')
const errorMessage = ref('')

const handleLogin = async () => {
  if (!email.value || !password.value) {
    errorMessage.value = 'Veuillez saisir vos identifiants.'
    return
  }

  // Redirection vers le tableau de bord
  await navigateTo('/tasks')
}
</script>

<template>
  <div class="login-container">
    <div class="login-header">
      <span class="logo">⚡</span>
      <h2>Connexion à DevFlow</h2>
      <p>Espace réservé aux collaborateurs DevSquad</p>
    </div>

    <form @submit.prevent="handleLogin" class="login-form">
      <div v-if="errorMessage" class="error-banner">
        {{ errorMessage }}
      </div>

      <div class="field">
        <label>Adresse e-mail</label>
        <input v-model="email" type="email" required placeholder="nom@devsquad.pro" />
      </div>

      <div class="field">
        <label>Mot de passe</label>
        <input v-model="password" type="password" required placeholder="••••••••" />
      </div>

      <button type="submit" class="btn-submit">
        Se connecter
      </button>
    </form>
  </div>
</template>

<style scoped>
.login-container { display: flex; flex-direction: column; gap: 24px; }
.login-header { text-align: center; }
.logo { font-size: 2.5rem; }
.login-header h2 { margin: 8px 0 4px; font-size: 1.5rem; }
.login-header p { color: #94a3b8; font-size: 0.875rem; }
.login-form { display: flex; flex-direction: column; gap: 16px; }
.field { display: flex; flex-direction: column; gap: 6px; }
.field label { font-size: 0.85rem; color: #94a3b8; }
.field input {
  background: #0f172a;
  border: 1px solid #334155;
  color: #f8fafc;
  padding: 10px 14px;
  border-radius: 8px;
}
.btn-submit {
  background: #38bdf8;
  color: #0f172a;
  border: none;
  font-weight: 700;
  padding: 12px;
  border-radius: 8px;
  cursor: pointer;
  margin-top: 8px;
}
.error-banner {
  background: #fee2e2;
  color: #991b1b;
  padding: 10px;
  border-radius: 8px;
  font-size: 0.85rem;
}
</style>
```

---

## 🛠️ 4. Page d'Erreur Personnalisée (`app/error.vue`)

```vue
<!-- app/error.vue -->
<script setup lang="ts">
import type { NuxtError } from '#app'

const props = defineProps<{
  error: NuxtError
}>()

const handleError = () => {
  clearError({ redirect: '/tasks' })
}
</script>

<template>
  <div class="error-page">
    <span class="error-code">{{ props.error.statusCode }}</span>
    <h1 class="error-title">
      {{ props.error.statusCode === 404 ? 'Page Introuvable' : 'Une erreur est survenue' }}
    </h1>
    <p class="error-message">{{ props.error.message }}</p>
    <button @click="handleError" class="btn-home">
      Retour au tableau de bord
    </button>
  </div>
</template>

<style scoped>
.error-page {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  background: #0f172a;
  color: #f8fafc;
  padding: 24px;
}
.error-code { font-size: 6rem; font-weight: 900; color: #38bdf8; line-height: 1; }
.error-title { font-size: 2rem; margin: 16px 0; }
.error-message { color: #94a3b8; margin-bottom: 32px; max-width: 500px; }
.btn-home {
  padding: 12px 24px;
  background: #38bdf8;
  color: #0f172a;
  border: none;
  border-radius: 8px;
  font-weight: 700;
  cursor: pointer;
}
</style>
```
