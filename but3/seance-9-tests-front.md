# Séance 9 – Tests front

## 🎯 Objectifs
- Écrire des tests unitaires et E2E.
- Découvrir Playwright.

## 📖 Partie théorique

### Unit tests avec Vitest

- Tests unitaires des composants Vue.
- Vérification du rendu et des props.

### E2E avec Playwright

- Tests de bout en bout simulant des scénarios utilisateurs.
- Vérification du fonctionnement global de l’application.

### Pyramide des tests

- Structure recommandée :
  - Tests unitaires (base)
  - Tests d’intégration (milieu)
  - Tests E2E (sommet)

## 💻 Exemples
```js
// Vitest
import { mount } from '@vue/test-utils'
import TaskCard from '../TaskCard.vue'

test('affiche le titre', () => {
  const wrapper = mount(TaskCard, { props: { title: 'Test' } })
  expect(wrapper.text()).toContain('Test')
})
```
```ts
// Playwright
test('login puis poster un message', async ({ page }) => {
  await page.goto('/login')
  await page.fill('#email', 'alice@example.com')
  await page.fill('#password', 'secret')
  await page.click('button:has-text("Se connecter")')
  await expect(page).toHaveURL('/channels')
})
```

## 📝 Travaux pratiques
- Tester un composant simple (`TaskCard`).
- Écrire un test E2E (login → ajout tâche).

