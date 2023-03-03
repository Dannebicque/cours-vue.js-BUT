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

Pour que cela fonctionne, il faut ajouter le code suivant dans le fichier `src/main.js` (attention certaines parties sont déjà présentes) :

```javascript
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

...

### A faire

* Créer une route `/about` qui affiche un message a propos, dans une vue `About.vue`, et qui se nomme "about" (à créer dans le répertoire `src/views`). Ajoutez un lien vers cette page en utilisant son nom.

## Routes avec paramètres

On peut accéder aux paramètres de la route, ou à l'objet router depuis n'importe quel composant, en utilisant les fonctions `useRouter` et `useRoute` :

Par exemple :

```javascript
import { useRouter, useRoute } from 'vue-router'

const router = useRouter()
const route = useRoute()

const username = ref()

onMounted(() => {
    // Pour récupérer un paramètre de l'URL (on verra cela par la suite)
    username.value = route.params.username
})

goToDashboard() {
    //pour faire une redirection
    if (isAuthenticated) {
    router.push('/dashboard')
    } else {
    router.push('/login')
    }
}
```

### A faire

* Ajoutez une route `/pays` qui affiche une vue `Pays.vue` (à créer dans le répertoire `src/views`). Ajoutez un lien vers cette page.
* Dans la vue `Pays.vue`, faire une boucle sur 10 pays fictifs (nommés pays 1, pays 2, ...), et afficher un lien vers une page de détail de chaque pays (`/fiche-pays/:id`), en utilisant le nom de la route. Ajoutez la vue associée `FichePays.vue` (à créer dans le répertoire `src/views`).
* Dans la vue `FichePays.vue`, affichez la valeur du parmètre dans la page.