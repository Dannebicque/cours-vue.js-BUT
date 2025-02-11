# Séance 8 : Watch et évents

## Watch

Sur la séance 4 nous avons vu comment passer des paramètres aux composants enfants (les **props**). Mais si ce paramètre change, comment le composant enfant peut-il le savoir ? Par défaut le composant enfant ne verra jamais le changement de sa "props".

C'est là qu'intervient la propriété **watch** de VueJs. Elle permet de surveiller les changements d'une propriété et d'effectuer une action en conséquence.

### Cas classique

La documentation de ce point se trouve ici : [https://vuejs.org/guide/essentials/watchers.html](https://vuejs.org/guide/essentials/watchers.html).

Exemple d'utilisation de la propriété **watch** :

```javascript
<script setup>
import { ref, watch } from 'vue'

const count = ref(0)

watch(count, (newValue, oldValue) => {
    console.log(`count changed from ${oldValue} to ${newValue}`)
})
</script>
```

Dans cet exemple, nous avons une variable `count` qui est réactive. Nous avons ensuite utilisé la fonction `watch` pour surveiller les changements de cette variable. A chaque changement, la fonction passée en deuxième paramètre est appelée. Cette fonction prend deux paramètres : la nouvelle valeur et l'ancienne valeur.

### Cas des props

Dans le cas des **props**, nous pouvons aussi utiliser la propriété **watch** pour surveiller les changements de ces **props**. Mais les props sont un peu particulière, et nous devons légèrement modifier la manière de les définir (en déclarant une constante contenant l'ensemble de nos props).

Puis le watch ressemblerait à ça :

```javascript
<script setup>
import {  watch, ref } from 'vue'

const props = defineProps(['pays'])

watch(() => props.pays, (newValue, oldValue) => {
//la valeur de la props est changée et newValue contient la nouvelle valeur. Le composant est actualisé.
  console.log(newValue) 
});
</script>
```

Vous noterez la syntaxe particulière de la fonction `watch` qui prend une fonction fléchée en premier paramètre, et une fonction en deuxième paramètre.

La première permet de retourner la variable à surveiller, et la seconde est la fonction appelée lors du changement de cette variable.

{% hint style="info" %}
L'utilisation de watch est nécessaire si le changement de valeur d'une props doit déclencher une action dans votre composant enfant (un appel Api, une mise à jour d'autres variables, ...). Dans le cas contrainte, la partie "vue" va réagir directement à la modification de la props. Les props sont réactives.
{% endhint %}

### Exercice 1

* Dans le composant **Pays.vue**, ajouter un checkbox avec un v-model associé qui va déterminer si les images des drapeaux sont affichées ou non.
* Passer cette valeur aux composants enfants (**CardPays.vue**) pour qu'il puisse afficher ou non les images des drapeaux en fonction de la valeur du checkbox.
* Utiliser la propriété **watch** pour surveiller les changements de cette valeur dans le composant **CardPays.vue** et afficher un message dans la console.

{% embed url="https://youtu.be/YVa936s_tsY" %}

## Événements

De manière un peu similaire, comment un composant enfant peut-il communiquer avec un composant parent ? Par exemple, comment un composant enfant peut-il demander à son parent de faire quelque chose ou de se mettre à jour en fonction d'une action ?

C'est là qu'interviennent les **événements**. Un composant enfant peut émettre un événement, et un composant parent peut écouter cet événement.

### Émettre un événement

Pour émettre un événement, on utilise la fonction `emit`.

{% code title="ComposantEmeteur.vue" lineNumbers="true" %}
```javascript
<script setup>
import { ref } from 'vue'
const emit = defineEmits(['emitEvent'])

const count = ref(0)

function increment() {
    count.value++
    emit('emitEvent', count.value)
    // ou sans paramètre
    // emit('emitEvent')
}

</script>
```
{% endcode %}

Dans l'exemple ci-dessus l'événement `increment` est émis avec la valeur de `count` comme paramètre. Il est émis à chaque fois que la fonction `increment` est appelée.

Il est aussi possible de passer un événement depuis la partie template du composant, par exemple sur un bouton ou un input.

sans paramètre :&#x20;

```javascript
<button @click="$emit('emitEvent')">Increment</button>
```

avec un paramètre :

```javascript
<button @click="$emit('emitEvent', count)">Increment</button>
```

### Écouter un événement

Pour écouter un événement, on utilise la syntaxe `v-on` ou `@` pour les événements.

{% code title="ComposantParent.vue" lineNumbers="true" %}
```html
<template>
  <div>
    <ComposantEmeteur @emitEvent="increment">Increment</ComposantEmeteur>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment(value) {
    count.value = value
}
</script>
```
{% endcode %}

Dans cet exemple, le composant parent écoute l'événement `emitEvent` émis par le composant enfant. Lorsque l'événement est émis, la fonction `increment` est appelée avec la valeur passée par l'événement.

### Exercice 2

* Dans le composant **CardPays.vue**, ajouter un checkbox afin de pouvoir sélectionner un pays.
* Lorsque le checkbox est coché, émettre un événement pour indiquer au composant parent que le pays est sélectionné.
* Dans le composant **Pays.vue**, écouter cet événement et afficher le nombre de pays selectionnés.
* Gérer le cas où je déselectionne un pays.
* Dans le composant **Pays.vue**, écouter cet événement et afficher un message indiquant que le pays est sélectionné.

{% embed url="https://youtu.be/u4e9NVpch04" %}
