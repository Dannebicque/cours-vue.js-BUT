# Séance 6 : Bonnes pratiques d'architecture & Principes SOLID

## 🎯 Objectifs de la séance

* Comprendre pourquoi les architectures front-end modernes exigent les mêmes standards de rigueur que le back-end.
* Découvrir et appliquer les **5 principes SOLID** adaptés à l'écosystème Vue 3 & Nuxt 4 à travers des **exemples simples et découplés**.
* Savoir découper une application en couches distinctes : **UI (Composants)**, **Logique réactive (Composables)**, **État global (Stores)** et **Transport de données (Services/API)**.
* Identifier et éliminer les anti-patterns récurrents (*God Component*, logique métier dans le template, mutation directe de props).
* **Atelier pratique** : Refactoriser un composant monolithique "spaghetti" vers une architecture propre et SOLID.

---

## 🏛️ 1. Pourquoi SOLID en Front-End ?

À mesure qu'une application Vue / Nuxt grandit, les composants ont tendance à devenir des "fourre-tout" ingérables (*God Components*). Les principes **SOLID**, introduits par Robert C. Martin (*Uncle Bob*), permettent de concevoir des composants **modulaires**, **testables** et **évolutifs**.

```
┌────────────────────────────────────────────────────────────┐
│                    Architecture en couches                 │
├────────────────────────────────────────────────────────────┤
│  1. Vue / UI Layer      : Pages, Layouts, Composants       │
│  2. Reactive Logic      : Composables (useTasks, useAuth)  │
│  3. Global State        : Stores Pinia (Cache, Session)    │
│  4. Data Access Layer   : Services API / Nitro Server API  │
└────────────────────────────────────────────────────────────┘
```

---

## 🧱 2. Les 5 Principes SOLID : Définitions & Exemples Découplés

| Lettre | Principe | Définition adaptée au Front-End |
| :---: | :--- | :--- |
| **S** | **Single Responsibility** | Un composant ou une fonction ne doit avoir qu'**une seule responsabilité** (une seule raison de changer). |
| **O** | **Open / Closed** | Un composant doit être **ouvert à l'extension** (slots, variants) mais **fermé à la modification**. |
| **L** | **Liskov Substitution** | Deux implémentations respectant la même interface doivent être **interchangeables sans casser le code**. |
| **I** | **Interface Segregation** | Un composant ne doit demander **que les propriétés dont il a strictement besoin**. |
| **D** | **Dependency Inversion** | Dépendre d'**abstractions (interfaces, contextes injectés)** plutôt que d'implémentations concrètes rigides. |

---

### 1️⃣ S — Single Responsibility Principle (SRP)

> *"Un composant, un composable ou un module ne doit avoir qu'une seule raison de changer."*

Dans Vue 3, on sépare :
* **Composant UI** : Rendu visuel, accessibilité, émission d'événements.
* **Composable** : Logique métier réactive, manipulation de l'état.
* **Service** : Appels réseau, requêtes HTTP pures.

#### ❌ Anti-pattern (Composant qui fait tout)

Voici une carte météo `WeatherCard.vue` qui effectue elle-même la requête API, convertit la température de Kelvin en Celsius, gère les erreurs et affiche l'interface :

```vue
<!-- ❌ WeatherCard.vue : Viole le SRP -->
<script setup lang="ts">
import { ref, onMounted } from 'vue'

const weather = ref<any>(null)
const loading = ref(false)

onMounted(async () => {
  loading.value = true
  const res = await fetch('https://api.weather.com/v1/paris')
  const data = await res.json()
  // Logique métier de conversion mélangée au composant
  data.tempCelsius = Math.round(data.tempKelvin - 273.15)
  weather.value = data
  loading.value = false
})
</script>

<template>
  <div class="weather-card">
    <p v-if="loading">Chargement...</p>
    <div v-else-if="weather">
      <h3>{{ weather.city }}</h3>
      <p>{{ weather.tempCelsius }} °C</p>
    </div>
  </div>
</template>
```

