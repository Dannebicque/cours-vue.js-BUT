# Séance 4 : Manipuler les formulaires

{% hint style="info" %}
Cette partie est une traduction/adaptation de la documentation officielle située : [https://fr.vuejs.org/guide/essentials/forms.html](https://fr.vuejs.org/guide/essentials/forms.html)
{% endhint %}

## Directive `v-model`

La directive `v-model` est l'équivalent de `v-bind` et `v-on` en même temps. Elle permet de lier un élément du DOM à une propriété du composant.

```html
<input v-model="message" placeholder="edit me">
<p>Message is: {{ message }}</p>
```

```javascript
const message = ref('Hello Vue!')
```

`v-model` peut être utilisé sur des entrées de différents types, des éléments `<textarea>`, et `<select>`. Il s'étend automatiquement aux différentes paires de propriétés et d'événements du DOM en fonction de l'élément sur lequel il est utilisé :

Les éléments `<input>` de type texte et `<textarea>` utilisent la propriété _value_ et l'événement _input_ ; `<input type="checkbox">` et `<input type="radio">` utilisent la propriété _checked_ et l'événement _change_ ; `<select>` utilise _value_ comme propriété et _change_ comme événement.

## Différents types de champs

Un ensemble d'exemple est disponible directement sur la documentation :

* Champs Textes : [https://vuejs.org/guide/essentials/forms.html#text](https://vuejs.org/guide/essentials/forms.html#text)
* Champs TextArea avec plusieurs lignes : [https://vuejs.org/guide/essentials/forms.html#multiline-text](https://vuejs.org/guide/essentials/forms.html#multiline-text)
* Champs Checkbox unique ou en récupérant un tableau : [https://vuejs.org/guide/essentials/forms.html#checkbox](https://vuejs.org/guide/essentials/forms.html#checkbox)
* Champs radio : [https://vuejs.org/guide/essentials/forms.html#radio](https://vuejs.org/guide/essentials/forms.html#radio)
* Champs select (simple ou multiple) : [https://vuejs.org/guide/essentials/forms.html#select](https://vuejs.org/guide/essentials/forms.html#select)

## Modificateurs

### .lazy

Par défaut, `v-model` synchronise l'entrée avec les données après chaque événement input. Vous pouvez ajouter le modificateur `lazy` pour enclencher la synchronisation après les événements change :

```html
<!-- synchronisé après "change" au lieu de "input" -->
<input v-model.lazy="msg" />
```

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

## Exercices

### Exercice 1

Créer un formulaire avec les champs suivants :

* Nom
* Prénom

Affichez les valeurs saisies dans une balise `<p>`.

### Exercice 2

Créer un formulaire avec les champs suivants :

* Nom
* Prénom

Affichez les valeurs saisies lorsque l'on appuie sur un bouton

### Exercice 3

Créer un formulaire avec les champs suivants :

* Nom
* Prénom
* Genre (radio)

Afficher une phrase différente selon le genre sélectionné.
