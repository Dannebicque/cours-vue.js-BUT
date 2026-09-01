# Séance 9 – Tests front

## 🎯 Objectifs

- Écrire des tests unitaires et E2E.
- Découvrir Cypress.

## 📖 Partie théorique

### Introduction aux tests

Les tests sont essentiels pour garantir la qualité et la fiabilité du code. Ils permettent de détecter les régressions, de vérifier le comportement attendu et d'assurer une meilleure maintenabilité du projet. Dans cette séance, nous allons explorer les différents types de tests et les outils associés.

Les tests viennent s'intégrer dans le cycle de développement, souvent via l'intégration continue (CI), pour automatiser la vérification du code à chaque modification et avant chaque déploiement.

Il existe plusieurs types de tests :

1. Tests unitaires (Unit tests)
2. Tests d’intégration (Integration tests)
3. Tests de bout en bout (E2E - End to End)

### Tests unitaires (Unit tests) avec Vitest

#### Qu'est-ce que Vitest ?

Vitest est un framework de test unitaire ultra-rapide conçu spécifiquement pour les projets Vite. Il est compatible avec l'API de Jest, mais offre des performances bien supérieures grâce à son intégration native avec Vite et son utilisation des ESM (ECMAScript Modules).

**Avantages de Vitest :**

- Exécution ultra-rapide grâce à Vite
- API compatible avec Jest (migration facile)
- Support natif de TypeScript et JSX
- Watch mode intelligent (re-test uniquement ce qui a changé)
- Interface UI pour visualiser les tests
- Support du parallélisme et de l'isolation des tests
- Intégration native avec Vue Test Utils

**Cas d'usage typiques :**

- Tests de composants Vue (rendu, props, events)
- Tests de composables et hooks réutilisables
- Tests de fonctions utilitaires et helpers
- Tests de stores Pinia
- Validation de la logique métier

#### Installation de Vitest

```bash
# Installation de Vitest et des utilitaires pour Vue
npm install -D vitest @vue/test-utils jsdom

# Ou avec pnpm
pnpm add -D vitest @vue/test-utils jsdom

# Pour le coverage (optionnel)
npm install -D @vitest/coverage-v8
```

**Configuration de base (`vitest.config.js`) :**

```javascript
import { defineConfig } from 'vitest/config'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
  test: {
    globals: true, // Utiliser les globales (describe, it, expect)
    environment: 'jsdom', // Simuler un navigateur
    coverage: {
      reporter: ['text', 'html'],
      exclude: ['node_modules/', 'tests/']
    }
  }
})
```

**Ajout de scripts dans `package.json` :**

```json
{
  "scripts": {
    "test": "vitest",
    "test:ui": "vitest --ui",
    "test:run": "vitest run",
    "test:coverage": "vitest run --coverage"
  }
}
```

#### Exemple 1 : Test d'un composant simple

```vue
<!-- src/components/Counter.vue -->
<template>
  <div class="counter">
    <h2>{{ title }}</h2>
    <p>Compteur : {{ count }}</p>
    <button @click="increment">+1</button>
    <button @click="decrement">-1</button>
    <button @click="reset">Reset</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const props = defineProps({
  title: {
    type: String,
    default: 'Mon compteur'
  },
  initialValue: {
    type: Number,
    default: 0
  }
})

const emit = defineEmits(['change'])

const count = ref(props.initialValue)

const increment = () => {
  count.value++
  emit('change', count.value)
}

const decrement = () => {
  count.value--
  emit('change', count.value)
}

const reset = () => {
  count.value = props.initialValue
  emit('change', count.value)
}
</script>
```

