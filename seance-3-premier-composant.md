# Séance 3 : Écouter des événements

{% hint style="info" %}
Cette partie est une traduction/adaptation de la documentation officielle située : [https://vuejs.org/guide/essentials/event-handling.html](https://vuejs.org/guide/essentials/event-handling.html)
{% endhint %}

## Écouter des événements

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

ref() permet de rendre la propriété `count` réactive. Il faut ajouter l'import suivant :

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

Notre bouton appelle ici une méthode nommée `greet`. Cette méthode est définie dans la partie script. Elle affiche une alerte avec le message "Hello Vue.js!". Elle prend, automatiquement, en paramètre un événement natif du DOM.

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

## Événement de touche

Il est possible d'utiliser les événements de touche pour écouter les événements clavier. Par exemple, si on veut écouter l'événement `keyup` sur un champ de saisie, on peut faire :

```html
<input @keyup.enter="onEnter">
```

Ici, on écoute l'événement `keyup` sur le champ de saisie et on exécute la méthode `onEnter` lorsque la touche `Enter` est pressée.

## Exercices

### Exercice 1

Créer un composant `Counter` qui affiche deux boutons et un compteur. Le premier bouton permet d'incrémenter le compteur. Le compteur doit être réinitialisé à 0 lorsque l'on clique sur le second bouton.

### Exercice 2

Faire une boucle permettant d'afficher 4 compteurs. Testez le comportement des boutons.
