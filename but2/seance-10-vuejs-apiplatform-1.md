# Séance 10 : VueJs et ApiPlatform (1)

## Afficher les fournisseurs

Vous allez ajouter une nouvelle vue pour afficher la liste des fournisseurs. Pour cela, en vous basant sur les séances précédentes, suivez les étapes suivantes :

* Créer une vue `Fournisseurs.vue` dans le dossier `src/views`
* Ajouter la route associée
* Ajouter le lien dans le menu
* Ajouter dans Fournisseurs.vue, une variable `data` qui contiendra la liste des fournisseurs, et une méthode `onMounted` qui appellera l'API pour récupérer la liste des fournisseurs. Regardez dans ApiPlatform, l'URL à utiliser.
* Affichez les fournisseurs : Libellé et adresse. Attention, l'adresse pourrait être vide/null, il faut donc vérifier avant d'afficher l'adresse.

## Ajouter un fournisseur

Vous allez maintenant ajouter un formulaire pour ajouter un nouveau fournisseur. Dans un premier temps, nous allons ajouter simple le libellé du fournisseur.

Pour des raisons de simplicités, nous allons utiliser un formulaire HTML classique, et non un formulaire avec VueJs. Pour cela, vous allez créer un formulaire HTML dans la vue `Fournisseurs.vue`. Ce formulaire contiendra un champ `input` pour le libellé du fournisseur, et un bouton pour envoyer le formulaire.

On associera un v-model sur le champ input pour récupérer la valeur du champ (nommé libelle). On ajoutera un `@click` sur le formulaire pour intercepter l'événement de clic sur le bouton du formulaire (on pourrait le faire avec un `@submit`).

La méthode appelée (`ajouter` par exemple) devra récupérer la valeur du champ libellé, et appeler l'API pour ajouter le fournisseur.

De nouveau regarder dans ApiPlatform, l'URL à utiliser. Cette requête doit être en POST, et le corps de la requête doit contenir le libellé du fournisseur.

Avec fetch cela pourrait ressembler à ça :

```javascript
await fetch('http://127.0.0.1:8000/api/fournisseurs', {
    method: 'POST',
    headers: {
       'Content-Type': 'application/json'
    },
    body: JSON.stringify({
       libelle: libelle.value
    })
})
```

avec axios :

```javascript
await axios.post('http://127.0.0.1:8000/api/fournisseurs', {
    libelle: libelle.value
})
```

### Exercice

Mettre en place le formulaire et la méthode pour ajouter un fournisseur.

## Raffraichir la liste des fournisseurs

Comme vous pouvez le constater, le fournisseur est ajouté dans la base de données, mais ce n'est pas visible dans la liste des fournisseurs. Pour cela, il faut raffraichir la liste des fournisseurs.

Pour cela, une fois le fournisseur ajouté, il faudrait actualiser la liste des fournisseurs. On pourrait reprendre un code similaire à celui de la méthode `onMounted` pour récupérer la liste des fournisseurs. Mais il y a un problème : la méthode `onMounted` est appelée une seule fois, au chargement de la page. Il faut donc trouver une autre solution.

Pour éviter de copier/coller du code et de le dupliquer, on va écrire une fonction qui récupère la liste des fournisseurs, et qui sera appelée par la méthode `onMounted` et par la méthode qui ajoute un fournisseur, et peut être à d'autres instants.

```javascript
async function getFournisseurs {
    // code pour récupérer la liste des fournisseurs
}
```

On peut maintenant appeler cette fonction dans la méthode `onMounted` et dans la méthode qui ajoute un fournisseur.

```javascript
onMounted(async () => {
  getFournisseurs()
})
```

### Exercice

Mettre en place le raffraichissement de la liste des fournisseurs lors de l'ajout d'un nouveau fournisseur.

## Ajouter l'adresse

Maintenant que vous avez mis en place l'ajout d'un fournisseur, vous allez ajouter l'adresse du fournisseur.

Pour cela, vous allez modifier le formulaire HTML pour ajouter un champ pour l'adresse. Vous allez également modifier la méthode qui ajoute un fournisseur pour envoyer l'adresse.

L'ajout de l'adresse est un peu particulier, car c'est une relation entre Fournisseur et Adresse. Mais si vous regardez dans ApiPlatform, vous verrez que l'adresse peut être envoyée dans le corps de la requête, directement dans l'objet fournisseur.

```json
{
  "libelle": "string",
  "adresse": {
    "rue": "string",
    "codepostal": "string",
    "ville": "string",
    "fournisseurs": [
      "string"
    ]
  },
  "articles": [
    {
      "designation": "string",
      "description": "string",
      "quantite": 0,
      "prix": 0,
      "fournisseur": "string"
    }
  ]
}
```

Vous auriez même la possibilité d'ajouter un article. Mais pour l'instant, nous allons juste ajouter l'adresse.

Nous devons donc envoyer un objet json ressemblant à ça :

```json
{
  "libelle": "string",
  "adresse": {
    "rue": "string",
    "codepostal": "string",
    "ville": "string"
  }
}
```

### Exercice

* Ajouter les champs pour l'adresse dans le formulaire HTML (rue, codepostal, ville), déclarer les variables dans la vue, et les associer au v-model.
* Modifier la méthode qui ajoute un fournisseur pour envoyer l'adresse.
