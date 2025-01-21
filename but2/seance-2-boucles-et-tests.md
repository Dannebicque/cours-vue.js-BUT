# Séance 2 : Boucles et tests

Comme tous les langages, il est possible d'avoir des conditions/tests, des boucles en Vue.js. Bien que Vue.js soit un framework JavaScript, les boucles et tests peuvent être soit dans le script (et donc avec une syntaxe JavaScript classique), soit dans la partie template, et dans ce cas on utilise la syntaxe de Vue.js décrite ci-après.

## Les tests

{% hint style="info" %}
Cette partie reprend la traduction de la documentation officielle se trouvant ici : [https://vuejs.org/guide/essentials/conditional.html](https://vuejs.org/guide/essentials/conditional.html)
{% endhint %}

### `v-if`

La directive `v-if` est utilisée pour restituer conditionnellement un bloc. Le bloc ne sera rendu que si l'expression de la directive retourne une valeur évaluée à vrai.

```html
<h1 v-if="awesome">Vue est magnifique!</h1>
```

Dans cet exemple `awesome` doit être une variable définie dans la partie script.

### `v-else`

Vous pouvez utiliser la directive `v-else` pour indiquer un bloc "sinon" lié à un `v-if`:

{% code lineNumbers="true" %}
```html
<button @click="awesome = !awesome">Basculer</button>

<h1 v-if="awesome">Vue est magnifique!</h1>
<h1 v-else>Oh non 😢</h1>
```
{% endcode %}

Un élément `v-else` doit immédiatement suivre un élément `v-if` ou un élément `v-else-if` sinon il ne sera pas reconnu.

{% hint style="info" %}
Dans cet extrait de code la ligne 1 contient la notation `@click` (qu'on peut aussi écrire `v-on:click`) qui va déclencher quelque chose lors d'un clic. C'est le déclenchement d'un événement sur le DOM. Nous y reviendrons par la suite. A chaque clique, la variable `awesome` recevra son inverse.
{% endhint %}

### Exercice

* Testez le code ci-dessus avec le bouton.

### `v-else-if`

Le `v-else-if`, comme son nom l'indique, sert de bloc "else if" lié à un `v-if`. Il peut également être enchaîné plusieurs fois:

```html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Pas A/B/C
</div>
```

Similaire à `v-else`, un bloc `v-else-if` doit immédiatement suivre un bloc `v-if` ou `v-else-if`.

### Exercices

* Définir 5 notes, et pour chacune indiquer en Vue.js si elles sont supérieures à 10 ou non.
* Modifier le code pour différencier les notes entre 8 et 10

### `v-if` avec `<template>`

Puisque `v-if` est une directive, elle doit être attachée à un seul élément. Mais que se passe-t-il si nous voulons basculer plus d'un élément ? Dans ce cas, nous pouvons utiliser `v-if` sur un élément `<template>`, qui sert de conteneur invisible. Le résultat du rendu final n'inclura pas l'élément `<template>`.

```html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

`v-else` et `v-else-if` peuvent également être utilisés dans `<template>`.

### `v-show`

Une autre option pour afficher conditionnellement un élément est la directive `v-show`. L'utilisation est sensiblement la même:

```html
<h1 v-show="ok">Bonjour!</h1>
```

La différence est qu'un élément avec `v-show` sera toujours rendu et restera dans le DOM; `v-show` bascule uniquement la propriété CSS `display` de l'élément.

`v-show` ne prend pas en charge l'élément `<template>`, et ne fonctionne pas avec `v-else`.

### `v-if` vs. `v-show`

`v-if` est un rendu conditionnel "réel" car il garantit que les écouteurs d'événements et les composants enfants à l'intérieur du bloc conditionnel sont correctement détruits et recréés lors des basculements.

`v-if` est également **paresseux** : si la condition est fausse lors du rendu initial, il ne fera rien - le bloc conditionnel ne sera rendu que lorsque la condition deviendra vraie pour la première fois.

En comparaison, `v-show` est beaucoup plus simple - l'élément est toujours rendu quelle que soit la condition initiale, avec un basculement basé sur CSS.

De manière générale, `v-if` a des coûts de basculement plus élevés tandis que `v-show` a des coûts de rendu initiaux plus élevés. Préférez donc `v-show` si vous avez besoin de basculer quelque chose très souvent, et préférez `v-if` si la condition est peu susceptible de changer à l'exécution.

## Les boucles

{% hint style="info" %}
Extrait de la traduction de la page : [https://vuejs.org/guide/essentials/list.html](https://vuejs.org/guide/essentials/list.html)
{% endhint %}

### `v-for`

Nous pouvons utiliser la directive `v-for` pour rendre une liste d'items basée sur un tableau. La directive `v-for` nécessite une syntaxe spéciale de la forme `item in items`, où `items` est le tableau de données source et `item` est un **alias** pour l'élément du tableau sur lequel on itère :

```javascript
const items = [{ message: 'Foo' }, { message: 'Bar' }]
```

```html
<li v-for="item in items">
  {{ item.message }}
</li>
```

À l'intérieur de la portée du `v-for`, les expressions du template ont accès à toutes les propriétés de la portée du parent. De plus, `v-for` supporte également un second et optionnel alias pour l'index de l'item actuel :

```javascript
const parentMessage = 'Parent'
const items = [{ message: 'Foo' }, { message: 'Bar' }]
```

```html
<li v-for="(item, index) in items">
  {{ parentMessage }} - {{ index }} - {{ item.message }}
</li>
```

Donnera le résultat ci-dessous.

* Parent - 0 - Foo
* Parent - 1 - Bar

{% hint style="info" %}
Noté que contrairement au PHP par exemple, l'index est le second argument (c'est l'équivalent du "i") dans les boucles for, ou du "key" dans les foreach, mais par contre le sens et la syntaxe est identique à un forEach en JavaScript
{% endhint %}

### v-for imbriqués

Pour les `v-for` imbriqués, les portées fonctionnent de la même manière qu'avec les fonctions imbriquées. Chaque portée de `v-for` a accès aux portées de ses parents :

```html
<li v-for="item in items">
  <span v-for="childItem in item.children">
    {{ item.message }} {{ childItem }}
  </span>
</li>
```

### `v-for` avec un objet

Vous pouvez également utiliser `v-for` pour itérer sur les propriétés d'un objet. L'ordre d'itération sera basé sur le résultat de l'appel à `Object.keys()` sur l'objet :

```javascript
const myObject = {
  title: 'How to do lists in Vue',
  author: 'Jane Doe',
  publishedAt: '2016-04-10'
}
```

```html
<ul>
  <li v-for="value in myObject">
    {{ value }}
  </li>
</ul>
```

Vous pouvez également fournir un second alias pour le nom de la propriété (aussi appelé clé) :

```html
<li v-for="(value, key) in myObject">
  {{ key }}: {{ value }}
</li>
```

Et un autre pour l'index :

```html
<li v-for="(value, key, index) in myObject">
  {{ index }}. {{ key }}: {{ value }}
</li>
```

### `v-for` avec une portée

`v-for` peut également prendre un nombre entier. Dans ce cas il va répéter le template un certain nombre de fois, basé sur une portée `1...n`.

```html
<span v-for="n in 10">{{ n }}</span>
```

{% hint style="warning" %}
Notez qu'ici `n` démarre avec une valeur initiale de `1` au lieu de `0`.
{% endhint %}

### `v-for` sur le `<template>`

Comme le modèle `v-if`, vous pouvez aussi utiliser une balise `<template>` avec `v-for` pour effectuer le rendu d'un bloc composé de plusieurs éléments. Par exemple :

```html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

## Exercices

* Définir un tableau en JavaScript et l'afficher avec une boucle dans le template.
* Générez un tableau de 10 variables en JavaScript, et l'afficher dans le template
  * Pour chaque note indiquer si elle est supérieure à 10 (en ajoutant une class green pour la couleur de la texte) ou red pour une note inférieure à 10.
