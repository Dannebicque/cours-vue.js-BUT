# Séance 9 : VueJs et ApiPlatform (2/2)

## Supprimer un fournisseur

Pour cela, vous allez ajouter un bouton dans la liste des fournisseurs. Ce bouton va appeler une fonction (supprimer) qui va supprimer le fournisseur. Pour connaitre l'id du fournisseur, vous allez utiliser devoir passer à la méthode supprimer l'id du fournisseur. Vous noterez que dans ApiPlatform, vous avez deux "id" :
- id : un nombre (par défaut), qui correspondant à l'ID de votre entité dans la base de données
- @id : une chaîne de caractères, qui correspond à l'URL (on parle même d'URI : *Uniform Ressource Identifier*) de votre entité.

Vous pouvez utiliser celui de votre choix.
Le bouton va donc ressembler à ça :

```html
<button @click="supprimer(fournisseur.id)">Supprimer</button>

ou

<button @click="supprimer(fournisseur['@id'])">Supprimer</button>
```

Il faut ensuite ajouter la méthode supprimer dans la partie javascript. Cette méthode doit faire une requête HTTP DELETE sur l'URL du fournisseur à supprimer. Avec fetch cela pourrait ressembler à ça, si vous avez utilisé `id` :

```javascript
async function supprimer(id) {
  await fetch(`http://127.0.0.1:8000/api/fournisseur/${id}`, {
    method: 'DELETE',
    headers: {
      'Content-Type': 'application/json',
    },
  })
}  
```

Si vous avez passé `@id` du fournisseur, vous pouvez utiliser l'URL suivante :

```javascript
async function supprimer(id) {
  await fetch(`http://127.0.0.1:8000${id}`, {
    method: 'DELETE',
    headers: {
      'Content-Type': 'application/json',
    },
  })
}
```

Avec axios, cela ressemblerait à ça :

```javascript
async function supprimer(id) {
  await axios.delete(`http://127.0.0.1:8000/api/fournisseur/${id}`)
}
```

### Exercice

* Mettre en place le bouton et la méthode pour supprimer un fournisseur.
* Mettre en place le raffraichissement de la liste des fournisseurs lors de la suppression d'un fournisseur.

## Modifier un fournisseur

Pour modifier un fournisseur, vous allez ajouter un bouton dans la liste des fournisseurs. Ce bouton va appeler une fonction (modifier) qui va modifier le fournisseur. Comme pour la suppression vous devez passer l'id du fournisseur à la méthode modifier.

Dans VueJs nous pourrions avoir deux composants, un pour avoir un formulaire de création, et un autre pour un formulaire de modification. Ici nous allons exploiter notre forulaire de création pour le modifier pour qu'il puisse être utilisé pour la modification.

Nous allons devoir sauvegarder si un id est passé (et donc on souhaite modifier un fournisseur) ou non (et donc on souhaite créer un fournisseur). Pour cela, nous allons utiliser une variable `id` dans le composant VueJs. Cette variable sera initialisée à `''`. Si un id est passé, alors nous allons faire une requête HTTP PUT sur l'URL avec un id, sinon nous allons faire une requête HTTP POST sur l'URL sans id, pour une création.

La méthode modifier, qui va récupérer les informations du fournisseur à modifier, va ressembler à ça :

```javascript
async function modifier(_id) {
  await fetch(...votreurl...).then((response) => {
        response.json().then((fournisseur) => {
          id.value = fournisseur.id
          libelle.value = fournisseur.libelle
          ...
        })
      },
  )
}
```

Notez que nous faisons ici une requête HTTP GET pour récupérer les informations du fournisseur à modifier. Mais nous aurions pu passer l'objet complet fournisseur, plutôt que son id et remplir directement les champs du formulaire.

```javascript
async function modifier(_fournisseur_) {
    id.value = fournisseur.id
    libelle.value = fournisseur.libelle
    ...
}
```

Il faut modifier la méthode associée au bouton "Ajouter" pour qu'elle fasse une requête HTTP PUT si un id est passé, et une requête HTTP POST si aucun id n'est passé.

```javascript
async function ajouter() {
    //on prépare les données à envoyer
  const dataPost = JSON.stringify({
    libelle: libelle.value,
    adresse: {
      rue: rue.value,
      ...
    },
  })

  if (id.value !== '') {
    await fetch(..url.., {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json',
      },
      body: dataPost,
    })
  } else {
    ...le code d'ajout vu précédemment...
  }
}
```

### Exercice

* Mettre en place le bouton et la méthode pour modifier un fournisseur.
* Mettre en place le raffraichissement de la liste des fournisseurs lors de la modification d'un fournisseur.

Vous venez de mettre en place un CRUD complet en VueJs.