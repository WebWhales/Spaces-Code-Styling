# Vue.js

## Folder Structure

In a Laravel project, we use `resources/js` a directory to serve as the main entry point for js (analogous to a `src` folder in JS standard). Organize it by domain/feature. A clear, scalable structure could look like:

```python
resources/js/
├── Components/           # Reusable presentation components
│   ├── Base/
│   │   ├── Button.vue
│   │   └── Input.vue
│   └── Project/
│   │   ├── ProjectForm.vue
│   │   └── ProjectList.vue
│   └── Employee/
│        ├── ProjectForm.vue
│        └── ProjectList.vue
├── Composables/           # Composition API utilities (useX.js)
│   ├── useAuth.js
│   └── useNotifications.js
├── Enums/
│   ├── AvailabilityStatus.js
├── Layouts/
│   ├── AppLayout.vue
├── Stores/ (xStore.js)
│   ├── authStore.js
│   ├── employeeStore.js
│   └── projectStore.js
├── Pages/  # Page components (We try to keep the file nesting structure like the endpoint)
│   ├── Dashboard.vue
│   ├── Projects/
│   │   ├── Index.vue
│   │   └── Show.vue
│   └── Auth/
│       ├── Login.vue
│       └── Register.vue
├── Plugins/
│   └── axios.js
├── Services/
│   └── projectService.js
└── Utils/                 # Pure utility functions
    └── formatDate.js
```

### Short description

- **Components**: Vue components (UI elements, form controls, widgets).
- **Composables**: Custom Hooks (using the Composition API) for encapsulating and reusing reactive logic, side-effects, and stateful functionality across components.
- **Enums**: Definitions representing fixed sets of named constants.
- **Layout**: High-level layout components (e.g. default or auth layouts) that wrap pages, define common structure (headers, footers, sidebars), and handle layout-specific styling or logic.
- **Pages**: Route-mapped components that serve as entry points for each URL. File structure here mirrors your route tree
    - e.g. `Pages/Debts/Payments/Index.vue` → `GET /debts/{id}/payments`
- **Plugins**: Initialization and configuration files for third-party libraries (e.g. Axios setup, internationalization, global event bus)
- **Services**: Modules responsible for data access: functions or classes that call external APIs, handle HTTP requests/responses, and return parsed data to components or composables.
- **Utils**: Pure utility functions (formatters, validators, helpers) that have no Vue or DOM dependencies and can be used anywhere in the project.

---

## Naming Conventions

- **Folders:** `PascalCase`, e.g. `Project/`.
- **Files:** `camelCase`, e.g. `projectList.js`, `useAuth.js`.
- **Enums:** `PascalCase`, e.g. UserRole`.js`.
    - use a **singular noun** or noun phrase that describes the concept.
- **Vue components:** `PascalCase`, e.g. `ProjectList.vue`.
- **Composables:** prefix `use`, e.g. `useProject.js`
    
    
    - exporting `export function useProject() {}`.
- **Stores:** suffix `Store`, e.g. `projectStore.js`
    
    
    - Pinia exporting `export const useProjectStore = defineStore(...)`.

---

## Scaling Large Folders

Keep each folder scoped to its domain/feature to avoid one giant directory.

### Components/Base Too Big?

