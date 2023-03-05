# Séance 3 : Routes

Dans une application web classique, on utilise des URL pour changer le comportement de notre application, et le contenu des pages. Ce mécanisme fonctionne en envoyant une requête au serveur, qui nous retourne une page HTML.

Ce mécanisme est intéressant, mais ne permet pas d'avoir des applications dites "SPA" _Single Page Application_, c'est à dire des applications dont on à l'impression que l'intégralité de la page n'est jamais rechargée, mais seulement une partie.

Dans une application monopage (SPA), le JavaScript côté client doit intercepter la navigation, récupérer dynamiquement de nouvelles données, et mettre à jour la page actuelle sans la recharger entièrement. Il en résulte généralement une expérience utilisateur plus rapide, en particulier pour les cas d'utilisation qui ressemblent davantage à des "applications" réelles/logicielles, où l'utilisateur est censé effectuer de nombreuses interactions sur une longue période de temps.

Dans ces SPA, le "routage" est donc effectué côté client, dans le navigateur.

Vue.js propose un système de routage nommé [VueRouter](https://github.com/vuejs/router), officiellement supporté, qui permet de créer des applications SPA. La documentation officielle se trouve ici : [https://router.vuejs.org/](https://router.vuejs.org/). Tout comme dans les frameworks back, le système de route est très puissant et permet un contrôle précis de vos URL et des actions associées.

## Premier exemple

Tout d'abord, dans notre projet, nous devons installer les routes. Normalement c'est déjà fait, puisque vous avez du le choisir lors de l'installation du projet, dans le cas contraire, il faut l'installer avec la commande suivante :

```bash
npm install vue-router@4 

# ou 

yarn add vue-router@4
```

Si on souhaite ajouter des liens dans notre application, et définir une partie de notre page qui sera remplacée selon l'URL, on doit utiliser la balise `<router-link>` (pour les liens) et `<router-view>` (pour la partie où le contenu sera chargé). Si on utilisait une balise `<a>` classique, le comportement par défaut du navigateur serait de recharger la page, ce qui n'est pas ce que l'on souhaite.

```html
<template>
  <div id="app">
    <h1>Mon application</h1>
    <router-link to="/">Accueil</router-link>
    <router-link to="/about">A propos</router-link>
    <router-view></router-view>
  </div>
```

Dans le code ci-dessus, on a ajouté deux liens, qui pointent vers les URL `/` et `/about` (ligne 4 et 5). On a également ajouté une balise `<router-view>` (ligne 6), qui sera remplacée par le contenu de la page correspondant à l'URL.

Pour que cela fonctionne, il faut déclarer nos routes. On pourra le faire directement dans le `main.js`, mais la recommandation est plutôt de constuire nos routes dans un repértoire dédié et un ou plusieurs fichiers, par exemple `src/router/index.js`.

```javascript
import { createRouter, createWebHistory } from 'vue-router'

import Home from '../views/Home.vue'
import About from '../views/About.vue'

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes: [
    {
      path: '/',
      component: Home
    },
    {
      path: '/about',
      component: About
    }
  ]
})

export default router
```

Il faut ensuite importer ce fichier dans le `main.js` (ce fichier dois déjà ressembler à quelque chose de similaire):

```javascript
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'

import './assets/main.css'

const app = createApp(App)

app.use(router)

app.mount('#app')
```

## Mise en pratique

### Contexte

Sur le reste de cette ressource nous allons concervoir une petite application intégralement en Vue.js permettant de commander des fiches descriptives des pays du monde.

### Création du projet

Pour vous faciliter le départ (vous pourriez aussi installé un nouveau projet comme vue en séance 1 et supprimer les éléments inutiles), un fichier vide, pré-configué est disponible sur ce lien : [Projet vide](projet vide).

* Téléchargez le fichier zip
* Dézippé le dans le dossier de votre choix
* executez la commande `npm install` ou `yarn install` pour installer les dépendances
* Testez le projet avec la commande `npm run dev` ou `yarn dev`

Ce projet contient le nécessaire pour utiliser les routes, n'hésitez pas à regarder les fichiers décrits plus tôt dans le cours.

### A faire

* Créer une première route `/` qui affiche un message de bienvenue, dans une vue `Home.vue` (à créer dans le répertoire `src/views`). ajoutez un lien vers cette page.

## Routes nommées

Pour des raisons pratiques et pour éviter de manipuler des URL dans le code, on peut définir des routes nommées.

```javascript
const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About },
  { path: '/user', component: User, name: 'user' },
]
```

On peut ensuite utiliser ces routes nommées dans le code :

```html
  <router-link :to="{name: 'user'}">
    Utilisateur
  </router-link>
```

### A faire

* Créer une route `/about` qui affiche un message a propos, dans une vue `About.vue`, et qui se nomme "about" (à créer dans le répertoire `src/views`). Ajoutez un lien vers cette page en utilisant son nom.

## Routes avec paramètres

Il est possible de passer des paramètres à nos routes.

```javascript

const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About },
  { path: '/user/:username', component: User, name: 'user' },
]
```

Pour passer le paramètre lors de la mise en place du lien, on peut soit passer par la route nommée (le plus pratique), soit construire l'URL avec une variable.

### Avec une route nommée

```html
  <router-link :to="{name: 'user', params: {username: 'david'}}">
    Utilisateur
  </router-link>

<!-- ou avec une variable -->

  <router-link :to="{name: 'user', params: {username: username}}">
    Utilisateur
  </router-link>
  ```

### Avec une URL

```html
  <router-link :to="`/user/${username}`">
    Utilisateur
  </router-link>
```

Où username est une variable javascript.

### Récupérer le paramètre

On peut accéder aux paramètres de la route, ou à l'objet router depuis n'importe quel composant, en utilisant la fonction `useRoute` :

Par exemple :

```javascript
import { useRoute } from 'vue-router'

const route = useRoute()

// Pour récupérer un paramètre de l'URL
const username = route.params.username
```

### A faire

* Ajoutez une route `/pays` qui affiche une vue `Pays.vue` (à créer dans le répertoire `src/views`). Ajoutez un lien vers cette page.
* Dans la vue `Pays.vue`, faire une boucle sur 10 pays fictifs (nommés pays 1, pays 2, ...), et afficher un lien vers une page de détail de chaque pays (`/fiche-pays/:id`), en utilisant le nom de la route. Ajoutez la vue associée `FichePays.vue` (à créer dans le répertoire `src/views`).
* Dans la vue `FichePays.vue`, affichez la valeur du parmètre dans la page.
