# Séance 8 – Nuxt avancé & SEO

## 🎯 Objectifs
- Approfondir middleware et SEO.
- Optimiser application Nuxt.

## 📖 Partie théorique (détaillée)

### Middleware : global, local et route middleware

Les middlewares permettent d'exécuter du code avant la navigation (auth, redirections, collecte analytics). Ils peuvent être :

- **Globaux** : appliqués à toutes les routes via un enregistrement global (ex : vérification d'auth à l'entrée de l'app).
- **Locaux** : définis pour une page / layout particulier (fichier `middleware/` importé dans la page).
- **Par route** : via `definePageMeta({ middleware: 'nom' })` ou `middleware: ['a','b']`.

Exemples :

```javascript
// middleware/auth.global.js
export default defineNuxtRouteMiddleware((to) => {
	const user = useState('user')
	if (!user.value) {
		return navigateTo('/login')
	}
})
```

```javascript
// pages/dashboard.vue
export default definePageMeta({ middleware: 'auth' })
```

Bonnes pratiques :

- Garder les middlewares rapides et idempotents (éviter des appels lourds).
- Utiliser des middlewares pour la sécurité et la redirection, pas pour du rendu lourd.

### SEO et meta tags (useHead)

Nuxt propose `useHead()` (via @unhead/vue) pour déclarer les meta tags côté composant/page, compatible SSR.

Exemple : title, description, Open Graph :

```javascript
export default {
	setup() {
		useHead({
			title: 'Page Profil - Mon App',
			meta: [
				{ name: 'description', content: 'Profil utilisateur - Mon App' },
				{ property: 'og:title', content: 'Profil utilisateur' },
				{ property: 'og:description', content: 'Voir le profil de l\'utilisateur' }
			]
		})
	}
}
```

Dynamic SEO : utiliser `useAsyncData` ou `useFetch` pour charger des données (titre/meta dynamiques) avant d'appeler `useHead`.

### Sitemaps, robots et indexation

- **Sitemap** : générer un sitemap.xml pour aider les moteurs (module `@nuxtjs/sitemap` ou génération custom via un endpoint server).
- **robots.txt** : définir les règles d'indexation (ex : bloquer les pages de staging).

Exemple d'endpoint simple pour sitemap :

```javascript
// server/api/sitemap.get.js
export default defineEventHandler(() => {
	const urls = [ '/', '/about', '/blog/post-1' ]
	const xml = `<?xml version="1.0" encoding="UTF-8"?>\n<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">${urls.map(u => `<url><loc>${u}</loc></url>`).join('')}</urlset>`
	return sendNoContent(event) // placeholder – en pratique renvoyer xml avec bon header
})
```

### Performance : lazy loading, code splitting et images

- **Lazy loading des composants** : importer dynamiquement les composants lourds (`() => import('...')`) ou utiliser `<client-only>` pour les parts purement client.
- **Code splitting** : Nuxt/Vite fait automatiquement le code splitting des pages; regrouper les routes en chunks si nécessaire.
- **Images** : utiliser le module image (nuxt/image) pour optimisation, lazy loading et formats modernes.

Exemple lazy component :

```html
<template>
	<LazyComp v-if="show" />
</template>

<script setup>
const LazyComp = defineAsyncComponent(() => import('@/components/HeavyChart.vue'))
const show = ref(false)
</script>
```

### SEO pour contenus dynamiques et revalidation

- Pour SSG, utiliser la revalidation/ISR si le contenu change régulièrement (Nuxt supporte des stratégies via Nitro ou modules).  
- Pour pages très dynamiques, préférer SSR ou Edge Rendering.

### Monitoring et bonnes pratiques SEO

- Ajouter balises Open Graph / Twitter Cards pour le partage social.
- Gérer canonical URLs pour éviter le contenu dupliqué.
- Tester avec Lighthouse et Search Console ; monitorer les pages importantes.

## 📝 Travaux pratiques

- Créer une page profil SSR (`useAsyncData`) et ajouter `useHead` dynamiquement.
- Générer un `sitemap.xml` simple via `server/api`.
- Optimiser une page produit avec image optimisation et lazy loading.
