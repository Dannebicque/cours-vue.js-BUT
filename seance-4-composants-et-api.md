# Séance 6 : Composants et API

{% hint style="info" %}
Cette séance est une adaptation/traduction des parties suivantes de la documentation officielle de Vue.js : [Components Basics](https://vuejs.org/guide/essentials/component-basics.html), et les parties suivantes sur les [Props](https://vuejs.org/guide/components/props.html) et les [Events](https://vuejs.org/guide/components/events.html).
{% endhint %}

## Principes

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

## Props (passer des données à nos composants enfants)

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

## Exercice

### Exercice 1

En considérant par exemple la liste d'article suivante :

```javascript
const posts = ref([
  { id: 1, title: 'My journey with Vue', texte: 'Message de mon article' },
  { id: 2, title: 'Blogging with Vue', texte: 'Deuxième message de mon article' },
  { id: 3, title: 'Why Vue is so fun', texte: 'Encore un autre message de mon article' }
])
```

1. Créez un composant `Post` qui affiche le titre et le texte d'un article.
2. Créez un composant `PostList` qui affiche une liste d'articles, en utilisant une boucle pour afficher les Post et passer les éléments.

{% hint style="info" %}
Remarque : comme nous allons essayer de passer le contenu d'une variable dans une ou plusieurs props, nous devons passer par un v-bind sur les props (comme pour n'importe quel attribut).
{% endhint %}

### Exercice 2

Il est possible de passer des props d'un composant à un autre. En reprenant l'exemple ci-dessous, définissez un composant pour un bouton "Lire Plus" (et le composant associé LirePlus) sur votre article qui n'affichera qu'un résumé. Passé depuis le composant Post, l'id de l'article au composant LirePlus.

## Écouter des événements

Les props permettent donc de communiquer du parent vers l'enfant. Mais comment communiquer de l'enfant vers le parent ?

Les composants enfants peuvent émettre des événements, qui peuvent être écoutés par le composant parent.

Reprennons l'exemple de la documentation.

Par exemple, on peut décider d'ajouter une fonctionnalité d'accessibilité permettant d'agrandir le texte des articles du blog, tout en laissant la taille par défaut sur le reste de la page.

Dans le parent, nous développons cette fonctionnalité en ajoutant une variable `postFontSize` (qui doit être réactive) :

```javascript
const posts = ref([
  /* ... */
])

const postFontSize = ref(1)
```

Qui pourra être utilisée dans le template afin de contrôler la taille de police de tous les articles du blog :

```javascript
<div :style="{ fontSize: postFontSize + 'em' }">
  <BlogPost
    v-for="post in posts"
    :key="post.id"
    :title="post.title"
   />
</div>
```

Maintenant ajoutons un bouton dans le template du composant `<BlogPost>` :

```javascript
<!-- BlogPost.vue, omitting <script> -->
<template>
  <div class="blog-post">
    <h4>{{ title }}</h4>
    <button>Enlarge text</button>
  </div>
</template>
```

Pour le moment le bouton ne fait rien. Nous voulons que le clique communique au parent qu'il doit agrandir le texte de tous les articles. Pour résoudre ce problème, les composants fournissent un système personnalisé d'événements. Le parent peut choisir d'écouter n'importe quel événement de l'instance du composant enfant grâce à `v-on` ou `@`, nous l'avons déjà vue dans une précédente partie :

```javascript
<BlogPost
  ...
  @enlarge-text="postFontSize += 0.1"
 />
```

Ensuite le composant enfant peut émettre lui-même un événement en appelant la méthode intégrée `$emit`, et en lui passant le nom de l'événement :

```javascript
<!-- BlogPost.vue, en omettant <script> -->
<template>
  <div class="blog-post">
    <h4>{{ title }}</h4>
    <button @click="$emit('enlarge-text')">Enlarge text</button>
  </div>
</template>
```

Grâce à l'écouteur `@enlarge-text="postFontSize += 0.1"`, le parent va recevoir l'événement et mettre à jour la valeur de `postFontSize`.

## Exercice

### Exercice 1

Intégrez cet exemple à votre projet précédent avec vos articles.

### Exercice 2

??