```javascript
// src/components/__tests__/Counter.spec.js
import { describe, it, expect, vi } from 'vitest'
import { mount } from '@vue/test-utils'
import Counter from '../Counter.vue'

describe('Counter.vue', () => {
  it('affiche le titre par défaut', () => {
    const wrapper = mount(Counter)
    expect(wrapper.find('h2').text()).toBe('Mon compteur')
  })

  it('affiche le titre personnalisé via props', () => {
    const wrapper = mount(Counter, {
      props: { title: 'Compteur personnalisé' }
    })
    expect(wrapper.find('h2').text()).toBe('Compteur personnalisé')
  })

  it('initialise avec la valeur par défaut', () => {
    const wrapper = mount(Counter)
    expect(wrapper.find('p').text()).toContain('0')
  })

  it('initialise avec une valeur personnalisée', () => {
    const wrapper = mount(Counter, {
      props: { initialValue: 10 }
    })
    expect(wrapper.find('p').text()).toContain('10')
  })

  it('incrémente le compteur au clic', async () => {
    const wrapper = mount(Counter)
    const button = wrapper.findAll('button')[0]
    
    await button.trigger('click')
    expect(wrapper.find('p').text()).toContain('1')
    
    await button.trigger('click')
    expect(wrapper.find('p').text()).toContain('2')
  })

  it('décrémente le compteur au clic', async () => {
    const wrapper = mount(Counter, {
      props: { initialValue: 5 }
    })
    const button = wrapper.findAll('button')[1]
    
    await button.trigger('click')
    expect(wrapper.find('p').text()).toContain('4')
  })

  it('réinitialise le compteur', async () => {
    const wrapper = mount(Counter, {
      props: { initialValue: 10 }
    })
    
    // Incrémenter
    await wrapper.findAll('button')[0].trigger('click')
    expect(wrapper.find('p').text()).toContain('11')
    
    // Reset
    await wrapper.findAll('button')[2].trigger('click')
    expect(wrapper.find('p').text()).toContain('10')
  })

  it('émet un événement "change" avec la nouvelle valeur', async () => {
    const wrapper = mount(Counter)
    
    await wrapper.findAll('button')[0].trigger('click')
    
    expect(wrapper.emitted('change')).toBeTruthy()
    expect(wrapper.emitted('change')[0]).toEqual([1])
  })
})
```

#### Exemple 2 : Test d'un composant avec props et slots

```vue
<!-- src/components/Card.vue -->
<template>
  <div class="card" :class="{ highlighted: isHighlighted }">
    <div class="card-header">
      <slot name="header">
        <h3>{{ title }}</h3>
      </slot>
    </div>
    <div class="card-body">
      <slot></slot>
    </div>
    <div class="card-footer" v-if="$slots.footer">
      <slot name="footer"></slot>
    </div>
  </div>
</template>

<script setup>
defineProps({
  title: String,
  isHighlighted: {
    type: Boolean,
    default: false
  }
})
</script>
```

```javascript
// src/components/__tests__/Card.spec.js
import { describe, it, expect } from 'vitest'
import { mount } from '@vue/test-utils'
import Card from '../Card.vue'

describe('Card.vue', () => {
  it('affiche le titre par défaut', () => {
    const wrapper = mount(Card, {
      props: { title: 'Mon titre' }
    })
    expect(wrapper.find('h3').text()).toBe('Mon titre')
  })

  it('affiche le contenu du slot par défaut', () => {
    const wrapper = mount(Card, {
      slots: {
        default: '<p>Contenu de la carte</p>'
      }
    })
    expect(wrapper.find('.card-body').html()).toContain('Contenu de la carte')
  })

  it('affiche le slot header personnalisé', () => {
    const wrapper = mount(Card, {
      slots: {
        header: '<h2>Titre personnalisé</h2>'
      }
    })
    expect(wrapper.find('.card-header h2').text()).toBe('Titre personnalisé')
  })

  it('affiche le footer si le slot est fourni', () => {
    const wrapper = mount(Card, {
      slots: {
        footer: '<button>Action</button>'
      }
    })
    expect(wrapper.find('.card-footer').exists()).toBe(true)
    expect(wrapper.find('.card-footer button').text()).toBe('Action')
  })

  it('cache le footer si le slot n\'est pas fourni', () => {
    const wrapper = mount(Card)
    expect(wrapper.find('.card-footer').exists()).toBe(false)
  })

  it('applique la classe highlighted', () => {
    const wrapper = mount(Card, {
      props: { isHighlighted: true }
    })
    expect(wrapper.find('.card').classes()).toContain('highlighted')
  })
})
```

