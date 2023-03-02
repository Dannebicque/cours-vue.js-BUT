# Séance 5 : Cycle de vie et premiers pas avec les API

{% hint style="info" %}
Cette séance est la traduction/adaptation de la documentation officielle se trouvant ici : [https://vuejs.org/guide/essentials/lifecycle.html](https://vuejs.org/guide/essentials/lifecycle.html)
{% endhint %}

Chaque instance d'un composant Vue passe par une série d'étapes d'initialisation lorsqu'elle est créée - par exemple, il faut paramétrer l'observation des données, compiler le template, monter l'instance sur le DOM, et le mettre à jour lorsque les données changent. En cours de route, des fonctions appelées hooks du cycle de vie sont également exécutées, donnant la possibilité à l'utilisateur d'ajouter son propre code à des étapes spécifiques.

Ci-dessous figure le diagramme du cycle de vie d'une instance.

<figure><img src="https://fr.vuejs.org/assets/lifecycle.6903e504.png" alt=""><figcaption><p>Cycle de vie d'un composant Vue</p></figcaption></figure>

Chaque case rouge est un moment où il est possible d'intéragir avec le composant.

## Enregistrement des hooks du cycle de vie

Par exemple, le hook `onMounted` peut être utilisé pour exécuter du code après que le composant ait terminé son rendu initial et créer les nœuds du DOM (sur la case route `mounted` du schéma ci-dessus):

{% code lineNumbers="true" %}
```javascript
<script setup>
import { onMounted } from 'vue'

onMounted(() => {
  console.log(`the component is now mounted.`)
})
</script>
```
{% endcode %}

Il y a également d'autres hooks qui peuvent être appelés à différentes étapes du cycle de vie de l'instance, dont les plus couramment utilisés sont onMounted, onUpdated, et onUnmounted.

Il peut être intéressant d'utiliser ce hook pour effectuer des actions lorsque le composant est monté, mis à jour ou démonté. Par exemple, onMounted peut être utilisé pour récupérer des données depuis une API, et onUnmounted pour annuler les abonnements aux événements.

## Accès aux données depuis un appel API

{% hint style="info" %}
**Les appels API peuvent se faire à chaque fois que nécessaire, dans des méthodes, dans des hooks, ...**

Il est possible d'utiliser la méthode fetch pour récupérer des données depuis une API, qui est native à JavaScript. (voir [https://developer.mozilla.org/fr/docs/Web/API/Fetch\_API](https://developer.mozilla.org/fr/docs/Web/API/Fetch\_API)). Il existe également la librairie axios qui permet de faire des appels API plus facilement, et qui est très utilisée dans le monde Vue.js (et les framework frot en général). (voir [https://axios-http.com/fr/docs/intro](https://axios-http.com/fr/docs/intro))
{% endhint %}

### Exemple d'utilisation de fetch

{% code lineNumbers="true" %}
```javascript
<script setup>
import { onMounted } from 'vue'

onMounted(async () => {
  const response = await fetch('https://jsonplaceholder.typicode.com/todos/1')
  const data = await response.json()
  console.log(data)
})
</script>
```
{% endcode %}

### Exemple d'utilisation de axios

Axios n'étant pas natif, il faut d'abord l'installer avec la commande `npm install axios` ou `yarn add axios`.

{% code lineNumbers="true" %}
```javascript
<script setup>
import { onMounted } from 'vue'
import axios from 'axios'

onMounted(async () => {
  const response = await axios.get('https://jsonplaceholder.typicode.com/todos/1')
  console.log(response.data)
})
</script>
```
{% endcode %}

{% hint style="info" %}
Pour la suite, nous allons utiliser axios. Mais libre à vous d'utiliser la méthode que vous préférez. Le code et la logique seront sensiblement identiques
{% endhint %}

## Utilisation de l'API

### Création d'un composant

Créons un composant qui va récupérer des données depuis une API et les afficher dans la page.

{% code lineNumbers="true" %}
```javascript
<template>
  <div>
    <h1>API</h1>
    <p>{{ data }}</p>
    <p>{{ data.title }}</p>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

let data = ref('')

onMounted(async () => {
  const response = await axios.get('https://jsonplaceholder.typicode.com/todos/1') //cette URL est une démonstration, vous devez adapter l'URL à votre besoin. La récupération se fait ici en GET, mais il est possible de faire des requêtes POST, PUT, DELETE, ...
  data.value = response.data
})
</script>
```
{% endcode %}

### Utilisation du composant

Nous allons maintenant utiliser ce composant dans notre application.

{% code lineNumbers="true" %}
```javascript

<template>
  <div>
    <h1>App</h1>
    <API />
  </div>
</template>

<script setup>
import API from './components/API.vue'
</script>
```
{% endcode %}

### Explications

Dans le composant API, nous avons déclaré une variable `data` qui est initialisée à une chaîne de caractères vide. Nous avons ensuite utilisé le hook `onMounted` pour récupérer les données depuis l'API et les stocker dans la variable `data`.

Dans le composant App, nous avons importé le composant API et nous l'avons ajouté dans la liste des composants utilisés.

A partir de là, `data` est accessible dans le template du composant App, et donc dans le template du composant API. Cette variable contient toutes les données retournées par l'API, et il est donc possible de l'afficher dans le template de manière plus fine.

{% code lineNumbers="true" %}
```javascript
<template>
  <div>
    <h1>API</h1>
    <p>{{ data.title }}</p>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

let data = ref('')

onMounted(async () => {
  const response = await axios.get('https://jsonplaceholder.typicode.com/todos/1')
  data.value = response.data
})
</script>
```
{% endcode %}

Ce code ne fonctionne que si votre réponse json contient une entrée `title`. Ce qui est le cas dans l'exemple ici qui renvoi la réponse suivante :

```javascript
{
  "userId": 1,
  "id": 1,
  "title": "delectus aut autem",
  "completed": false
}
```

### API et Formulaires

Si on souhaite récupérer les données précises sur une API, on peut paramètrer l'appel avec des variables.

Par exemple

```javascript
</template>

<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

let data = ref('')

onMounted(async () => {
  const id = 1
  const response = await axios.get(`https://jsonplaceholder.typicode.com/todos/${id}`)
  data.value = response.data
})
</script>
```

Cette solution fonctionnelle, n'est pas très interactive, on pourrait donc récupérer cet id depuis un formulaire (ou l'URL par la suite).

Exemple :

```javascript
<template>
  <div>
    <h1>API</h1>

    <input type="text" v-model="id">
    <button @click="getApi">Get Data</button>
    <p>{{ data }}</p>
    <p>{{ data.title }}</p>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

