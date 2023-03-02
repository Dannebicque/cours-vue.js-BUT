# Séance 7 : Routes

Dans une application web classique, on utilise des URL pour changer le comportement de notre application, et le contenu des pages. Ce mécanisme fonctionne en envoyant une requête au serveur, qui nous retourne une page HTML.

Ce mécanisme est intéressant, mais ne permet pas d'avoir des applications dites "SPA" _Single Page Application_, c'est à dire des applications dont on à l'impression que l'intégralité de la page n'est jamais rechargeée, mais seulement une partie.

Dans une application monopage (SPA), le JavaScript côté client doit intercepter la navigation, récupérer dynamiquement de nouvelles données, et mettre à jour la page actuelle sans la recharger entièrement. Il en résulte généralement une expérience utilisateur plus rapide, en particulier pour les cas d'utilisation qui ressemblent davantage à des "applications" réelles, où l'utilisateur est censé effectuer de nombreuses interactions sur une longue période de temps.

Dans ces SPA, le "routage" est donc effectué côté client, dans le navigateur.

Vue.js propose un système de routage nommé [VueRouter](https://github.com/vuejs/router), officiellement supporté, qui permet de créer des applications SPA. La documentation officielle se trouve ici : [https://router.vuejs.org/](https://router.vuejs.org/). Tout comme dans les frmameworks back, le système de route est très puissant et permet une contrôle précis de vos URL et des actions associés.

## Premier exemple

Tout d'abord, dans notre projet, nous devons installer les routes. Normalement c'est déjà fait, puisque vous avez du le choisir lors de l'installation du projet, dans le cas contraire, il faut l'installer avec la commande suivante :

```bash
npm install vue-router@4 ou yarn add vue-router@4
```

Si on souhaite ajouter des liens dans notre application, et définir une partie de notre page qui sera remplacée selon l'URL, on doit utiliser la balise `<router-link>` (pour les liens) et `<router-view>` (pour la partie de la page qui sera remplacée). Si on utilisait une balise `<a>` classique, le comportement par défaut du navigateur serait de recharger la page, ce qui n'est pas ce qu'on veut.

````html
<template>
  <div id="app">
    <h1>Mon application</h1>
    <router-link to="/">Accueil</router-link>
    <router-link to="/about">A propos</router-link>
    <router-view></router-view>
  </div>
```html

Dans le code ci-dessus, on a ajouté deux liens, qui pointent vers les URL `/` et `/about`. On a également ajouté une balise `<router-view>`, qui sera remplacée par le contenu de la page correspondant à l'URL.

Pour que cela fonctionne, il faut ajouter le code suivant dans le fichier `src/main.js` (attention certaines parties sont déjà présentes) :

```js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'

// 1. On défini le contenu des routes. Ici simplement avec une template basique, mais on pourrait importer des composants.
const Home = { template: '<div>Home</div>' }
const About = { template: '<div>About</div>' }

// 2. Définir pour chaque route, le chemin et le composant associé
const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About },
]

// 3. Créer l'instance du router et passer les options de `routes`
const router = VueRouter.createRouter({
  // 4. Provide the history implementation to use. We are using the hash history for simplicity here.
  history: VueRouter.createWebHashHistory(),
  routes, // short for `routes: routes`
})

// 5. Create and mount the root instance.
const app = Vue.createApp({})
// Make sure to _use_ the router instance to make the
// whole app router-aware.
app.use(router)

app.mount('#app')
````

On peut accéder aux paramètres de la route, ou à l'objet router depuis n'importe quel composant, en utilisant les fonctions `useRouter` et `useRoute` :

Par exemple :

```js
import { useRouter, useRoute } from 'vue-router'

const router = useRouter()
const route = useRoute()

username() {
    // Pour récupérer un paramètre de l'URL (on verra cela par la suite)
    return route.params.username
}


goToDashboard() {
    //pour faire une redirection
    if (isAuthenticated) {
    router.push('/dashboard')
    } else {
    router.push('/login')
    }
}
```