#### Exemple 3 : Test d'un composable

```javascript
// src/composables/useCounter.js
import { ref, computed } from 'vue'

export function useCounter(initialValue = 0) {
  const count = ref(initialValue)
  const doubleCount = computed(() => count.value * 2)
  
  const increment = () => {
    count.value++
  }
  
  const decrement = () => {
    count.value--
  }
  
  const reset = () => {
    count.value = initialValue
  }
  
  const isPositive = computed(() => count.value > 0)
  const isNegative = computed(() => count.value < 0)
  
  return {
    count,
    doubleCount,
    increment,
    decrement,
    reset,
    isPositive,
    isNegative
  }
}
```

```javascript
// src/composables/__tests__/useCounter.spec.js
import { describe, it, expect } from 'vitest'
import { useCounter } from '../useCounter'

describe('useCounter', () => {
  it('initialise avec la valeur par défaut', () => {
    const { count } = useCounter()
    expect(count.value).toBe(0)
  })

  it('initialise avec une valeur personnalisée', () => {
    const { count } = useCounter(10)
    expect(count.value).toBe(10)
  })

  it('incrémente correctement', () => {
    const { count, increment } = useCounter(5)
    increment()
    expect(count.value).toBe(6)
    increment()
    expect(count.value).toBe(7)
  })

  it('décrémente correctement', () => {
    const { count, decrement } = useCounter(5)
    decrement()
    expect(count.value).toBe(4)
  })

  it('réinitialise à la valeur initiale', () => {
    const { count, increment, reset } = useCounter(10)
    increment()
    increment()
    expect(count.value).toBe(12)
    reset()
    expect(count.value).toBe(10)
  })

  it('calcule le double du compteur', () => {
    const { count, doubleCount, increment } = useCounter(5)
    expect(doubleCount.value).toBe(10)
    increment()
    expect(doubleCount.value).toBe(12)
  })

  it('détecte si le compteur est positif', () => {
    const { isPositive, increment, decrement } = useCounter(0)
    expect(isPositive.value).toBe(false)
    increment()
    expect(isPositive.value).toBe(true)
    decrement()
    decrement()
    expect(isPositive.value).toBe(false)
  })

  it('détecte si le compteur est négatif', () => {
    const { isNegative, decrement } = useCounter(0)
    expect(isNegative.value).toBe(false)
    decrement()
    expect(isNegative.value).toBe(true)
  })
})
```

#### Exemple 4 : Test de fonctions utilitaires

```javascript
// src/utils/validators.js
export function isValidEmail(email) {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  return emailRegex.test(email)
}

export function isStrongPassword(password) {
  // Au moins 8 caractères, 1 majuscule, 1 minuscule, 1 chiffre
  return password.length >= 8 &&
    /[A-Z]/.test(password) &&
    /[a-z]/.test(password) &&
    /[0-9]/.test(password)
}

export function formatPrice(price, currency = 'EUR') {
  return new Intl.NumberFormat('fr-FR', {
    style: 'currency',
    currency
  }).format(price)
}

export function truncate(text, maxLength = 100) {
  if (text.length <= maxLength) return text
  return text.slice(0, maxLength) + '...'
}
```

