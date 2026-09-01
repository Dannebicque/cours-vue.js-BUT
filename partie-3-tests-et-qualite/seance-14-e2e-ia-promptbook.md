# Séance 14 : Tests E2E & Journal de Bord IA (`AI_PROMPT_BOOK`)

## 🎯 Objectifs de la séance

* Mettre en place des tests de bout en bout (**E2E**) avec **Playwright** ou **Cypress** pour valider des parcours utilisateurs réels.
* Développer une **posture d'ingénieur critique face aux IA génératives** (Claude, Gemini, ChatGPT, Copilot).
* Structurer le journal de bord d'ingénierie IA : le fichier **`AI_PROMPT_BOOK.md`** obligatoire pour le projet final.
* Évaluer la maintenabilité, la sécurité et l'absence d'hallucinations dans le code produit.

---

## 🎭 1. Tests End-to-End (E2E) avec Playwright

Les tests E2E pilotent un véritable navigateur (Chromium, Firefox, WebKit) pour simuler le comportement d'un utilisateur humain.

```typescript
// tests/e2e/tasks-workflow.spec.ts
import { test, expect } from '@playwright/test'

test.describe('Workflow utilisateur complet sur DevFlow', () => {
  test('Connexion, création d\'une tâche et filtrage', async ({ page }) => {
    // 1. Accès à la page de connexion
    await page.goto('/login')

    // 2. Remplissage du formulaire
    await page.fill('input[type="email"]', 'alex@devsquad.pro')
    await page.fill('input[type="password"]', 'secret')
    await page.click('button[type="submit"]')

    // 3. Vérification de la redirection vers /tasks
    await expect(page).toHaveURL('/tasks')

    // 4. Ouverture de la modal de création
    await page.click('button:has-text("+ Nouvelle Tâche")')
    await page.fill('input[placeholder*="Titre"]', 'Tâche de test E2E Playwright')
    await page.click('button:has-text("Créer la tâche")')

    // 5. Vérification de l'apparition de la carte dans le tableau Kanban
    await expect(page.locator('.kanban-board')).toContainText('Tâche de test E2E Playwright')
  })
})
```

---

## 🤖 2. Utilisation Professionnelle et Critique de l'IA

L'intelligence artificielle est un accélérateur puissant, mais **elle n'est jamais responsable du code livré**. Le développeur reste le seul garant de la sécurité, de la maintenabilité et de l'exactitude architecturale.

### Les 3 Niveaux d'Usage de l'IA :

| Niveau | Pratique | Verdict professionnel |
| :---: | :--- | :---: |
| ❌ **Niveau 0 (Boîte noire)** | Copier-coller aveugle d'un prompt sans comprendre les lignes générées. | **Inacceptable (0/6 en soutenance)** |
| ⚠️ **Niveau 1 (Génération basique)** | Demander du code, le tester superficiellement sans audit de sécurité ni typage strict. | **Risqué et fragile** |
| ✅ **Niveau 2 (Pair Programming Pro)** | Guider l'IA avec un contexte d'architecture précis (SOLID, Types), auditer chaque ligne, corriger les hallucinations et documenter les choix. | **Standard attendu en BUT3** |

---

## 📓 3. Le Journal de Bord IA : `AI_PROMPT_BOOK.md`

Pour le projet final **"Opération Phoenix"**, chaque équipe doit maintenir à la racine de son dépôt GitHub un fichier `AI_PROMPT_BOOK.md`.

### Structure type d'une entrée du journal :

```markdown
# 📖 Journal de Bord IA — Équipe DevSquad #04

## Entrée #12 : Génération du Composable de synchronisation WebSocket

* **Date :** 15/10/2026
* **Auteur :** Sarah Connor
* **Outil IA utilisé :** Claude 3.7 Sonnet

### 🎯 Objectif & Contexte fourni à l'IA
> "Générer un composable Nuxt 4 `useLiveSync` pour écouter des événements SSE et mettre à jour le store Pinia sans fuite de mémoire lors du démontage du composant."

### ⚠️ Hallucination / Problème détecté dans la réponse de l'IA
> L'IA a utilisé un `onUnmounted` classique qui n'était pas SSR-safe et a oublié de typer la charge utile de l'événement avec notre interface `TaskSyncEvent`. De plus, elle a importé `ref` depuis `'vue'` alors que Nuxt l'auto-importe nativement.

### 🛠️ Correction manuelle apportée par l'équipe
> Nous avons encapsulé l'écouteur dans `onMounted` avec une vérification `import.meta.client`, typé l'événement avec `~/types/events` et retiré les imports superflus.

### 💡 Règle apprise / Prompts réutilisables
> Toujours préciser dans les prompts : *"Respecte les conventions Nuxt 4, dossier app/, auto-imports actifs et typage strict sans any."*
```

---

## 🎯 Auto-évaluation & Checklist

* [ ] J'ai compris la différence entre un test unitaire (Vitest) et un test E2E (Playwright).
* [ ] Je sais identifier une hallucination ou un anti-pattern dans du code généré par IA.
* [ ] J'ai créé le template `AI_PROMPT_BOOK.md` prêt pour le projet final.