#### ✅ Clean Code (Séparation des responsabilités)

1. **La logique métier et le fetch sont isolés dans un composable réutilisable :**

```typescript
// composables/useWeather.ts
export function useWeather(city: string) {
  const temperatureCelsius = ref<number | null>(null)
  const cityName = ref<string>(city)
  const isLoading = ref<boolean>(false)

  const fetchWeather = async () => {
    isLoading.value = true
    const data = await $fetch<{ tempKelvin: number }>(`/api/weather/${city}`)
    temperatureCelsius.value = Math.round(data.tempKelvin - 273.15)
    isLoading.value = false
  }

  return { cityName, temperatureCelsius, isLoading, fetchWeather }
}
```

2. **Le composant UI ne s'occupe plus QUE du rendu visuel :**

```vue
<!-- ✅ WeatherCard.vue : Respecte le SRP -->
<script setup lang="ts">
interface Props {
  city: string
  temperature: number | null
  loading?: boolean
}

defineProps<Props>()
</script>

<template>
  <article class="weather-card">
    <span v-if="loading" class="spinner">Chargement...</span>
    <template v-else>
      <h3>{{ city }}</h3>
      <p class="temp">{{ temperature }} °C</p>
    </template>
  </article>
</template>
```

---

### 2️⃣ O — Open / Closed Principle (OCP)

> *"Un composant doit être ouvert à l'extension, mais fermé à la modification."*

Si vous avez besoin d'ajouter une icône, un badge ou un bouton secondaire dans un composant existant, vous ne devriez pas avoir à modifier son code interne avec des chaînes de `v-if="type === 'xxx'"`. Utilisez les **Slots** et la **Composition**.

#### ❌ Anti-pattern (Modification continue du composant source)

Chaque nouveau besoin oblige à ouvrir le fichier et ajouter une nouvelle condition :

```vue
<!-- ❌ BaseAlert.vue : Viole l'OCP -->
<script setup lang="ts">
defineProps<{
  type: 'info' | 'warning' | 'error' | 'success-with-timer' | 'info-with-button'
  message: string
  buttonLabel?: string
}>()
</script>

<template>
  <div :class="['alert', type]">
    <span v-if="type === 'warning'">⚠️</span>
    <span v-if="type === 'error'">❌</span>
    <span v-if="type === 'info'">ℹ️</span>
    <p>{{ message }}</p>
    <button v-if="type === 'info-with-button'">{{ buttonLabel }}</button>
  </div>
</template>
```

#### ✅ Clean Code (Extensibilité par les Slots)

Le composant expose des slots nommés. Il est maintenant prêt pour n'importe quel cas d'usage futur sans jamais avoir besoin d'être retouché :

```vue
<!-- ✅ BaseAlert.vue : Respecte l'OCP -->
<script setup lang="ts">
interface Props {
  variant?: 'info' | 'warning' | 'error' | 'success'
}

withDefaults(defineProps<Props>(), {
  variant: 'info'
})
</script>

<template>
  <div :class="['alert', `alert-${variant}`]">
    <!-- Slot pour icône personnalisable -->
    <slot name="icon">
      <span v-if="variant === 'warning'">⚠️</span>
      <span v-else-if="variant === 'error'">❌</span>
      <span v-else>ℹ️</span>
    </slot>

    <!-- Slot principal pour le message ou contenu riche -->
    <div class="alert-content">
      <slot />
    </div>

    <!-- Slot pour actions contextuelles (bouton, lien, timer...) -->
    <div v-if="$slots.actions" class="alert-actions">
      <slot name="actions" />
    </div>
  </div>
</template>
```

---

### 3️⃣ L — Liskov Substitution Principle (LSP)

> *"Deux objets ou composants implémentant la même interface doivent pouvoir se substituer l'un à l'autre sans altérer le comportement du programme."*

En front-end, cela concerne les **contrats d'interfaces pour les services et adapters de données**.

#### ❌ Anti-pattern (Implémentation qui casse le contrat)