```javascript
// src/utils/__tests__/validators.spec.js
import { describe, it, expect } from 'vitest'
import { isValidEmail, isStrongPassword, formatPrice, truncate } from '../validators'

describe('validators', () => {
  describe('isValidEmail', () => {
    it('valide un email correct', () => {
      expect(isValidEmail('test@example.com')).toBe(true)
      expect(isValidEmail('user.name@domain.co.uk')).toBe(true)
    })

    it('rejette un email invalide', () => {
      expect(isValidEmail('invalid')).toBe(false)
      expect(isValidEmail('test@')).toBe(false)
      expect(isValidEmail('@example.com')).toBe(false)
      expect(isValidEmail('test@.com')).toBe(false)
    })
  })

  describe('isStrongPassword', () => {
    it('valide un mot de passe fort', () => {
      expect(isStrongPassword('Password123')).toBe(true)
      expect(isStrongPassword('Str0ngP@ss')).toBe(true)
    })

    it('rejette un mot de passe faible', () => {
      expect(isStrongPassword('short')).toBe(false) // Trop court
      expect(isStrongPassword('alllowercase123')).toBe(false) // Pas de majuscule
      expect(isStrongPassword('ALLUPPERCASE123')).toBe(false) // Pas de minuscule
      expect(isStrongPassword('NoNumbers')).toBe(false) // Pas de chiffre
    })
  })

  describe('formatPrice', () => {
    it('formate un prix en euros', () => {
      expect(formatPrice(10)).toBe('10,00 €')
      expect(formatPrice(1234.56)).toBe('1 234,56 €')
    })

    it('formate un prix dans une autre devise', () => {
      expect(formatPrice(100, 'USD')).toContain('100')
    })
  })

  describe('truncate', () => {
    it('tronque un texte long', () => {
      const longText = 'a'.repeat(150)
      expect(truncate(longText, 100)).toBe('a'.repeat(100) + '...')
    })

    it('ne modifie pas un texte court', () => {
      const shortText = 'Court'
      expect(truncate(shortText, 100)).toBe('Court')
    })

    it('utilise la longueur par défaut', () => {
      const text = 'a'.repeat(150)
      expect(truncate(text).length).toBe(103) // 100 + '...'
    })
  })
})
```

#### Exemple 5 : Test d'un store Pinia

```javascript
// src/stores/cart.js
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const useCartStore = defineStore('cart', () => {
  const items = ref([])
  
  const totalItems = computed(() => items.value.reduce((sum, item) => sum + item.quantity, 0))
  const totalPrice = computed(() => items.value.reduce((sum, item) => sum + (item.price * item.quantity), 0))
  
  const addItem = (product, quantity = 1) => {
    const existingItem = items.value.find(item => item.id === product.id)
    
    if (existingItem) {
      existingItem.quantity += quantity
    } else {
      items.value.push({ ...product, quantity })
    }
  }
  
  const removeItem = (productId) => {
    const index = items.value.findIndex(item => item.id === productId)
    if (index > -1) {
      items.value.splice(index, 1)
    }
  }
  
  const clearCart = () => {
    items.value = []
  }
  
  return {
    items,
    totalItems,
    totalPrice,
    addItem,
    removeItem,
    clearCart
  }
})
```

```javascript
// src/stores/__tests__/cart.spec.js
import { describe, it, expect, beforeEach } from 'vitest'
import { setActivePinia, createPinia } from 'pinia'
import { useCartStore } from '../cart'

describe('Cart Store', () => {
  beforeEach(() => {
    // Créer une instance Pinia fraîche pour chaque test
    setActivePinia(createPinia())
  })

  it('initialise avec un panier vide', () => {
    const cart = useCartStore()
    expect(cart.items).toEqual([])
    expect(cart.totalItems).toBe(0)
    expect(cart.totalPrice).toBe(0)
  })

  it('ajoute un article au panier', () => {
    const cart = useCartStore()
    const product = { id: 1, name: 'Produit 1', price: 10 }
    
    cart.addItem(product)
    
    expect(cart.items).toHaveLength(1)
    expect(cart.items[0]).toMatchObject({ id: 1, name: 'Produit 1', quantity: 1 })
  })

  it('incrémente la quantité d\'un article existant', () => {
    const cart = useCartStore()
    const product = { id: 1, name: 'Produit 1', price: 10 }
    
    cart.addItem(product, 2)
    cart.addItem(product, 3)
    
    expect(cart.items).toHaveLength(1)
    expect(cart.items[0].quantity).toBe(5)
  })

  it('calcule le nombre total d\'articles', () => {
    const cart = useCartStore()
    
    cart.addItem({ id: 1, name: 'Produit 1', price: 10 }, 2)
    cart.addItem({ id: 2, name: 'Produit 2', price: 20 }, 3)
    
    expect(cart.totalItems).toBe(5)
  })

  it('calcule le prix total', () => {
    const cart = useCartStore()
    
    cart.addItem({ id: 1, name: 'Produit 1', price: 10 }, 2) // 20
    cart.addItem({ id: 2, name: 'Produit 2', price: 15 }, 3) // 45
    
    expect(cart.totalPrice).toBe(65)
  })

  it('supprime un article du panier', () => {
    const cart = useCartStore()
    
    cart.addItem({ id: 1, name: 'Produit 1', price: 10 })
    cart.addItem({ id: 2, name: 'Produit 2', price: 20 })
    
    cart.removeItem(1)
    
    expect(cart.items).toHaveLength(1)
    expect(cart.items[0].id).toBe(2)
  })

  it('vide le panier', () => {
    const cart = useCartStore()
    
    cart.addItem({ id: 1, name: 'Produit 1', price: 10 })
    cart.addItem({ id: 2, name: 'Produit 2', price: 20 })
    
    cart.clearCart()
    
    expect(cart.items).toEqual([])
    expect(cart.totalItems).toBe(0)
  })
})
```

