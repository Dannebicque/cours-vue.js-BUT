# Séance 6a – Introduction à TypeScript

## 🎯 Objectifs

- Comprendre les bases de TypeScript.
- Ajouter du typage dans un projet Vue/Nuxt.

## 📖 Partie théorique

### Pourquoi TypeScript ?

- Superset de JavaScript avec typage statique.
  - Tout code JavaScript valide est aussi du TypeScript.
  - On peut progressivement ajouter du typage.
  - Pas besoin de tout réécrire.
- Détection d’erreurs à la compilation.
- Meilleure autocomplétion et documentation.
- Adoption croissante dans l’écosystème JS.
- Utilisé par des frameworks comme Angular, Vue, React.

### Approche de TypeScript

> TypeScript est un langage de programmation libre développé par Microsoft qui a pour but d'améliorer et de sécuriser la production de code JavaScript. Il s'agit d'un sur-ensemble syntaxique strict de JavaScript (c'est-à-dire que tout code JavaScript correct peut être utilisé avec TypeScript). Le code TypeScript est transcompilé en JavaScript et peut ainsi être interprété par n'importe quel navigateur web ou moteur JavaScript. (https://fr.wikipedia.org/wiki/TypeScript)

TypeScript permet un typage statique optionnel des variables et des fonctions, la création de classes et d'interfaces, l'import de modules, tout en conservant l'approche non-contraignante de JavaScript.

La documentation officielle est très complète : https://www.typescriptlang.org/

### Types de base

#### Types primitifs

- `string`
- `number`
- `boolean`
- `null`
- `undefined`
- `void`
- `any`

Exemples :

```ts
let name: string = 'Alice'
let age: number = 30
let isAdmin: boolean = false
let nothing: null = null
let notDefined: undefined = undefined
let noReturn: void = undefined
let anything: any = 'Could be anything'
```

#### Types complexes

- `array` : `string[]` ou `Array<string>`
- `tuple` : `[string, number]`
- `enum` : énumérations
- `object` : `{ key: value }`

Exemples :

```ts
let names: string[] = ['Alice', 'Bob']
let user: [string, number] = ['Alice', 30]
enum Role { Admin, User, Guest }
let currentRole: Role = Role.Admin
let person: { name: string; age: number } = { name: 'Alice', age: 30 }
``` 

#### Fonctions

```ts
function add(a: number, b: number): number {
    return a + b
}
const greet = (name: string): string => `Hello, ${name}`
```

#### Interfaces et types personnalisés

```ts
interface User {
    id: number
    name: string
    email?: string // optionnel
}
type ID = string | number // union de types
```

Utilisation

```ts
let user: User = { id: 1, name: 'Alice' }
let userId: ID = 123
userId = 'abc' // aussi valide
```

### Le fichier de configuration `tsconfig.json`

TypeScript utilise un fichier `tsconfig.json` pour configurer le compilateur.

Exemple minimal :

```json
{
  "compilerOptions": {
    "target": "ESNext",
    "module": "ESNext",
    "strict": true,
    "moduleResolution": "node",
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*.ts", "src/**/*.vue"],
  "exclude": ["node_modules"]
}
```

### Ajouter TypeScript à un projet Vue/Nuxt

Avec vuejs 3 et Nuxt 3 ou 4, TypeScript est nativement supporté. Il vous suffit de renommer les fichiers `.js` en `.ts` avec `<script lang="ts">` pour les fichiers Vue.

Exemple dans un composant Vue :

```vue
<template>
    <div>
        <h1>{{ title }}</h1>
        <p>{{ description }}</p>
    </div>
</template>
<script lang="ts" setup>
import { ref } from 'vue'
const title = ref<string>('Hello TypeScript')
const description = ref<string>('This is a Vue component with TypeScript')
</script>
<style scoped>
/* styles */
</style>
```