```typescript
// Service standard
class ConsoleLogger {
  log(message: string): void {
    console.log(`[LOG] : ${message}`)
  }
}

// ❌ Viole LSP : Change la signature ou lève une exception inattendue
class RemoteApiLogger {
  // Erreur : impose un objet au lieu d'une string et retourne une promesse bloquante
  async log(payload: { errorId: number; text: string }): Promise<void> {
    await fetch('/api/logs', { method: 'POST', body: JSON.stringify(payload) })
  }
}
```

#### ✅ Clean Code (Contrat d'interface strict et interchangeable)

```typescript
// 1. Définition du contrat commun
export interface LoggerService {
  log(message: string): void
}

// 2. Implémentation A (Développement)
export class DevLogger implements LoggerService {
  log(message: string): void {
    console.info(`%c[DEV LOG] ${message}`, 'color: cyan')
  }
}

// 3. Implémentation B (Production) -> Respecte à 100% la même signature
export class RemoteLogger implements LoggerService {
  log(message: string): void {
    // Envoi asynchrone non-bloquant en arrière-plan
    navigator.sendBeacon('/api/logs', JSON.stringify({ message, timestamp: Date.now() }))
  }
}

// N'importe quel composant peut utiliser DevLogger ou RemoteLogger de manière transparente !
```

---

### 4️⃣ I — Interface Segregation Principle (ISP)

> *"Un composant ne doit pas être forcé de dépendre d'interfaces ou de propriétés dont il n'a pas besoin."*

Ne passez pas un "gros objet" complet à un composant enfant qui n'a besoin que d'un ou deux champs.

#### ❌ Anti-pattern (Passage d'un objet monolithique)

Un composant `ProductPrice.vue` qui ne sert qu'à afficher un prix reçoit l'objet `Product` complet avec sa description, ses 10 photos, son stock et ses avis :

```vue
<!-- ❌ ProductPrice.vue -->
<script setup lang="ts">
// Viole ISP : Couplage fort avec toute l'entité Product
defineProps<{
  product: {
    id: string
    title: string
    description: string
    images: string[]
    stock: number
    price: number
    currency: string
  }
}>()
</script>

<template>
  <span class="price">{{ product.price }} {{ product.currency }}</span>
</template>
```

#### ✅ Clean Code (Props minimales et ciblées)

Le composant ne dépend que de ce dont il a besoin pour fonctionner. Il peut désormais être réutilisé pour afficher le prix d'un abonnement, d'un panier ou d'une facture :

```vue
<!-- ✅ ProductPrice.vue : Respecte l'ISP -->
<script setup lang="ts">
interface Props {
  amount: number
  currency?: string
}

withDefaults(defineProps<Props>(), {
  currency: 'EUR'
})
</script>

<template>
  <span class="price">
    {{ new Intl.NumberFormat('fr-FR', { style: 'currency', currency }).format(amount) }}
  </span>
</template>
```

---

### 5️⃣ D — Dependency Inversion Principle (DIP)

> *"Les modules de haut niveau ne doivent pas dépendre des modules de bas niveau. Tous deux doivent dépendre d'abstractions."*

Dans Vue 3 / Nuxt 4, le DIP se concrétise avec **`provide` / `inject`** et des **`InjectionKey<T>`** typées. Un composant ne crée pas lui-même ses dépendances concrètes : elles lui sont fournies par son environnement parent.

#### ❌ Anti-pattern (Dépendance concrète codée en dur)

Un composant de paiement importe directement une implémentation concrète Stripe :

```vue
<!-- ❌ CheckoutButton.vue -->
<script setup lang="ts">
// Dépendance directe et rigide à Stripe
import { stripePaymentGateway } from '@/services/stripe'

const pay = async () => {
  await stripePaymentGateway.processPayment(49)
}
</script>
```

#### ✅ Clean Code (Injection d'une abstraction via InjectionKey)

1. **Définition de l'abstraction et de la clé typée :**

