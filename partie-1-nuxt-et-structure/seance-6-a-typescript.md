# Séance 1-a : TypeScript pour NuxtJs / VueJs

## 🎯 Objectifs

* Comprendre les bases de TypeScript.
* Ajouter du typage dans un projet Vue/Nuxt.

## 📖 Partie théorique

### Pourquoi TypeScript ?

* Superset de JavaScript avec typage statique.
  * Tout code JavaScript valide est aussi du TypeScript.
  * On peut progressivement ajouter du typage.
  * Pas besoin de tout réécrire.
* Détection d’erreurs à la compilation.
* Meilleure autocomplétion et documentation.
* Adoption croissante dans l’écosystème JS.
* Utilisé par des frameworks comme Angular, Vue, React.

### Approche de TypeScript

> TypeScript est un langage de programmation libre développé par Microsoft qui a pour but d'améliorer et de sécuriser la production de code JavaScript. Il s'agit d'un sur-ensemble syntaxique strict de JavaScript (c'est-à-dire que tout code JavaScript correct peut être utilisé avec TypeScript). Le code TypeScript est transcompilé en JavaScript et peut ainsi être interprété par n'importe quel navigateur web ou moteur JavaScript. (https://fr.wikipedia.org/wiki/TypeScript)

TypeScript permet un typage statique optionnel des variables et des fonctions, la création de classes et d'interfaces, l'import de modules, tout en conservant l'approche non-contraignante de JavaScript.

La documentation officielle est très complète : https://www.typescriptlang.org/

### Types de base

#### Types primitifs

* `string`
* `number`
* `boolean`
* `null`
* `undefined`
* `void`
* `any`

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

{% hint style="info" %}
#### Le typage ici, est  utile pour expliquer au compilateur comment vérifier les valeurs dans chacune des variables
{% endhint %}

#### Types complexes

* `array` : `string[]` ou `Array<string>`
* `tuple` : `[string, number]`
* `enum` : énumérations
* `object` : `{ key: value }`

Exemples :

```ts
let names: string[] = ['Alice', 'Bob']
let user: [string, number] = ['Alice', 30]
enum Role { Admin, User, Guest }
type Status = 'draft' | 'published' | 'archived'
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

ou encore, pour un "ref" en VueJs

```typescript
interface User {
id: number
name: string
}
const user = ref<User | null>(null)
```

### Attention au type any / unknown

```typescript
let data: any
data.foo.bar() // TypeScript ne protège plus rien
```

contre :

```typescript
let data: unknown
data.foo // erreur
```

Puis :

```typescript
if (  typeof data === 'object'  && data !== null  && 'name' in data) 
{  
    // on commence à préciser ce que contient data
}
```

`unknown` est justement conçu comme l'alternative sûre à `any` : il oblige à vérifier ou raffiner la donnée avant son utilisation.

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

### Cas concrets avec VueJs/Nuxt

```typescript
<script setup>
const props = defineProps({
  title: String,
  count: Number
})
</script>
```

et arriver à :

```typescript
<script setup lang="ts">
interface Props {
  title: string
  count?: number
}

const props = defineProps<Props>()
</script>
```

Puis :

```typescript
const emit = defineEmits<{
  select: [id: number]
  delete: [id: number]
}>()
```

Et évidemment :

```typescript
const count = ref(0)
```

contre :

```typescript
const user = ref<User | null>(null)
const users = ref<User[]>([])
```

ainsi que :

```typescript
const selected = computed<User | null>(() => {
  return users.value.find(user => user.id === selectedId.value) ?? null
})
```

### Appel API typé

Avant :

```typescript
const response = await fetch('/api/users')
const users = await response.json()
```

Après :

```typescript
interface User {
  id: number
  name: string
  email: string
}

const response = await fetch('/api/users')
const users: User[] = await response.json()
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
