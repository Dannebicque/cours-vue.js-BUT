# 5 dernières séances : Mini projet e-commerce

## Objectif

L'objectif de ces séances est de mettre en place un exemple de site e-commerce en utilisant VueJS et une API REST. Nous allons créer un site e-commerce simple avec des produits, des catégories, un panier et un formulaire de commande.

## Mise en place du projet

### API REST

Nous allons utiliser une API REST pour récupérer les données des produits et des catégories. Vous pouvez utiliser l'API de démonstration ici : [https://dummyjson.com/docs/products](https://dummyjson.com/docs/products)

### VueJS

Nous allons créer un site e-commerce simple avec les fonctionnalités suivantes :

* Afficher la liste des produits (avec la pagination, l'ensemble des éléments correctements présentés et une image affichée)
* Afficher la liste des catégories (depuis l'API)
* Afficher les détails d'un produit dans une page dédiée. Pouvoir afficher les images dans une modale.
* Ajouter un produit au panier avec une confirmation d'ajout et l'état du panier visible sur toutes les pages
* Afficher le panier
* Le panier doit être visible sur toutes les pages (avec le nombre de produits et le montant total
* Supprimer un produit du panier (avec une modal de confirmation) ou modifier la quantité (supprier si quantité = 0). Le montant du panier est mis à jour en temps réel.
* Sauvegarder le panier dans le `localStorage`
* Formulaire de commande avec validation des champs

### Mise en page

Vous pouvez utiliser un framework CSS comme Bootstrap ou TailwindCSS pour la mise en page. Vous pouvez également regarder du côté de Vuetify pour un framework CSS compatible avec VueJS ou d'autres ici : https://ui-libs.vercel.app/).

### Les stores (Pinia)...

Deux références : https://blog.zenika.com/2023/07/13/pinia-%F0%9F%8D%8D-lavenir-du-state-management-vue-js-en-10-minutes/
ou https://runthatline.com/how-to-use-local-storage-pinia/

Et la documentation officielle : https://pinia.vuejs.org/

#### Le concept

Pinia est un store de gestion d'état pour Vue 3. Il est inspiré de Vuex mais avec une approche plus simple et plus performante. Il est basé sur le concept de stores qui sont des objets JavaScript qui contiennent l'état de l'application et des méthodes pour le modifier. Les stores sont réactifs, ce qui signifie qu'ils mettent à jour automatiquement les composants qui les utilisent lorsqu'ils sont modifiés.

#### Installation

Pour installer Pinia, vous devez d'abord ajouter le package à votre projet :

```bash
npm install pinia
```

Ensuite, vous devez l'ajouter à votre application Vue :

```javascript

import { createApp } from 'vue'
import { createPinia } from 'pinia'
import App from './App.vue'
//... et votre code en plus...
const app = createApp(App)
app.use(createPinia())
app.mount('#app')
```

#### Création d'un store

Un "Store" est un fichier JavaScript qui contient les données et les méthodes pour les manipuler. Il est possible d'écrire un store en "option API" ou en "composition API".

Comme pour VueJs il est conseillé d'utiliser la composition API (ou setup API dans la documentation de Pinia) pour créer un store avec Pinia.

Pour créer un store avec Pinia, vous devez d'abord importer la fonction `defineStore` :

```javascript
import { defineStore } from 'pinia'

// You can name the return value of `defineStore()` anything you want,
// but it's best to use the name of the store and surround it with `use`
// and `Store` (e.g. `useUserStore`, `useCartStore`, `useProductStore`)
// the first argument is a unique id of the store across your application
export const useAlertsStore = defineStore('alerts', {
  // other options...
})
```

ou useAlertsStore est le nom du store et 'alerts' est l'id du store.

Ci-dessous un exemple de store pour un compteur :

```javascript
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  const name = ref('Eduardo')
  const doubleCount = computed(() => count.value * 2)
  function increment() {
    count.value++
  }

  return { count, name, doubleCount, increment }
})
```

Les lignes suivantes définissent les propriétés du store :

* `count` : un compteur qui est initialisé à 0
* `name` : un nom qui est initialisé à 'Eduardo'
* `doubleCount` : une propriété calculée qui retourne le double de `count`
* `increment` : une méthode qui incrémente `count` de 1
* `return` : retourne les propriétés et les méthodes que vous souhaitez rendre accessibles aux composants qui utilisent le store

#### Utilisation d'un store

Pour utiliser un store dans un composant, vous devez d'abord importer la fonction `useStore` :

```javascript
<script setup>
import { useCounterStore } from '@/stores/counter'

// access the `store` variable anywhere in the component ✨
const store = useCounterStore()
</script>
```

Ensuite, vous pouvez accéder aux propriétés et aux méthodes du store en utilisant la variable `store` :

```javascript
<template>
  <div>
    <p>Count: {{ store.count }}</p>
    <p>Name: {{ store.name }}</p>
    <p>Double Count: {{ store.doubleCount }}</p>
    <button @click="store.increment">Increment</button>
  </div>
</template>
```