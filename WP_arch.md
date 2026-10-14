```bash
src/
├── domain/                        # Core — pure business rules, zero dependencies
│   ├── models/
│   │   ├── app.model.ts
│   │   └── drink.model.ts
│   └── repositories/
│       ├── drink.repository.ts    # abstract contract (interface only)
│       └── index.ts
│
├── application/                   # Use-cases — orchestrates domain, no framework imports
│   ├── composables/
│   │   ├── useAppMeta.ts
│   │   └── useDrinks.ts
│   └── utils/
│       ├── formatPrice.ts
│       └── resolveEl.ts
│
├── infrastructure/                # Adapters — implements repository contracts
│   ├── data/
│   │   └── drinks.data.ts         # static dataset
│   ├── services/
│   │   └── local-drink.service.ts # concrete implementation of drink.repository
│   └── index.ts
│
├── presentation/                  # UI — Vue components, styles, animations
│   ├── components/
│   │   ├── BottleSvg/
│   │   │   ├── BottleSkeleton.vue
│   │   │   └── BottleSvg.vue
│   │   ├── CartPopup/
│   │   │   ├── CartPopup.vue
│   │   │   └── CartPopup.styles.ts
│   │   ├── CategoryCard/
│   │   │   ├── CategoryCard.vue
│   │   │   └── CategoryCard.styles.ts
│   │   ├── CategoryChips/
│   │   │   ├── CategoryChips.vue
│   │   │   └── CategoryChips.styles.ts
│   │   ├── HeroCarousel/
│   │   │   ├── HeroCarousel.vue
│   │   │   ├── HeroCarousel.styles.ts
│   │   │   └── HeroCarousel.animations.ts
│   │   ├── LogoIcon/
│   │   │   ├── LogoIcon.vue
│   │   │   ├── LogoIcon.styles.ts
│   │   │   └── LogoIcon.animations.ts
│   │   ├── ProductCard/
│   │   │   ├── ProductCard.vue
│   │   │   └── ProductCard.styles.ts
│   │   ├── PromoBanner/
│   │   │   ├── PromoBanner.vue
│   │   │   └── PromoBanner.styles.ts
│   │   └── SectionHeader/
│   │       ├── SectionHeader.vue
│   │       └── SectionHeader.styles.ts
│   │
│   ├── layouts/
│   │   ├── AppFooter/
│   │   │   ├── AppFooter.vue
│   │   │   └── AppFooter.styles.ts
│   │   └── TopBar/
│   │       ├── TopBar.vue
│   │       ├── TopBar.styles.ts
│   │       └── TopBar.animations.ts
│   │
│   ├── pages/
│   │   ├── CartPage/
│   │   │   ├── CartPage.vue
│   │   │   └── CartPage.styles.ts
│   │   ├── CatalogPage/
│   │   │   ├── CatalogPage.vue
│   │   │   └── CatalogPage.styles.ts
│   │   ├── HomePage/
│   │   │   ├── HomePage.vue
│   │   │   ├── HomePage.styles.ts
│   │   │   └── HomePage.animations.ts
│   │   └── ProductPage/
│   │       ├── ProductPage.vue
│   │       └── ProductPage.styles.ts
│   │
│   └── styles/
│       └── theme.config.ts        # global tokens, recipes, theme
│
├── router/
│   └── index.ts                   # Vue Router config
│
├── stores/
│   └── cart.ts                    # Pinia store
│
├── __tests__/
│   └── App.spec.ts
│
├── App.vue
└── main.ts
```

# Onion Architecture (Clean Architecture)

```
┌──────────────────────────────────────────────┐
│              presentation/                   │  Outer ring — Framework
│         components · layouts · pages         │  Vue .vue + PandaCSS .styles.ts
│            router/ · stores/                 │  + GSAP .animations.ts
├──────────────────────────────────────────────┤
│             infrastructure/                  │  Adapters
│       services/  (implements repositories)   │  Concrete data access
│       data/      (static datasets)           │
├──────────────────────────────────────────────┤
│              application/                    │  Use-cases
│       composables/  (useX — ViewModels)      │  Orchestrates domain logic
│       utils/        (pure helpers)           │
├──────────────────────────────────────────────┤
│                domain/                       │  Core — innermost ring
│       models/       (interfaces & types)     │  Zero external dependencies
│       repositories/ (abstract contracts)     │
└──────────────────────────────────────────────┘
```

## Dependency Rule

Dependencies point **inward only**:

- `presentation` → `application` → `domain` ← `infrastructure`
- `infrastructure` implements `domain/repositories` (Dependency Inversion)
- `domain` never imports from any other layer

## Design Patterns

| Pattern                  | Where                                        | Purpose                                            |
| ------------------------ | -------------------------------------------- | -------------------------------------------------- |
| **Repository**           | `domain/repositories/` → `infrastructure/services/` | Abstract data access behind an interface            |
| **Dependency Inversion** | `infrastructure` implements `domain` contracts | Outer layers depend on inner abstractions           |
| **Composable (ViewModel)** | `application/composables/`                  | Encapsulate reactive state + business logic for views |
| **Component Triad**      | `Foo.vue` + `Foo.styles.ts` + `Foo.animations.ts` | Separate structure, style, and motion per component |
| **Store (State Management)** | `stores/cart.ts` (Pinia)                 | Global reactive state shared across pages           |

## Component Convention

Each UI unit lives in its own folder with up to 3 files:

| File               | Required | Responsibility       |
| ------------------ | -------- | -------------------- |
| `Foo.vue`          | yes      | Template + logic     |
| `Foo.styles.ts`    | yes      | PandaCSS style recipes |
| `Foo.animations.ts`| no       | GSAP timelines       |

# Terms

## Composables

| Is like **Custom Hook**

Is a function that uses Vue's Composition API to encapsulate and reuse stateful logic.

Core Traits:

- Stateful: Unlike a standard utility function, it maintains reactive state (ref, reactive).
- Reusable: One logic block can power multiple Views (.tsx files).
- Clean: It moves business logic out of the UI, preventing "fat" components.
- Naming: Always prefixed with use (e.g., useDrink, useToggle).


# Droplet

http://64.23.152.231:8080