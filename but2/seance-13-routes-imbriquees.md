# Séance 12 : Routes imbriquées // Animations et transitions

## Objectifs

L'objectif de cette séance est de mettre en place des routes imbriquées et de gérer les transitions entre les différentes pages/composants d'une page.

## Contexte

Nous allons ajouter une nouvelle route et vue associée afin de pouvoir lister les pays par région du monde (Europe, Asie, ...). Dans cette même page, nous afficherons la liste des pays à chaque changement de région (on pourrait imaginer un système d'onglets par exemple).

Nous aurons donc une partie de la page qui restera fixe (la liste des régions) et une autre qui changera en fonction de la région sélectionnée.

## Rappels sur les routes

Jusqu'a présent nous avons des routes simples, c'est à dire que nous avons une seule vue par route. La navigation est unique (dans App.vue), et la zone qui contient le résultat (router-view) est remplacée à chaque changement de route.

Dans le cas de routes imbriquées, nous allons avoir une route principale, et des routes secondaires qui seront affichées dans une nouvelle zone (router-view) contenu dans la vue "parent".

Exemple :

```
/user/johnny/profile                     /user/johnny/posts
+------------------+                  +-----------------+
| User             |                  | User            |
| +--------------+ |                  | +-------------+ |
| | Profile      | |  +------------>  | | Posts       | |
| |              | |                  | |             | |
| +--------------+ |                  | +-------------+ |
+------------------+                  +-----------------+
```

Source : [https://router.vuejs.org/guide/essentials/nested-routes.html](https://router.vuejs.org/guide/essentials/nested-routes.html)

## Mise en place

En reprenant les éléments des précédentes séances mettre en place les éléments suivants :

* Une vue "Regions" qui contiendra la liste des régions du monde. La liste sera un tableau javascript contenant les noms des régions.
* Une route et un lien dans le menu principal pour accéder à cette vue.
* Une vue FicheRegion qui contiendra la liste des pays pour une région donnée. La liste sera récupérée depuis l'API, et utilisera le composant CardPays déjà créé.

{% embed url="https://youtu.be/GS4HA7ospxA" %}

## Ajout des routes imbriquées.

Une route imbriquée comme son nom l'indique est une route qui est imbriquée dans une autre route. Cela signifie que la route enfant est accessible à partir de la route parente.

Cela se traduit donc dans le router par un tableau `children` dans la route parente.

Exemple (en lien avec le schéma précédent) :

```javascript
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User,
      children: [
        {
          // UserProfile will be rendered inside User's <router-view>
          // when /user/:id/profile is matched
          path: 'profile',
          component: UserProfile
        },
        {
          // UserPosts will be rendered inside User's <router-view>
          // when /user/:id/posts is matched
          path: 'posts',
          component: UserPosts
        }
      ]
    }
  ]
})
```

## Exercice

Mettre en place les routes imbriquées pour afficher la liste des pays par région. Vous devez donc :

* Ajouter une route enfant à la route "régions", qui va prendre en paramètre le nom de la région.
* Ajouter les router-link sur les régions de la vue Regions.vue pour accéder à la vue FicheRegion.
* Ajouter un router-view dans la vue Regions.vue pour afficher la vue associée à la partie enfant.

**Tester votre code. Est-ce que tout fonctionne correctement ?**

## Watch...

Le changement de région, modifie bien l'URL, mais le contenu ne change pas. Pourquoi ? Souvenez vous de la séance 8. Le composant FicheRegion est créé au premier appel, avec la donnée passée dans la route. VueJs 3, ne viendra plus le modifier, sauf si on lui explique qu'il doit le mettre à jour à chaque changement de route. Ce mécanisme peut surprendre, mais il est logique. En effet, si on a besoin de mettre à jour un composant, c'est que l'on a besoin de le détruire et de le recréer. C'est ce que fait VueJs 3, mais il ne le fait pas automatiquement. Ce mécanisme permet, entre-autre d'améliorer les performances de VueJs.

Nous devons donc ajouter un watcher sur le changement de route, pour mettre à jour la donnée de la région.

## Exercice

* Ajouter un watch sur le paramètre (voir séance 8) pour mettre à jour la donnée de la région.
* Simplifier le code pour n'avoir qu'une seule fonction qui fait l'appel API et la réutiliser dans le watch et dans le mounted.

{% embed url="https://youtu.be/s1FoTKTO6U8" %}

## Transitions

Nous allons maintenant ajouter des transitions entre les différentes vues. Pour cela, nous allons utiliser les transitions de VueJs.

VueJs propose nativement un mécanisme de transition entre les éléments. Pour cela, il suffit de déclarer une balise `<transition>` autour de l'élément que l'on veut animer. [https://vuejs.org/guide/built-ins/transition.html](https://vuejs.org/guide/built-ins/transition.html)

Exemple (issue de la documentation) :

```html
<button @click="show = !show">Toggle</button>
<Transition>
  <p v-if="show">hello</p>
</Transition>
```

Et il faut ajouter le CSS associé :

```css
.v-enter-active,
.v-leave-active {
  transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}
```

Ces classes sont automatiquements ajoutées par VueJs lors de l'ajout ou de la suppression de l'élément.

## Exercice

Ajouter une transition entre la vue Regions et la vue FicheRegion. Utilisez la transition "fade" de la documentation.

Ceci est un aperçu très rapide de la partie transition/animation. VueJs propose de nombreuses autres possibilités. Il est aussi assez simple de gérer des effets avec des animations en CSS. Vous trouverez des éléments dans la documentation : [https://vuejs.org/guide/extras/animation.html](https://vuejs.org/guide/extras/animation.html).
