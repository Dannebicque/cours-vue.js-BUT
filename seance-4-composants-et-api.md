# Séance 4 : Composants et API

## Composants

{% hint style="info" %}
Cette séance est une adaptation/traduction des parties suivantes de la documentation officielle de Vue.js : [Components Basics](https://vuejs.org/guide/essentials/component-basics.html), et les parties suivantes sur les [Props](https://vuejs.org/guide/components/props.html) et les [Events](https://vuejs.org/guide/components/events.html).
{% endhint %}

### Principes

Les composants nous permettent de fractionner l'UI en morceaux indépendants et réutilisables, sur lesquels nous pouvons réfléchir de manière isolée. Il est courant pour une application d'être organisée en un arbre de composants imbriqués.

<figure><img src=".gitbook/assets/components.7fbb3771.png" alt=""><figcaption><p>Schéma de principe des composants</p></figcaption></figure>

Cette approche est très similaire à celle des éléments HTML, mais Vue.js implémente son propre modèle de composant, nous permettant d'encapsuler du contenu et de la logique au sein de chaque composant. Les composants peuvent être réutilisés dans d'autres composants, et ils peuvent être composés de manière récursive pour créer des applications complexes.

### Définition d'un composant

Il existe deux solutions pour construire des composants en Vue.js :

* Une approche par Single-File Components (SFC), qui est la solution recommandée par Vue.js et qui consite à créer un fichier .vue pour chaque composant. Ce fichier contient le code HTML, CSS et JavaScript du composant. Nous allons voir cette approche dans la suite de ce cours. Cette solution implique de "compiler" le code du composant, ce qui est fait automatiquement par le CLI de Vue.js.
* Un fichier JavaScript qui exporte un objet composant. Nous ne verrons pas cette approche dans ce cours. Cette solution permet un usage sans compilation, mais elle est moins pratique que la solution SFC.

### Utilisation d'un composant

Afin d'utiliser un composant dit enfant dans un composant dit parent, nous devons l'importer. En supposant que nous ayons placé notre composant `compteur` dans un fichier nommé `ButtonCounter.vue`, le composant apparaîtra comme l'export par défaut du fichier. Pour l'important dans le parent, nous pourrions avoir la syntaxe suivante (nous l'avons déjà rencontré dans les parties précédentes du cours) :

```javascript
<script setup>
import ButtonCounter from './ButtonCounter.vue'
</script>

<template>
  <h1>Here is a child component!</h1>
  <ButtonCounter />
</template>
```

Précisons de nouveau que nous utilisons ici la méthode par "composition" de Vue.js 3, cette solution "simplifie" le code. Pour information avec la solution par "options" de Vue.js 2, nous aurions écrit :

```javascript
<script>
import ButtonCounter from './ButtonCounter.vue'

export default {
  components: {
    ButtonCounter
  }
}
</script>

<template>
  <h1>Here is a child component!</h1>
  <ButtonCounter />
</template>
```

Une fois un composant importé, vous pouvez l'utiliser autant de fois que nécessaire. Chaque composant reste indépendant des autres, et si nous ajoutons 2 composants `ButtonCounter` dans le même parent, ils seront indépendants l'un de l'autre et chacun aura ses valeurs propres.

{% hint style="info" %}
Dans les SFC, il est recommandé d'utiliser des noms de balise en casse Pascal (PascalCase) pour les composants enfants afin de les différencier des éléments HTML natifs. Bien que les noms des balises HTML natifs soient insensibles à la casse, les SFC de Vue sont un format compilé, donc nous pouvons y utiliser des noms de balise sensibles à la casse. Nous pouvons également utiliser /> pour fermer une balise.
{% endhint %}

### Props (passer des données à nos composants enfants)

Les props sont des attributs personnalisés que nous pouvons passer à nos composants enfants. Ils permettent de passer des données d'un composant parent à un composant enfant.

Imaginons que nous avons une application présentant des articles dans un blog. Nous aurions un composant parent affichant chaque article. Un article serait alors un composant enfant. Nous pourrions avoir une structure de composants comme celle-ci :

```javascript
<template>
  <h1>Mon Blog</h1>
  <Article />
  <Article />
  <Article />
  <Article />
</template>

<script setup>
import Article from './Article.vue'
</script>
```

Nous aurions ici 4 articles (on pourrait faire une boucle...), mais comment faire pour que chaque article soit différent ? Nous devons définir une "props" pour notre composant enfant `Article`, afin qu'il puisse recevoir des données. Nous pouvons définir une prop en utilisant l'attribut `props` de notre composant enfant :

```javascript
<script setup>
defineProps(['title'])
</script>

<template>
  <h4>{{ title }}</h4>
</template>
```

Dans ce code, nous définissons qu'il y a une prop `title` qui sera passée à notre composant enfant. Nous pouvons ensuite utiliser cette prop dans le template de notre composant enfant. Nous pouvons maintenant passer des données à notre composant enfant :

{% hint style="info" %}
`defineProps` est une macro de compilation qui est seulement accessible à l'intérieur de `<script setup>` et ne nécessite pas d'être explicitement importée. Les props déclarées sont automatiquement exposées au template. `defineProps` retourne également un objet contenant toutes les propriétés passées au composant, de manière à ce que l'on puisse y accéder en JavaScript si nécessaire :

```javascript
const props = defineProps(['title'])
console.log(props.title)
```
{% endhint %}

```javascript
<template>
  <h1>Mon Blog</h1>
  <Article title="Mon premier article" />
  <Article title="Mon deuxième article" />
  <Article title="Mon troisième article" />
  <Article title="Mon quatrième article" />
</template>

<script setup>
import Article from './Article.vue'
</script>
```

Un composant peut avoir autant de props que vous voulez, et par défaut, n'importe quelle valeur peut être passée à une prop. Nous pouvons définir des valeurs et un type par défaut pour les props :

```javascript
<script setup>
defineProps({
  title: {
    type: String,
    default: 'Mon article'
  }
})
</script>

<template>
  <h4>{{ title }}</h4>
</template>
```

### Exercice sur les composants

Définissez un composant nommé "CardPays" qui affiche le nom et la capitale d'un pays. Vous devrez passer le nom et la capitale du pays en tant que props.

Dans la page Pays, vous devrez afficher 3 cartes pays, avec les valeurs de votre choix.

{% hint style="info" %}
Remarque : comme nous allons essayer de passer le contenu d'une variable dans une ou plusieurs props, nous devons passer par un v-bind sur les props (comme pour n'importe quel attribut).
{% endhint %}


## Cycle de vie et premiers pas avec les API

{% hint style="info" %}
Cette séance est la traduction/adaptation de la documentation officielle se trouvant ici : [https://vuejs.org/guide/essentials/lifecycle.html](https://vuejs.org/guide/essentials/lifecycle.html)
{% endhint %}

Chaque instance d'un composant Vue passe par une série d'étapes d'initialisation lorsqu'elle est créée - par exemple, il faut paramétrer l'observation des données, compiler le template, monter l'instance sur le DOM, et le mettre à jour lorsque les données changent. En cours de route, des fonctions appelées hooks du cycle de vie sont également exécutées, donnant la possibilité à l'utilisateur d'ajouter son propre code à des étapes spécifiques.

Ci-dessous figure le diagramme du cycle de vie d'une instance.

<figure><img src="https://fr.vuejs.org/assets/lifecycle.6903e504.png" alt=""><figcaption><p>Cycle de vie d'un composant Vue</p></figcaption></figure>

Chaque case rouge est un moment où il est possible d'intéragir avec le composant.

### Enregistrement des hooks du cycle de vie

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

### Accès aux données depuis un appel API

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

### Utilisation de l'API

#### Création d'un composant

Dans une vue ou un composant, nous pouvons récupérer les données de l'API et les afficher dans le template.

{% code lineNumbers="true" %}
```javascript
<template>
  <div>
    <p>{{ data }}</p>
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

#### Explications

Nous avons déclaré une variable `data` qui est initialisée à une chaîne de caractères vide. Nous avons ensuite utilisé le hook `onMounted` pour récupérer les données depuis l'API et les stocker dans la variable `data`.

A partir de là, `data` est accessible dans le template. Cette variable contient toutes les données retournées par l'API, et il est donc possible de l'afficher dans le template de manière plus fine.

{% code lineNumbers="true" %}
```javascript
<template>
  <div>
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

### Exercice

Dans la vue Pays, ajoutez la méthode onMounted pour récupérer les données de l'API et les afficher dans le template.

{% hint style="info" %}

Vous pouvez utiliser l'API suivante : ['https://restcountries.com/v3.1/all']('https://restcountries.com/v3.1/all')

{% endhint %}

* Dans un premier temps affichez simplement la réponse dans votre template ``{{ data }}`` si votre variable se nomme `data`.

* Dans un deuxième temps faites une simple boucle pour afficher le nom de chaque pays. `{{ pays.name.common }}` si votre variable dans la boucle se nomme `pays`.

* Dans un troisième temps, utilisez le composant `CardPays` pour afficher les données de chaque pays, et passez en props la variable contenant toutes les données du pays.

* Modifiez le composant `CardPays` pour afficher les données de la props, et notamment l'image du drapeau et le nom du pays.