#### Mocking et Spy avec Vitest

```javascript
// Exemple de mock d'une fonction
import { describe, it, expect, vi } from 'vitest'

describe('Mocking', () => {
  it('mock une fonction de callback', () => {
    const callback = vi.fn()
    
    callback('test')
    callback('test2')
    
    expect(callback).toHaveBeenCalledTimes(2)
    expect(callback).toHaveBeenCalledWith('test')
    expect(callback).toHaveBeenLastCalledWith('test2')
  })

  it('mock une API externe', async () => {
    // Mock de fetch
    global.fetch = vi.fn(() =>
      Promise.resolve({
        ok: true,
        json: () => Promise.resolve({ data: 'mock data' })
      })
    )

    const response = await fetch('/api/data')
    const data = await response.json()

    expect(data).toEqual({ data: 'mock data' })
    expect(fetch).toHaveBeenCalledWith('/api/data')
  })
})
```

#### Bonnes pratiques Vitest

- Organiser les tests par fonctionnalité (un fichier de test par composant/fonction)
- Utiliser `describe` pour grouper les tests liés
- Nommer les tests de manière descriptive (`it('should do something')`)
- Tester un comportement à la fois par test
- Utiliser `beforeEach` / `afterEach` pour setup/cleanup
- Préférer les sélecteurs par `data-testid` pour les composants
- Tester les cas limites et les erreurs
- Viser une couverture de code > 80% sur la logique critique

### Tests d’intégration (Integration tests) avec Vitest

- Tests des interactions entre plusieurs composants.
- Vérification du comportement global d’une fonctionnalité.

### E2E avec Cypress

#### Qu'est-ce que Cypress ?

Cypress est un framework moderne de tests end-to-end (E2E) pour applications web. Contrairement aux outils traditionnels comme Selenium, Cypress s'exécute dans le même contexte que l'application testée, ce qui le rend plus rapide, plus fiable et plus facile à déboguer.

**Avantages de Cypress :**

- Syntaxe simple et lisible
- Feedback visuel en temps réel avec l'interface graphique
- Automatisation des attentes et des retry automatiques
- Capture d'écran et vidéo des tests en cas d'échec
- Débogage facile dans le navigateur
- Support natif du JavaScript/TypeScript

**Cas d'usage typiques :**
- Parcours utilisateur complets (inscription, login, navigation)
- Tests de formulaires et validation
- Vérification de workflows critiques (paiement, commande)
- Tests de régression avant déploiement

#### Installation de Cypress

```bash
# Installation en dev dependency
npm install -D cypress

# Ou avec pnpm
pnpm add -D cypress

# Initialiser Cypress (crée la structure de dossiers)
npx cypress open
```

Cette commande crée automatiquement :

- `cypress/` : dossier racine des tests
- `cypress/e2e/` : vos fichiers de tests
- `cypress/fixtures/` : données de test (JSON)
- `cypress/support/` : commandes personnalisées et configuration
- `cypress.config.js` : configuration globale

**Configuration de base (`cypress.config.js`) :**

```javascript
import { defineConfig } from 'cypress'

export default defineConfig({
  e2e: {
    baseUrl: 'http://localhost:5173', // URL de votre app en dev
    viewportWidth: 1280,
    viewportHeight: 720,
    video: true, // Enregistrer vidéos des tests
    screenshotOnRunFailure: true
  }
})
```