let data = ref('')
let id = ref()

async function getApi() {
  const response = await axios.get(`https://jsonplaceholder.typicode.com/todos/${id.value}`)
  data.value = response.data
}
</script>

```

## Exercices

### Exercice LP

En utilisant l"API : [https://restcountries.com/#api-endpoints-v3-all](https://restcountries.com/#api-endpoints-v3-all)

1. Récupérez et affichez la liste des pays (nom, capitale, langue)
2. Proposez une barre de recherche affichant les informations d'un pays de votre choix

### Exercice 1

Créer un composant qui récupère les données d'une API et qui les affiche dans la page sous la forme d'une liste ul/li. Mettre en vert les éléments si le champ `completed` est à `true`. Mettre en rouge les éléments si le champ `completed` est à `false`. (l'URL de l'API est [https://jsonplaceholder.typicode.com/todos/4](https://jsonplaceholder.typicode.com/todos/4) renvoie un exemple de réponse à true).

### Exercice 2

Récupéré un tableau de tâches avec l'URL [https://jsonplaceholder.typicode.com/todos](https://jsonplaceholder.typicode.com/todos) et afficher les tâches dans une liste ul/li. Mettre en vert les éléments si le champ `completed` est à `true`. Mettre en rouge les éléments si le champ `completed` est à `false`.
