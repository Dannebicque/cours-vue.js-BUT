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