**Ajout de scripts dans `package.json` :**

```json
{
  "scripts": {
    "cypress:open": "cypress open",
    "cypress:run": "cypress run",
    "test:e2e": "cypress run --browser chrome"
  }
}
```

#### Exemple 1 : Test de la homepage

```javascript
// cypress/e2e/homepage.cy.js
describe('Homepage', () => {
  beforeEach(() => {
    // Visiter la page avant chaque test
    cy.visit('/')
  })

  it('affiche le titre principal', () => {
    cy.get('h1').should('contain', 'Bienvenue')
  })

  it('contient le menu de navigation', () => {
    cy.get('nav').should('be.visible')
    cy.get('nav a').should('have.length.at.least', 3)
  })

  it('vérifie les liens de navigation', () => {
    cy.get('nav').within(() => {
      cy.contains('Accueil').should('have.attr', 'href', '/')
      cy.contains('À propos').should('exist')
      cy.contains('Contact').click()
    })
    
    // Vérifier la navigation
    cy.url().should('include', '/contact')
  })

  it('charge les données dynamiques', () => {
    // Attendre que les éléments soient chargés
    cy.get('[data-testid="task-list"]').should('be.visible')
    cy.get('.task-item').should('have.length.at.least', 1)
  })

  it('est responsive', () => {
    // Test mobile
    cy.viewport('iphone-x')
    cy.get('.mobile-menu-btn').should('be.visible')
    
    // Test desktop
    cy.viewport(1920, 1080)
    cy.get('.desktop-nav').should('be.visible')
  })
})
```

#### Exemple 2 : Test d'un formulaire de login

```javascript
// cypress/e2e/login.cy.js
describe('Formulaire de connexion', () => {
  beforeEach(() => {
    cy.visit('/login')
  })

  it('affiche le formulaire de connexion', () => {
    cy.get('form').should('be.visible')
    cy.get('input[type="email"]').should('exist')
    cy.get('input[type="password"]').should('exist')
    cy.get('button[type="submit"]').should('contain', 'Se connecter')
  })

  it('affiche une erreur si les champs sont vides', () => {
    cy.get('button[type="submit"]').click()
    
    cy.get('.error-message')
      .should('be.visible')
      .and('contain', 'Email requis')
  })

  it('affiche une erreur avec un email invalide', () => {
    cy.get('input[type="email"]').type('email-invalide')
    cy.get('input[type="password"]').type('motdepasse123')
    cy.get('button[type="submit"]').click()
    
    cy.get('.error-message')
      .should('contain', 'Email invalide')
  })

  it('se connecte avec des identifiants valides', () => {
    // Intercepter l'appel API
    cy.intercept('POST', '/api/auth/login', {
      statusCode: 200,
      body: {
        token: 'fake-jwt-token',
        user: { id: 1, name: 'Alice', email: 'alice@example.com' }
      }
    }).as('loginRequest')

    // Remplir le formulaire
    cy.get('input[type="email"]').type('alice@example.com')
    cy.get('input[type="password"]').type('Password123!')
    cy.get('button[type="submit"]').click()

    // Attendre la requête
    cy.wait('@loginRequest')

    // Vérifier la redirection
    cy.url().should('include', '/dashboard')
    cy.get('.welcome-message').should('contain', 'Bienvenue Alice')
  })

  it('gère les erreurs serveur', () => {
    cy.intercept('POST', '/api/auth/login', {
      statusCode: 401,
      body: { message: 'Identifiants incorrects' }
    }).as('loginError')

    cy.get('input[type="email"]').type('wrong@example.com')
    cy.get('input[type="password"]').type('wrongpassword')
    cy.get('button[type="submit"]').click()

    cy.wait('@loginError')
    cy.get('.error-message')
      .should('be.visible')
      .and('contain', 'Identifiants incorrects')
  })

  it('affiche un loader pendant la requête', () => {
    cy.intercept('POST', '/api/auth/login', (req) => {
      // Simuler un délai
      req.reply((res) => {
        res.delay = 1000
        res.send({ statusCode: 200, body: { token: 'fake' } })
      })
    })

    cy.get('input[type="email"]').type('alice@example.com')
    cy.get('input[type="password"]').type('Password123!')
    cy.get('button[type="submit"]').click()

    // Vérifier que le loader apparaît
    cy.get('.loading-spinner').should('be.visible')
    cy.get('button[type="submit"]').should('be.disabled')
  })
})
```

