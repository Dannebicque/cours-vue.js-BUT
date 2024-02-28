# Séance 14 : Exemple d'un panier simple + LocalStorage

## Objectif

L'objectif de ces séances est de mettre en place un exemple de panier en utilisant le `LocalStorage` de JavaScript, et permettre à l'utilisateur de gérer son panier en ajoutant, supprimant et modifiant les quantités des produits. Le tout en utilisant VueJS.

## Stockage dans le localStorage

Une solution simple consiste à stocker les informations dans le localStorage ou sessionStorage.
Lire ici pour plus de détails : https://fr.javascript.info/localstorage

Les principales caractéristiques de localStorage sont les suivantes :

* Partagé entre tous les onglets et fenêtres d’une même origine.
* Les données n’expirent pas. Il reste après le redémarrage du navigateur et même le redémarrage du système d’exploitation.

L’objet sessionStorage est beaucoup moins utilisé que localStorage.

* Les propriétés et les méthodes sont les mêmes, mais c’est beaucoup plus limité :
  * Le sessionStorage n’existe que dans l’onglet actuel du navigateur.
  * Un autre onglet avec la même page aura un stockage différent.
  * Mais il est partagé entre les iframes du même onglet (en supposant qu’ils proviennent de la même origine).
  * Les données survivent à l’actualisation de la page, mais pas à la fermeture/ouverture de l’onglet.

Nous allons utiliser localStorage pour stocker les produits du panier. Les méthodes principales sont :

 * `setItem(key, value)` – stocke la paire clé/valeur.
 * `getItem(key)` – récupère la valeur par clé.
 * `removeItem(key)` – supprime la clé avec sa valeur.
 * `clear()` – supprime tout.
 * `key(index)` – récupère la clé sur une position donnée.
 * `length` – le nombre d’éléments stockés.

## Mise en place du panier

* Ajouter une route et un composant vue associé (Panier.vue) pour accéder au panier.
* Dans un premier temps on va faire fonctionner les éléments simplement (sans composant, sans localStorage)
  * Définissez un tableau de produits (objet json) dans une constante `cart`, qui contient deux produits définis par un nom, une quantité et un prix unitaire
  * Afficher ce tableau dans le composant Panier.vue (avec une boucle `v-for` et dans un tableau ou une liste pour le moment.)
  * Ajoutez pour chaque produit un bouton + et un bouton - pour gérer les quantités et un bouton pour supprimer le produit du panier.
  * Associez les méthodes pour gérer les quantités et la suppression des produits.

## Stockage dans le localStorage

Nous allons modifier le panier pour stocker les produits dans le localStorage.

* Lisez le tableau `cart` depuis le localStorage et affichez-le dans le composant Panier.vue.
* Stockez le tableau `cart` dans le localStorage.
* Modifiez le tableau `cart` dans le localStorage lorsque l'utilisateur ajoute, supprime ou modifie les quantités des produits.

### Lire le tableau depuis le localStorage

```javascript
let cart = JSON.parse(localStorage.getItem('cart')) || [];
```

Si on veut l'utiliser dans un composant Vue, on peut déclarer une constante et la rendre réactive avec ref() :

```javascript
    const cart = ref(JSON.parse(localStorage.getItem('cart')) || [])
```

### Stocker le tableau dans le localStorage

```javascript
localStorage.setItem('cart', JSON.stringify(cart.value));
```

`cart.value` fait référence à la valeur de la variable réactive cart.

L'objet cart va être mis à jour à plusieurs endroits dans le composant Panier.vue. Il faudrait donc penser à mettre à jour le localStorage à chaque fois que le tableau cart est modifié. Mais on peut utiliser un watcher pour cela.

#### Utiliser un watcher pour mettre à jour le localStorage

```javascript
watch(cart, () => {
  localStorage.setItem('cart', JSON.stringify(cart.value));
});
```

### Modifier le tableau cart dans le localStorage

Pour cette partie, il faut associer les méthodes pour gérer les quantités et la suppression des produits. Les données du localStorage sont dans la variable `cart` et on peut les modifier directement. Cette variable est surveillée, donc le localStorage sera mis à jour automatiquement.

## Exercice

Faire fonctionner le panier et la sauvegarde dans le localStorage.