One of the solutions can be an [Atomic Design](https://bradfrost.com/blog/post/atomic-web-design/) where we split the folder `js/Components/Base` by abstraction:

 `atoms/`, `molecules/`, `organisms/` under Base.

- **Atoms**
    The smallest—indivisible—components: buttons, inputs, labels, icons.
- **Molecules**
    Simple compositions of atoms that form a distinct piece of UI: a form field (label + input + error message), a button group, etc.
- **Organisms**
    More complex, self-contained sections made of atoms and molecules: a navigation bar, a user profile card, a data table. Typically, these files can be moved to the specific domain/feature folder `Components/<Domain/Feature>`.

```python
resources/js/Components/Base/
├── Atoms/
│   ├── Button.vue
│   └── Input.vue
├── Molecules/
│   ├── FormField.vue
│   └── Modal.vue
└── Organisms/          # Typically, these files can be moved to the specific domain/feature folder Components/Projects, Components/Employees, etc.
    ├── UserCard.vue
    ├── NavigationMenu.vue
    └── DataTable.vue
```

#### Alternative

We can split the code by grouping it into categories. This approach works well in larger projects, as it prevents folders from becoming overcrowded with files.

```python
resources/js/Components/Base/
├── Form/
│   ├── Button.vue
│   └── Input.vue
├── Card/
│   ├── Card.vue
│   └── CardTitle.vue
└── Modal/
    └── Modal.vue
```

### Composables & Stores Growth

Split by domain/feature.

A **hook** and a **composable** play the same role. There are a few differences, which are more semantic.

**Shared composables**: Keep generic composables (e.g., `useDebounce`, `useMediaQuery`) in a `shared/` folder.

```python
resources/js/composables/
├── auth/
│   └── useAuth.js
├── projects/
│   ├── useProject.js
│   └── useProjectSearch.js
├── notifications/
│   └── useNotifications.js
└── shared/
    ├── useDebouce.js
    └── useMediaQuery.js
```

```python
resources/js/stores/
├── auth/
│   └── authStore.js
├── projects/
│   ├── projectStore.js
│   └── projectListStore.js  # if you have multiple stores per domain
└── employees/
    └── employeeStore.js
```

---

## Block Order in .vue Files

1. `<script setup>`
2. `<template>`
3. `<style scoped>`

---

## Composition API Only

Always use the Composition API (`<script setup>`) for new code. This is the Vue 3 standard. Avoid Options API for new components.

### Advantages

- Better Logic Reuse (e.g., allow the use of composables) & Organization
- Enhanced Tree-Shaking & Smaller Bundles. (Remove unused code from the bundles)
- Better Code Readability

---

## Composables

Composables are logic-only reusable functions harnessing the Composition API. Here’s how to structure, and implement them effectively.

Expose only refs, reactive objects, computed, and functions. Encapsulate watchers or lifecycle hooks inside the composable. **Don't use it for template logic** **like JSX, DOM manipulation or CSS**.

```javascript
  const darkMode = ref(false)

  // BAD: directly mutating the DOM/CSS class from inside composable
  watch(darkMode, (val) => {
    document.body.classList.toggle('dark-theme', val)
  }, { immediate: true })

  // BAD: returning a render function (JSX) for template logic
  function ThemeToggle() {
    return (
      <button onclick="{()"> (darkMode.value = !darkMode.value)}>
        Switch to {darkMode.value ? 'Light' : 'Dark'}
      </button>
    )
  }

  // Exposes both the watcher side-effects and the JSX render fn
  return { darkMode, ThemeToggle }
```

```javascript
  const darkMode = ref(false)

  // initialize from localStorage
  onMounted(() => {
    const saved = localStorage.getItem('darkMode')
    if (saved !== null) darkMode.value = saved === 'true'
  })

  // internal side-effect: persist preference
  watchEffect(() => {
    localStorage.setItem('darkMode', String(darkMode.value))
  })

  // only expose reactive state and a toggle fn
  function toggle() {
    darkMode.value = !darkMode.value
  }

  return { darkMode, toggle }
```

### Composables as global state

Composables can be used to cache and reuse logic, and also it can be used as an internal (global) state.

```javascript
// resources/js/Composables/useCountries.js
import { ref } from 'vue'
import axios from '@/plugins/axios'

const countriesCache = ref(null)

export async function useCountries() {
  if (!countriesCache.value) {
    try {
      const { data } = await axios.get('/api/countries')
      countriesCache.value = data
    } catch (err) {
      console.error(err)
    }
  }
  return { countries: countriesCache }
}
```

### When to Use

- **Reusability**: Two or more components share the same reactive logic (e.g. data fetching, form handling, pagination).
- **Complex setup**: A component’s `setup()` is becoming hard to read—dozens of refs, computed values, watchers, and lifecycle hooks all mixed together.
- **Testability**: You need to write unit tests for non-UI logic (sorting algorithms, complex state transitions, API workflows).
- **Decoupling**: Your component is cluttered with pure-logic code, date formatting, currency conversion, complex calculations.
- **Cross-cutting concerns**: Features that touch many parts of the app but share the same underlying mechanism. Media queries, scroll listeners, WebSocket connections, feature flags. So we don't have generic logic inside the component.

### When **Not** to Use

- **One-off logic** State or behavior that truly lives in one component only (e.g. a modal’s open/close state, a simple toggle used nowhere else).
- **Premature abstraction** pulling out a tiny bit of code before you know it’s here to stay (has stabilized) (like extracting a helper you’ve only ever used once or can you don't even know that the code well grow, or don't expect a situation where the helper will be used elsewhere).
- **High-performance hot-paths** Components that render extremely frequently (e.g. dozens per second in an animation loop or large virtual list) can suffer if a composable sets up many watchers or computeds unnecessarily.
- When a dedicated **store** (Pinia/Vuex) already better suits true application-wide state

---

## Provide / Inject

### When to Use

- **Context-style sharing** among a subtree of components (e.g. theme, modal API, i18n)
- You need exactly **one shared instance** (singleton) for all descendants
- Building a **plugin** or library that other components will `inject` without prop-drilling

### When **Not** to Use

- Consumers (like a components) live **outside** the provider’s render tree (Can consider using composable). The provided variables can only be used top to bottom, just child components.
- You need **multiple independent instances** (use normal composable or store)
- Logic/state is truly **application-wide** (Pinia/Vuex is better)

---

## Stores (Pinia)

Pinia stores manage shared state across components. Use these patterns to keep stores scalable and maintainable.

## When to Use

- **Shared, Reactive State Across Many Components**
    When multiple, unrelated parts of your app need to read or update the same state (e.g. user session, shopping cart, feature flags).
- **Complex State Logic**
    When your app’s state has non-trivial getters, actions (async flows), or needs plugins (e.g. persistence, devtools).
- **Devtools & Time-Travel Debugging**
    Pinia integrates with Vue Devtools out of the box for state inspection and time-travel.

## When Not to Use

- **Trivial Local State**
    Component-level state (form inputs, local toggles) stays in `ref`/`reactive` or a composable.
- **One-Off Shared State**
    If only two closely related components share a bit of state, consider a simple composable.
- **Performance-Critical Hot Paths**
    If reading or writing store state triggers too much reactivity overhead in very high-render components, keep that state local or use fine-grained composables.

