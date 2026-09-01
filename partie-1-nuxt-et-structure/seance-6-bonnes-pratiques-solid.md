# Séance 6 : Bonnes pratiques d'architecture & Principes SOLID

## 🎯 Objectifs de la séance

* Comprendre pourquoi les architectures front-end modernes exigent les mêmes standards de rigueur que le back-end.
* Découvrir et appliquer les **5 principes SOLID** adaptés à l'écosystème Vue 3 & Nuxt 4.
* Savoir découper une application en couches distinctes : **UI (Composants)**, **Logique réactive (Composables)**, **État global (Stores)** et **Transport de données (Services/API)**.
* Identifier et éliminer les anti-patterns récurrents (*God Component*, logique métier dans le template, mutation directe de props).
* **Atelier pratique** : Refactoriser un composant monolithique "spaghetti" vers une architecture propre et SOLID.

---

## 🏛️ 1. Pourquoi SOLID en Front-End ?

Maintenant que vous maîtrisez les bases de Nuxt 4, le typage strict, le routage et les composables, il est crucial de structurer votre code pour qu'il reste maintenable à grande échelle.

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

## 🧱 2. Les 5 Principes SOLID en Front-End

| Lettre | Principe | Définition adaptée au Front-End |
| :---: | :--- | :--- |
| **S** | **Single Responsibility** | Un composant ou un composable ne doit avoir qu'**une seule raison de changer**. |
| **O** | **Open / Closed** | Un composant doit être **ouvert à l'extension** (slots, variants) mais **fermé à la modification**. |
| **L** | **Liskov Substitution** | Les sous-composants ou adapters interchangeables doivent respecter le même contrat d'interface. |
| **I** | **Interface Segregation** | Un composant ne doit pas être forcé de recevoir des props dont il n'a pas l'utilité. |
| **D** | **Dependency Inversion** | Dépendre d'abstractions (types, composables injectés) plutôt que d'implémentations concrètes rigides. |

---

### 1️⃣ S — Single Responsibility Principle (SRP)

Dans Vue 3, séparez rigoureusement :
1. **La présentation visuelle (Composants UI)** : Afficher du HTML, écouter les interactions, émettre des événements.
2. **La logique métier et l'état réactif (Composables)** : Calculs, règles de validation, filtres, manipulation de listes.
3. **La communication réseau (Services API)** : Envoi de requêtes HTTP, transformation des payloads.

---

### 2️⃣ O — Open / Closed Principle (OCP)

Un composant doit être extensible par son consommateur (via des **slots**, scoped slots ou props de variants) sans qu'il soit nécessaire d'en modifier le code source interne à chaque nouveau besoin.

---

### 3️⃣ L — Liskov Substitution Principle (LSP)

Les implémentations concrètes d'une fonctionnalité (ex: stockage LocalStorage vs API serveur) doivent respecter la même interface afin d'être interchangeables sans impacter le reste du code.

---

### 4️⃣ I — Interface Segregation Principle (ISP)

Ne passez jamais un "gros objet" complet (ex: tout l'objet `User` avec ses 20 propriétés) à un composant enfant qui n'a besoin que du nom et de l'avatar. Utilisez des types ciblés (`Pick<User, 'name' | 'avatar'>`).

---

### 5️⃣ D — Dependency Inversion Principle (DIP)

Dans Vue 3 et Nuxt 4, le DIP se met en œuvre grâce au mécanisme de **`provide` / `inject`** combiné avec des **`InjectionKey<T>`** typées.

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
