# Séance 4 : Composables & Logique Métier Typée

## 🎯 Objectifs de la séance

* Maîtriser la création de **Composables** professionnels, typés et réutilisables dans Nuxt 4 (`app/composables/`).
* Comprendre la distinction fondamentale entre **Composable**, **Store Pinia** et **Service API**.
* Savoir encapsuler l'état réactif (`ref`), les calculs dérivés (`computed`) et les actions sans polluer les composants UI.
* Éviter les pièges récurrents de la Composition API en environnement SSR (fuites de mémoire, pollution d'état inter-requêtes).
* **Atelier Fil rouge DevFlow (Étape 2)** : Concevoir le composable métier `useTasks()` et le moteur de filtrage `useTaskFilters()`.

---

## 🧩 1. Qu'est-ce qu'un Composable ?

Un **Composable** est une fonction JavaScript/TypeScript qui exploite la Composition API de Vue pour encapsuler et réutiliser de la **logique avec état réactif** (*stateful logic*).

### Conventions de nommage et structure
* Le fichier est placé dans `app/composables/` et porte le préfixe `use` (ex: `useTasks.ts`, `useAuth.ts`, `useTheme.ts`).
* Nuxt 4 **auto-importe automatiquement** tous les composables de ce dossier.
* Un composable retourne un objet contenant des `Ref` ou `ComputedRef` et des fonctions d'action.

```
┌────────────────────────────────────────────────────────────┐
│                    Anatomie d'un Composable                │
├────────────────────────────────────────────────────────────┤
│  1. État réactif privé ou partagé (ref, reactive)          │
│  2. Propriétés dérivées / sélecteurs (computed)           │
│  3. Actions / Méthodes manipulant l'état                   │
│  4. Retour d'un contrat typé et sécurisé (readonly)        │
└────────────────────────────────────────────────────────────┘
```

---

## ⚖️ 2. Tableau de Synthèse : Composable vs Store vs Service

| Concept | Rôle principal | Réactivité Vue | Portée (Scope) | Exemple type |
| :--- | :--- | :---: | :--- | :--- |
| **Composable** (`useTasks`) | Factoriser de la logique métier réactive ou des comportements UI. | ✅ Oui (`ref`, `computed`) | Local par défaut, ou partagé par invocation. | `useTaskFilters()`, `usePagination()`, `useClipboard()` |
| **Store Pinia** (`useAuthStore`) | Maintenir un état global unique partagé entre toutes les pages (Source Unique de Vérité). | ✅ Oui (`state`, `getters`, `actions`) | Global (durée de vie de la session SPA). | Authentification, panier d'achat, cache de données global. |
| **Service API** (`taskService`) | Communiquer avec les APIs backend de manière pure et déconnectée de l'UI. | ❌ Non (fonctions pures, Promises) | Stateless (pas d'état conservé). | `apiClient.get('/tasks')`, `authService.login()` |

---

## 🔒 3. Bonnes Pratiques pour des Composables Robustes

### A. Règle d'or : Exposer des `Readonly` pour protéger l'état

Ne laissez pas les composants modifier directement un tableau ou un objet réactif issu d'un composable. Exposez des méthodes d'action explicites.

```typescript
// ✅ BON : Le composant ne peut que lire l'état ou appeler des actions
export function useTasks() {
  const tasks = ref<Task[]>([])

  const addTask = (newTask: Task) => {
    tasks.value.push(newTask)
  }

  return {
    tasks: readonly(tasks), // Protection contre les mutations sauvages
    addTask
  }
}
```

### B. Attention aux variables globales hors de la fonction en SSR

Instanciez l'état au sein de la fonction composable pour éviter les fuites de données entre requêtes sur le serveur.

---

## 🛠️ 4. Atelier Pratique : Fil Rouge "DevFlow" (Étape 2)

### Votre mission chez DevSquad :

1. **Créer le composable `app/composables/useTasks.ts`** :
   * Déclarer un tableau réactif de tâches initialisé avec un jeu de données mockées de 4 tâches réalistes (`Task[]`).
   * Calculer les métriques suivantes avec `computed` : `totalTasks`, `completedTasks`, `pendingTasks`, `urgentTasks`, `completionRate` (pourcentage arrondi), `totalEstimatedHours`.
   * Implémenter les méthodes d'action : `getTaskById(id)`, `addTask(data)`, `updateTaskStatus(id, newStatus)`, `deleteTask(id)`.
   * Retourner l'état protégé avec `readonly()`.

2. **Créer le composable `app/composables/useTaskFilters.ts`** :
   * Accepter la liste réactive des tâches en paramètre `Ref<Task[]>`.
   * Déclarer les `ref` pour `searchQuery`, `selectedStatus`, `selectedPriority`, `selectedAssigneeId`.
   * Calculer `filteredTasks` qui filtre dynamiquement les tâches selon l'ensemble des critères combinés.
   * Fournir une méthode `resetFilters()` pour tout réinitialiser.

3. **Intégrer les composables dans `app/pages/tasks/index.vue`** :
   * Afficher les métriques clés en haut de page.
   * Afficher les filtres interactifs (champ de recherche, menus déroulants).
   * Afficher le nombre de résultats trouvés et la liste des tâches.

{% hint style="tip" %}
La solution détaillée et commentée de cet atelier est disponible dans la page [Séance 4 (correction)](seance-4-correction.md).
{% endhint %}

---

## 🎯 Auto-évaluation & Checklist

* [ ] Je sais expliquer la différence entre un Composable, un Store Pinia et un Service.
* [ ] J'ai encapsulé la logique des tâches dans `app/composables/useTasks.ts` avec des retours `readonly`.
* [ ] Mon composable `useTaskFilters.ts` gère la recherche textuelle et les filtres combinés de manière réactive.
* [ ] Ma page `/tasks` réagit instantanément aux modifications et aux filtres.
