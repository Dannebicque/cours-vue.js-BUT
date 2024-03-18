# Séance 15-16 : Pour réviser

Cette séance vise à revoir l'ensemble des concepts vus sur le module VueJs.

L'API utilisée sera : [https://docs.magicthegathering.io/#api\_v1cards\_list](https://docs.magicthegathering.io/#api\_v1cards\_list)

Le travail à faire est le suivant :

* Installer un nouveau projet
* Définir une navigation avec les routes suivantes :&#x20;
  * Accueil
  * Recherche
  * Afficher les cartes
  * Afficher les sets
* Pour la recherche on pourra :&#x20;
  * Recherche un texte libre sur les cartes
  * Filtrer les cartes par couleur
  * Une liste des types (en récupérant les types depuis l'API pour alimenter une liste déroulante
* Pour la page des cartes
  * On pourra naviguer dans les éléments précédents/Suivants
  * On pourra choisir globalement d'afficher les images des cartes ou pas
  * Les cartes seront des composants
    * L'image, la couleur et le cout en mana (manacost), le type
    * Un lien permettra d'accéder à la fiche détaillée
    * Un bouton permettra d'ajouter la carte dans un panier fictif qui sera affiché sur toutes les pages
* La page détaillée d'une carte comprendra les éléments.
  * Le choix d'une langue affichera le nom de la carte dans la langue choisie (et uniquement celle ci)
* Afficher un panier des cartes selectionnées