```typescript
// context/paymentContext.ts
import type { InjectionKey } from 'vue'

export interface PaymentGateway {
  processPayment(amount: number): Promise<boolean>
}

export const PaymentGatewayKey: InjectionKey<PaymentGateway> = Symbol('PaymentGatewayKey')
```

2. **Le composant parent injecte l'implémentation souhaitée (Stripe en Prod, Mock en Test) :**

```vue
<!-- ParentProvider.vue -->
<script setup lang="ts">
import { provide } from 'vue'
import { PaymentGatewayKey } from '~/context/paymentContext'

// En environnement de test, on peut injecter un fakePaymentGateway sans toucher au bouton !
provide(PaymentGatewayKey, {
  async processPayment(amount: number) {
    console.log(`Paiement de ${amount}€ validé`)
    return true
  }
})
</script>
```

3. **Le bouton consomme l'interface abstraite sans savoir qui gère le paiement :**

```vue
<!-- ✅ CheckoutButton.vue : Respecte le DIP -->
<script setup lang="ts">
import { inject } from 'vue'
import { PaymentGatewayKey } from '~/context/paymentContext'

const gateway = inject(PaymentGatewayKey)

if (!gateway) {
  throw new Error('PaymentGateway manquant dans l\'arbre de composants.')
}

const handlePay = async () => {
  await gateway.processPayment(49)
}
</script>

<template>
  <button @click="handlePay" class="btn-pay">Payer 49 €</button>
</template>
```

---

## 🛠️ 3. Atelier Pratique : Refactoring SOLID de `TaskCard`

### Le composant legacy à assainir

Voici un composant legacy que le Lead Tech vous demande de refactoriser selon les principes SOLID :

```vue
<!-- components/TaskCardLegacy.vue -->
<template>
  <div class="card" :style="{ borderColor: task.priority === 'urgent' ? 'red' : 'gray' }">
    <h3>{{ task.title }}</h3>
    <p>{{ task.description }}</p>
    <p>Créé le : {{ new Date(task.createdAt).toLocaleDateString('fr-FR') }}</p>
    <p>Responsable : {{ task.assignee ? task.assignee.name : 'Aucun' }} ({{ task.assignee ? task.assignee.email : '' }})</p>
    <div>
      <button @click="changeStatus('done')">Terminer</button>
      <button @click="remove">Supprimer</button>
    </div>
  </div>
</template>

<script setup>
const props = defineProps(['task'])
const emit = defineEmits(['updated', 'deleted'])

const changeStatus = (newStatus) => {
  props.task.status = newStatus // ❌ Mutation directe de prop ! Anti-pattern majeur
  emit('updated', props.task)
}

const remove = () => {
  if (confirm('Supprimer la tâche ?')) {
    emit('deleted', props.task.id)
  }
}
</script>
```

### 📋 Votre mission de refactoring :
1. **Typage strict** : Remplacer les déclarations non typées par des interfaces TypeScript.
2. **Immuabilité (One-Way Data Flow)** : Ne jamais muter directement `props.task`. Émettre un événement propre vers le parent.
3. **Application du SRP** : Extraire le badge de priorité et l'avatar utilisateur dans des composants dédiés.
4. **Application de l'OCP** : Ajouter un slot pour permettre d'injecter des actions contextuelles sans altérer le composant.

{% hint style="tip" %}
La solution détaillée et commentée de cet atelier est disponible dans la page [Séance 6 (correction)](seance-6-correction.md).
{% endhint %}

---

## 🎯 Bilan & Auto-évaluation

* [ ] Je sais citer les 5 principes de SOLID et les expliquer avec des termes front-end.
* [ ] Je sais pourquoi la mutation directe d'une `prop` est un anti-pattern dans Vue.
* [ ] J'ai compris l'intérêt de `provide` / `inject` avec `InjectionKey<T>` pour inverser les dépendances.
* [ ] Mon code de la Partie 1 est propre, typé et prêt pour l'intégration des APIs et de Pinia en Partie 2.
