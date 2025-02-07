# Séance 7 : Evénements et Formulaires

{% hint style="info" %}
Cette partie est une traduction/adaptation de la documentation officielle située : [https://fr.vuejs.org/guide/essentials/forms.html](https://fr.vuejs.org/guide/essentials/forms.html)
{% endhint %}

## Directive `v-model`

La directive `v-model` permet de lier un élément du DOM à une variable du composant. Cette variable doit être déclarée dans la partie script du composant et réactive (`ref()`). Exemple :

```html
<input v-model="message" placeholder="edit me">
<p>Message is: {{ message }}</p>
```

```javascript
const message = ref('Hello Vue!')
```

`v-model` peut être utilisé sur des `<input>` de différents types, des éléments `<textarea>`, et `<select>`. Le `v-model` peut être considéré comme l'équivalent du `value` des champs de formulaire.

## Différents types de champs

Un ensemble d'exemple est disponible directement sur la documentation :

* Champs Textes : [https://vuejs.org/guide/essentials/forms.html#text](https://vuejs.org/guide/essentials/forms.html#text)
* Champs TextArea avec plusieurs lignes : [https://vuejs.org/guide/essentials/forms.html#multiline-text](https://vuejs.org/guide/essentials/forms.html#multiline-text)
* Champs Checkbox unique ou en récupérant un tableau : [https://vuejs.org/guide/essentials/forms.html#checkbox](https://vuejs.org/guide/essentials/forms.html#checkbox)
* Champs radio : [https://vuejs.org/guide/essentials/forms.html#radio](https://vuejs.org/guide/essentials/forms.html#radio)
* Champs select (simple ou multiple) : [https://vuejs.org/guide/essentials/forms.html#select](https://vuejs.org/guide/essentials/forms.html#select)

## Modificateurs

Vue.js permet d'ajouter des modificateurs à la directive `v-model` pour modifier son comportement, et modifier le type de données récupéré. Par exemple, pour récupérer un nombre au lieu d'une chaîne de caractères, il suffit d'ajouter le modificateur `number` à la directive `v-model`.

### .number

Si vous voulez que l'entrée de l'utilisateur soit automatiquement typée comme un nombre, vous pouvez ajouter le modificateur number à vos entrées gérées par `v-model` :

```html
<input v-model.number="age" />
```

Si la valeur ne peut pas être analysée avec parseFloat(), alors la valeur originale est utilisée à la place.

Le modificateur number est appliqué automatiquement si l'entrée possède type="number".

### .trim

Si vous voulez que les espaces blancs des entrées utilisateur soient automatiquement supprimés, vous pouvez ajouter le modificateur trim à vos entrées gérées par `v-model` :

```html
<input v-model.trim="msg" />
```

## Écouter des événements

{% hint style="info" %}
Cette partie est une traduction/adaptation de la documentation officielle située : [https://vuejs.org/guide/essentials/event-handling.html](https://vuejs.org/guide/essentials/event-handling.html)
{% endhint %}

Nous pouvons utiliser la directive `v-on`, qui peut se résumer par le symbole `@`, pour écouter les événements DOM et exécuter du JavaScript lorsqu'ils sont déclenchés. L'utilisation serait alors `v-on:click="handler"` ou avec le raccourci, `@click="handler"`.

`handler` peut soit être du code JavaScript (on parle de gestionnaire en ligne ou _inline handlers_), soit une méthode définie dans la partie script (on parle de méthode gestionnaire ou _method handler_).

### Gestionnaire en ligne

```html
<button @click="count++">Add 1</button>
<p>Count is: {{ count }}</p>
```

Avec la partie script :

```javascript
const count = ref(0)
```

Pour rappel, `ref()` permet de rendre la propriété `count` réactive. Il faut ajouter l'import suivant :

```javascript
import { ref } from 'vue'
```

Cette solution est pratique pour les interactions simples, mais elle ne permet pas de réutiliser le code. Pour cela, il faut utiliser une méthode gestionnaire.

### Méthode gestionnaire

```html
<!-- `greet` est le nom de la méthode définie dans la partie script -->
<button @click="greet">Greet</button>
```

{% code lineNumbers="true" %}
```javascript
const name = ref('Vue.js')

function greet(event) {
  alert(`Hello ${name.value}!`)
  // `event` est l'événement natif du DOM
  if (event) {
    alert(event.target.tagName)
  }
}
```
{% endcode %}

Notre bouton appelle ici une méthode nommée `greet`. Cette méthode est définie dans la partie script. Elle affiche une alerte avec le message "Hello Vue.js!". Elle prend, automatiquement, en paramètre un événement natif du DOM : la variable `event` en paramètre.

On peut grace à cet événement accéder à la cible de l'événement, ici le bouton, et afficher son nom de balise HTML, ou récupérer des valeurs des attributs, etc.

Il est possible de passer nos propres paramètres à une methode associée à un événément. Par exemple, si on veut passer le nom de la personne à saluer, on peut faire :

```html
<button @click="greet('John')">Say Hello</button>
```

```javascript
function greet(name) {
  alert(`Hello ${name}!`)
}
```

Si on souhaite à la fois passer nos paramètres et récupérer l'événement natif du DOM, on peut faire :

```html
<button @click="greet('John', $event)">Say Hello</button>
```

```javascript
function greet(name, event) {
  // ...
}
```

La notation `$event` est une syntaxe spéciale qui permet de passer l'événement natif du DOM en tant qu'argument à la méthode gestionnaire.

{% hint style="info" %}
Il existe de nombreux événements (change, keyup, keydown, blur...) qui peuvent être écoutés et déclencher des actions.
{% endhint %}

### Événement de touche

Il est possible d'utiliser les événements de touche pour écouter les événements clavier. Par exemple, si on veut écouter l'événement `keyup` sur un champ de saisie, on peut faire :

```html
<input @keyup.enter="onEnter">
```

Ici, on écoute l'événement `keyup` sur le champ de saisie et on exécute la méthode `onEnter` lorsque la touche `Enter` est pressée.

## Exercices

### Exercice 1

On va modifier la page pays pour filtrer les pays en fonction de la saisie de l'utilisateur. Nous allons le faire dans un premier temps avec un champ input et un bouton qui déclenchera un événement pour filtrer.

Dans un second temps nous le ferons en temps réel, c'est à dire que dès que l'utilisateur tape quelque chose dans le champ de saisie, la liste des pays sera filtrée.

#### Étape 1

Dans le fichier `src/views/Pays.vue`, ajouter un champ de saisie (associé à la variable _recherche_) et un bouton qui déclenchera un événement pour filtrer (la méthode se nommera filtrer et sera déclenchée sur le _click_).

#### Étape 2

Dans la partie script, ajouter une variable _recherche_ qui sera une chaîne de caractères vide.

#### Étape 3

Dans la partie script, ajouter une méthode _filtrer_ qui va filtrer la liste des pays en fonction de la saisie de l'utilisateur. On pourrait faire un nouvel appel API pour récupérer les pays en fonction de la saisie de l'utilisateur, mais on va faire un filtre en JavaScript sur cette exercice. Pour cela, on va utiliser la méthode `filter()` de JavaScript. Cette méthode prend en paramètre une fonction qui sera appelée pour chaque élément du tableau (léquivalent de faire une boucle sur toutes les réponses et comparer les valeurs). Cette fonction prend en paramètre un élément du tableau et doit retourner un booléen. Si le booléen est `true`, l'élément est conservé dans le tableau, sinon il est supprimé.

{% hint style="info" %}
* La méthode `filter()` retourne un nouveau tableau, elle ne modifie pas le tableau d'origine. [https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global\_Objects/Array/filter](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)
* La méthode `toLowerCase()` permet de transformer une chaîne de caractères en minuscules. La comparaison est sensible à la casse, donc on va transformer les chaînes de caractères en minuscules pour comparer les valeurs. [https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global\_Objects/String/toLowerCase](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/String/toLowerCase)
{% endhint %}

* La méthode `includes()` permet de savoir si une chaîne de caractères est incluse dans une autre chaîne de caractères. [https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global\_Objects/String/includes](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/String/includes)

#### Étape 4

Si vous testez votre code à ce stade et que vous avez remplacé la variable data par les résultats, alors les filtres suivants ne fonctionne plus car nous avons écrasé notre liste d'origine. Pour résoudre ce problème, il faut créer une variable qui contiendra la liste d'origine des pays et une autre qui contiendra la liste des pays filtrés. On va donc créer une variable _listeComplete_ qui contiendra la liste des pays d'origine et utiliser la variable _data_ qui contiendra; la première tous les pays également, puis la liste des pays filtrés lors d'une recherche.

### Exercice 2

Modifiez le code précédent pour que la recherche se fasse en temps réel, c'est à dire dès que l'utilisateur tape quelque chose dans le champ de saisie, la liste des pays sera filtrée.

{% embed url="https://youtu.be/vDddUkCY5Vs" %}
Exemple de rendu
{% endembed %}

### Exercice 3

On va ajouter une vue Recherche.vue (et le menu qui va avec) qui va permettre de rechercher des pays en fonction de la saisie de l'utilisateur. Cette fois, comme aucune donnée n'est chargée dès le début, on va utiliser l'API [https://restcountries.com/v3.1/name/....](https://restcountries.com/v3.1/name/...) pour récupérer les pays en fonction de la saisie de l'utilisateur. Il faut remplacer le `...` par la saisie de l'utilisateur.
