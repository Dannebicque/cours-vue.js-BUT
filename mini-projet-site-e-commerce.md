# 5 dernières séances : Mini projet e-commerce

## Objectif

L'objectif de ces séances est de mettre en place un exemple de site e-commerce en utilisant VueJS et une API REST. Nous allons créer un site e-commerce simple avec des produits, des catégories, un panier et un formulaire de commande.

## Mise en place du projet

### API REST

Nous allons utiliser une API REST pour récupérer les données des produits et des catégories. Vous pouvez utiliser l'API de démonstration ici : [https://dummyjson.com/docs/products](https://dummyjson.com/docs/products)

### VueJS

Nous allons créer un site e-commerce simple avec les fonctionnalités suivantes :

* Afficher la liste des produits (avec la pagination, l'ensemble des éléments correctements présentés et une image affichée)
* Afficher la liste des catégories (depuis l'API)
* Afficher les détails d'un produit dans une page dédiée. Pouvoir afficher les images dans une modale.
* Ajouter un produit au panier avec une confirmation d'ajout et l'état du panier visible sur toutes les pages
* Afficher le panier
* Le panier doit être visible sur toutes les pages (avec le nombre de produits et le montant total
* Supprimer un produit du panier (avec une modal de confirmation) ou modifier la quantité (supprier si quantité = 0). Le montant du panier est mis à jour en temps réel.
* Sauvegarder le panier dans le `localStorage`
* Formulaire de commande avec validation des champs

### Mise en page

Vous pouvez utiliser un framework CSS comme Bootstrap ou TailwindCSS pour la mise en page. Vous pouvez également regarder du côté de Vuetify pour un framework CSS compatible avec VueJS ou d'autres ici : https://ui-libs.vercel.app/).