#### Exemple 3 : Test d'ajout de tâche

```javascript
// cypress/e2e/tasks.cy.js
describe('Gestion des tâches', () => {
  beforeEach(() => {
    // Se connecter avant chaque test
    cy.visit('/login')
    cy.get('input[type="email"]').type('alice@example.com')
    cy.get('input[type="password"]').type('Password123!')
    cy.get('button[type="submit"]').click()
    cy.url().should('include', '/dashboard')
  })

  it('ajoute une nouvelle tâche', () => {
    // Ouvrir le formulaire
    cy.get('[data-testid="add-task-btn"]').click()
    
    // Remplir le formulaire
    cy.get('input[name="title"]').type('Nouvelle tâche de test')
    cy.get('textarea[name="description"]').type('Description de la tâche')
    cy.get('select[name="priority"]').select('High')
    
    // Soumettre
    cy.get('button[type="submit"]').click()
    
    // Vérifier l'ajout
    cy.get('.task-list').should('contain', 'Nouvelle tâche de test')
    cy.get('.success-message').should('be.visible')
  })

  it('marque une tâche comme terminée', () => {
    cy.get('.task-item').first().within(() => {
      cy.get('input[type="checkbox"]').check()
    })
    
    cy.get('.task-item').first()
      .should('have.class', 'completed')
  })

  it('supprime une tâche', () => {
    cy.get('.task-item').its('length').then((initialCount) => {
      cy.get('.task-item').first().within(() => {
        cy.get('[data-testid="delete-btn"]').click()
      })
      
      // Confirmer la suppression
      cy.get('.confirm-dialog button.confirm').click()
      
      // Vérifier la suppression
      cy.get('.task-item').should('have.length', initialCount - 1)
    })
  })
})
```

#### Commandes personnalisées Cypress

Vous pouvez créer des commandes réutilisables dans `cypress/support/commands.js` :

```javascript
// cypress/support/commands.js
Cypress.Commands.add('login', (email = 'alice@example.com', password = 'Password123!') => {
  cy.visit('/login')
  cy.get('input[type="email"]').type(email)
  cy.get('input[type="password"]').type(password)
  cy.get('button[type="submit"]').click()
  cy.url().should('include', '/dashboard')
})

Cypress.Commands.add('addTask', (title, description, priority = 'Medium') => {
  cy.get('[data-testid="add-task-btn"]').click()
  cy.get('input[name="title"]').type(title)
  cy.get('textarea[name="description"]').type(description)
  cy.get('select[name="priority"]').select(priority)
  cy.get('button[type="submit"]').click()
})

// Utilisation dans les tests
describe('Workflow complet', () => {
  it('se connecte et ajoute une tâche', () => {
    cy.login() // Commande personnalisée
    cy.addTask('Ma tâche', 'Description', 'High')
    cy.get('.task-list').should('contain', 'Ma tâche')
  })
})
```

#### Bonnes pratiques Cypress

- Utiliser des attributs `data-testid` pour des sélecteurs stables
- Éviter les sélecteurs CSS fragiles (classes, IDs peuvent changer)
- Nettoyer les données entre les tests (`cy.clearLocalStorage()`, `cy.clearCookies()`)
- Utiliser `cy.intercept()` pour mocker les appels API
- Organiser les tests par fonctionnalité (un fichier = une feature)
- Utiliser des commandes personnalisées pour éviter la duplication

### Pyramide des tests

- Structure recommandée :
  - Tests unitaires (base)
  - Tests d’intégration (milieu)
  - Tests E2E (sommet)


## 📝 Travaux pratiques

- Tester un composant simple (`TaskCard`).
- Écrire un test E2E, avec Cypress (login → ajout tâche).

