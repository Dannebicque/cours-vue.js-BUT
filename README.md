# Présentation

## Quels sont les problèmes du développement front "classique" ?

* Chaque page web HTML est codée intégralement, et peu réutilisable pour d'autres pages (hors include en php).
* Une page web est relativement figée (sans javascript), et ne permet pas une interaction "temps réel" (un champ complété alimente une zone directement).
* Le manque de découpage de la page ne permet pas de réutiliser tout ou partie de la page sur une autre page

## Qu'est ce que Vue.js ?

> Vue.js (aussi appelé plus simplement Vue), est un [framework JavaScript](https://fr.wikipedia.org/wiki/Framework\_JavaScript) [open-source](https://fr.wikipedia.org/wiki/Open\_source) utilisé pour construire des interfaces utilisateur et des [applications web monopages](https://fr.wikipedia.org/wiki/Application\_web\_monopage). Vue a été créé par Evan You et est maintenu par lui et le reste des membres actifs de l'équipe principale travaillant sur le projet et son écosystème.

{% embed url="https://vuejs.org/" %}
La documentation officielle
{% endembed %}

Vue.js apporte la notion de composant (qui n'est pas nouvelle en soit, HTML intégre déjà la notion de composant), et permet un "binding" complet entre les éléments (mise à jour réciproque des éléments).

Vue.js apporte une structuration forte du développement front, une réutilisabilité importante avec la notion de composant et améliore l'interactivité de l'application et l'expérience des utilisateurs.

Vue.js nécessite un environnement NodeJs pour fonctionner. Le code écrit n'est pas directement exploitable par les navigateurs et implique un mécanisme de "traduction".&#x20;

Un automatisateur de tâche est indispensable, tel que webpack ou, ViteJs qui est recommandé avec la version 3 de Vue.js.

Vue.js implique également une bonne connaissance de la syntaxe JavaScript et en particulier les versions ECMAScript 6 (ES6) et suivantes.

## Quelles sont les alternatives à Vue.js

Les "concurrents" directs de Vue.js sont :&#x20;

* AngularJs (dont le développeur de Vue.js est issu), précurseur sur l'approche par composant, fondé par Google.
* ReactJs, développé par Facebook

Lire par exemple :&#x20;

{% embed url="https://kinsta.com/fr/blog/vue-vs-react/" %}

{% embed url="https://pagepro.co/blog/react-vs-vue-comparison/" %}
