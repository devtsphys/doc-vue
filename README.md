# Vue.js Reference Card

## Table of Contents

- [Essentials](#essentials)
- [Components](#components)
- [Reactivity](#reactivity)
- [Directives](#directives)
- [Templates & Rendering](#templates--rendering)
- [Lifecycle Hooks](#lifecycle-hooks)
- [Composition API](#composition-api)
- [Router](#router)
- [State Management](#state-management)
- [Best Practices](#best-practices)

## Essentials

### Vue Instance / App Creation

```js
// Vue 3
import { createApp } from 'vue'
import App from './App.vue'

const app = createApp(App)
app.mount('#app')

// Vue 2
import Vue from 'vue'
import App from './App.vue'

new Vue({
  render: h => h(App)
}).$mount('#app')
```

### Component Basic Structure

```vue
<template>
  <div>
    <h1>{{ title }}</h1>
    <p>{{ message }}</p>
  </div>
</template>

<script>
export default {
  name: 'MyComponent',
  props: {
    title: String
  },
  data() {
    return {
      message: 'Hello Vue!'
    }
  }
}
</script>

<style scoped>
h1 {
  color: #42b983;
}
</style>
```

### Component Registration

```js
// Global registration (Vue 3)
import MyComponent from './MyComponent.vue'
app.component('my-component', MyComponent)

// Global registration (Vue 2)
import MyComponent from './MyComponent.vue'
Vue.component('my-component', MyComponent)

// Local registration
import MyComponent from './MyComponent.vue'
export default {
  components: {
    MyComponent
  }
}
```

## Components

### Props

```vue
<script>
export default {
  props: {
    // Basic type check
    title: String,
    
    // Multiple types
    id: [String, Number],
    
    // Required prop
    required: {
      type: String,
      required: true
    },
    
    // Default value
    message: {
      type: String,
      default: 'Hello'
    },
    
    // Object/Array default
    items: {
      type: Array,
      default: () => []
    },
    
    // Custom validator
    status: {
      validator: (value) => {
        return ['success', 'warning', 'error'].includes(value)
      }
    }
  }
}
</script>
```

### Component Events

```vue
<template>
  <button @click="emitEvent">Click me</button>
</template>

<script>
export default {
  // Vue 3
  emits: ['update', 'delete'],
  
  methods: {
    emitEvent() {
      this.$emit('update', { id: 1, status: 'updated' })
    }
  }
}
</script>

<!-- Parent component -->
<template>
  <child-component @update="handleUpdate"></child-component>
</template>
```

### Slots

```vue
<!-- Base component with slots -->
<template>
  <div class="container">
    <header>
      <slot name="header">Default header content</slot>
    </header>
    
    <main>
      <slot>Default content</slot>
    </main>
    
    <footer>
      <slot name="footer">Default footer</slot>
    </footer>
  </div>
</template>

<!-- Usage -->
<template>
  <base-layout>
    <template v-slot:header>
      <h1>Page Title</h1>
    </template>
    
    <p>Main content</p>
    
    <template #footer>
      <p>Footer content</p>
    </template>
  </base-layout>
</template>
```

### Scoped Slots

```vue
<!-- List component with scoped slot -->
<template>
  <ul>
    <li v-for="(item, index) in items" :key="index">
      <slot :item="item" :index="index"></slot>
    </li>
  </ul>
</template>

<!-- Usage -->
<template>
  <data-list :items="fruits">
    <template v-slot:default="slotProps">
      <span>{{ slotProps.index }}. {{ slotProps.item.name }}</span>
    </template>
  </data-list>
</template>

<!-- Shorthand -->
<template>
  <data-list :items="fruits">
    <template #default="{ item, index }">
      <span>{{ index }}. {{ item.name }}</span>
    </template>
  </data-list>
</template>
```

## Reactivity

### Options API Data & Methods

```js
export default {
  data() {
    return {
      count: 0,
      user: {
        name: 'John',
        isActive: true
      },
      items: ['Apple', 'Banana', 'Orange']
    }
  },
  methods: {
    increment() {
      this.count++
    },
    updateUser(name) {
      this.user.name = name
    },
    addItem(item) {
      this.items.push(item)
    }
  }
}
```

### Computed Properties

```js
export default {
  data() {
    return {
      firstName: 'John',
      lastName: 'Doe',
      items: [1, 2, 3, 4, 5]
    }
  },
  computed: {
    // Getter only
    fullName() {
      return `${this.firstName} ${this.lastName}`
    },
    
    // Getter and setter
    fullNameComputed: {
      get() {
        return `${this.firstName} ${this.lastName}`
      },
      set(newValue) {
        const names = newValue.split(' ')
        this.firstName = names[0]
        this.lastName = names[1] || ''
      }
    },
    
    // Filtering computed property
    evenItems() {
      return this.items.filter(item => item % 2 === 0)
    }
  }
}
```

### Watchers

```js
export default {
  data() {
    return {
      question: '',
      answer: '',
      user: {
        name: '',
        email: ''
      }
    }
  },
  watch: {
    // Simple watcher
    question(newQuestion, oldQuestion) {
      if (newQuestion.includes('?')) {
        this.getAnswer()
      }
    },
    
    // Deep watcher
    user: {
      handler(newValue, oldValue) {
        console.log('User object changed')
      },
      deep: true
    },
    
    // Immediate watcher (runs immediately)
    'user.name': {
      handler(newValue) {
        console.log(`Name is now ${newValue}`)
      },
      immediate: true
    }
  },
  methods: {
    getAnswer() {
      // Fetch answer based on question
    }
  }
}
```

## Directives

### Core Directives

```vue
<template>
  <!-- Conditional rendering -->
  <div v-if="showContent">Visible when showContent is true</div>
  <div v-else-if="showAlt">Alternative content</div>
  <div v-else>Fallback content</div>
  
  <!-- Displays but keeps in DOM (toggle visibility) -->
  <div v-show="isVisible">Toggles display CSS property</div>
  
  <!-- List rendering -->
  <ul>
    <li v-for="(item, index) in items" :key="item.id">
      {{ index }}: {{ item.name }}
    </li>
  </ul>
  
  <!-- Event handling -->
  <button v-on:click="handleClick">Click me</button>
  <button @click="handleClick($event, 'param')">With params</button>
  
  <!-- Two-way binding -->
  <input v-model="message">
  <textarea v-model="content"></textarea>
  <input type="checkbox" v-model="isChecked">
  
  <!-- Modifiers -->
  <form @submit.prevent="onSubmit">
    <input @keyup.enter="submit">
    <button @click.once="handleClick">Click once only</button>
  </form>
  
  <!-- Binding attributes and classes -->
  <div v-bind:class="{ active: isActive, 'text-danger': hasError }"></div>
  <div :class="[baseClass, isActive ? 'active' : '']"></div>
  <div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
</template>
```

### Custom Directives

```js
// Global directive (Vue 3)
app.directive('focus', {
  mounted(el) {
    el.focus()
  }
})

// Global directive (Vue 2)
Vue.directive('focus', {
  inserted(el) {
    el.focus()
  }
})

// Local directive
export default {
  directives: {
    highlight: {
      mounted(el, binding) {
        el.style.backgroundColor = binding.value || 'yellow'
      }
    }
  }
}

// Usage
<input v-focus>
<p v-highlight="'orange'">Highlighted text</p>
```

## Templates & Rendering

### Template Syntax

```vue
<template>
  <!-- Text interpolation -->
  <span>{{ message }}</span>
  
  <!-- Raw HTML (careful with XSS) -->
  <div v-html="rawHtml"></div>
  
  <!-- Attribute binding -->
  <img :src="imageSrc" :alt="imageAlt">
  
  <!-- JavaScript expressions -->
  <div>{{ message.split('').reverse().join('') }}</div>
  <div>{{ isActive ? 'Active' : 'Inactive' }}</div>
  
  <!-- One-time binding with v-once -->
  <span v-once>{{ neverChanges }}</span>
</template>
```

### Render Functions

```js
// Vue 3
import { h } from 'vue'

export default {
  render() {
    return h('div', 
      { class: 'container' },
      [
        h('h1', 'Title'),
        h('p', this.message)
      ]
    )
  }
}

// with JSX
export default {
  render() {
    return (
      <div class="container">
        <h1>Title</h1>
        <p>{this.message}</p>
      </div>
    )
  }
}
```

## Lifecycle Hooks

### Component Lifecycle

```js
export default {
  beforeCreate() {
    // Called before instance is created
  },
  created() {
    // Instance created, reactive data established
    // Good for API calls
  },
  beforeMount() {
    // Right before DOM mounting occurs
  },
  mounted() {
    // Component mounted to DOM
    // Access to this.$el
  },
  beforeUpdate() {
    // Data changed, before DOM re-render
  },
  updated() {
    // Re-render completed
  },
  beforeUnmount() {  // Vue 3 (beforeDestroy in Vue 2)
    // Right before teardown
  },
  unmounted() {  // Vue 3 (destroyed in Vue 2)
    // Component destroyed
    // Good for cleanup
  },
  
  // Error handling
  errorCaptured(err, instance, info) {
    // Handle component errors
    return false // Prevent propagation
  },
  
  // Vue 3 only
  activated() {
    // Used with <keep-alive>
    // Component activated
  },
  deactivated() {
    // Used with <keep-alive>
    // Component deactivated
  }
}
```

## Composition API

### Setup Function

```vue
<script>
import { ref, reactive, computed, watch, onMounted } from 'vue'

export default {
  props: {
    title: String
  },
  setup(props, context) {
    // Reactive state
    const count = ref(0)
    const user = reactive({
      name: 'John',
      email: 'john@example.com'
    })
    
    // Computed properties
    const doubleCount = computed(() => count.value * 2)
    
    // Methods
    function increment() {
      count.value++
    }
    
    // Watchers
    watch(count, (newValue, oldValue) => {
      console.log(`Count changed from ${oldValue} to ${newValue}`)
    })
    
    // Lifecycle hooks
    onMounted(() => {
      console.log('Component mounted')
    })
    
    // Expose to template
    return {
      count,
      user,
      doubleCount,
      increment
    }
    
    // Working with context
    // context.attrs
    // context.slots
    // context.emit
  }
}
</script>
```

### <script setup> (Vue 3)

```vue
<script setup>
import { ref, computed, watch, onMounted } from 'vue'
import { useRoute } from 'vue-router'
import ChildComponent from './ChildComponent.vue'

// Props definition
const props = defineProps({
  title: String,
  items: Array
})

// Emits definition
const emit = defineEmits(['update', 'delete'])

// Expose to parent via template refs
const count = ref(0)
const doubleCount = computed(() => count.value * 2)

function increment() {
  count.value++
  emit('update', count.value)
}

// Automatically available in template
const route = useRoute()

// Lifecycle hooks
onMounted(() => {
  console.log('Component mounted')
})

// Define what to expose to parent components using template refs
defineExpose({
  count,
  increment
})
</script>
```

### Composables / Reusable Logic

```js
// useCounter.js
import { ref, computed } from 'vue'

export function useCounter(initialCount = 0) {
  const count = ref(initialCount)
  const doubleCount = computed(() => count.value * 2)
  
  function increment() {
    count.value++
  }
  
  function decrement() {
    count.value--
  }
  
  function reset() {
    count.value = initialCount
  }
  
  return {
    count,
    doubleCount,
    increment,
    decrement,
    reset
  }
}

// Usage in component
import { useCounter } from '@/composables/useCounter'

export default {
  setup() {
    const { count, increment } = useCounter(10)
    return { count, increment }
  }
}
```

## Router

### Vue Router Setup

```js
// router/index.js (Vue 3)
import { createRouter, createWebHistory } from 'vue-router'
import Home from '@/views/Home.vue'
import About from '@/views/About.vue'

const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home
  },
  {
    path: '/about',
    name: 'About',
    component: About
  },
  {
    path: '/user/:id',
    name: 'User',
    component: () => import('@/views/User.vue'),  // Lazy loaded
    props: true
  },
  {
    path: '/admin',
    name: 'Admin',
    component: () => import('@/views/Admin.vue'),
    meta: { requiresAuth: true },
    children: [
      {
        path: 'dashboard',
        component: () => import('@/views/admin/Dashboard.vue')
      }
    ]
  },
  {
    path: '/:pathMatch(.*)*',
    name: 'NotFound',
    component: () => import('@/views/NotFound.vue')
  }
]

const router = createRouter({
  history: createWebHistory(),
  routes
})

// Global navigation guard
router.beforeEach((to, from, next) => {
  if (to.meta.requiresAuth && !isAuthenticated()) {
    next('/login')
  } else {
    next()
  }
})

export default router

// main.js
import router from './router'
app.use(router)
```

### Router Usage in Components

```vue
<template>
  <div>
    <!-- Navigation with router-link -->
    <router-link to="/">Home</router-link>
    <router-link :to="{ name: 'User', params: { id: 123 }}">User</router-link>
    <router-link :to="{ path: '/about', query: { plan: 'premium' }}">About</router-link>
    
    <!-- Active link classes (automatically applied) -->
    <router-link to="/blog" active-class="active" exact-active-class="exact-active">Blog</router-link>
    
    <!-- Render the matched component -->
    <router-view></router-view>
    
    <!-- Named views -->
    <router-view name="sidebar"></router-view>
    <router-view name="main"></router-view>
  </div>
</template>

<script>
export default {
  methods: {
    // Programmatic navigation
    goToHome() {
      this.$router.push('/')
    },
    goToUser(id) {
      this.$router.push({ name: 'User', params: { id }})
    },
    goBack() {
      this.$router.go(-1)
    }
  },
  computed: {
    // Access route parameters
    userId() {
      return this.$route.params.id
    },
    // Access query parameters
    searchQuery() {
      return this.$route.query.q
    }
  }
}
</script>
```

## State Management

### Vuex Store Setup

```js
// store/index.js
import { createStore } from 'vuex'

// Module example
const products = {
  namespaced: true,
  state: () => ({
    all: []
  }),
  mutations: {
    setProducts(state, products) {
      state.all = products
    }
  },
  actions: {
    async fetchProducts({ commit }) {
      const products = await fetchProductsApi()
      commit('setProducts', products)
    }
  },
  getters: {
    availableProducts: state => state.all.filter(p => p.inventory > 0)
  }
}

export default createStore({
  state: {
    count: 0,
    user: null
  },
  mutations: {
    increment(state) {
      state.count++
    },
    setUser(state, user) {
      state.user = user
    }
  },
  actions: {
    incrementAsync({ commit }) {
      setTimeout(() => {
        commit('increment')
      }, 1000)
    },
    async login({ commit }, credentials) {
      const user = await authAPI.login(credentials)
      commit('setUser', user)
      return user
    }
  },
  getters: {
    isLoggedIn: state => !!state.user,
    doubleCount: state => state.count * 2
  },
  modules: {
    products
  }
})

// main.js
import store from './store'
app.use(store)
```

### Vuex in Components

```vue
<template>
  <div>
    <p>Count: {{ count }}</p>
    <p>Double count: {{ doubleCount }}</p>
    <button @click="increment">Increment</button>
    <button @click="incrementAsync">Increment Async</button>
    
    <div v-if="isLoggedIn">
      Welcome, {{ user.name }}!
      <button @click="logout">Logout</button>
    </div>
    <div v-else>
      <button @click="login">Login</button>
    </div>
    
    <!-- Module usage -->
    <ul>
      <li v-for="product in availableProducts" :key="product.id">
        {{ product.name }}
      </li>
    </ul>
    <button @click="fetchProducts">Load Products</button>
  </div>
</template>

<script>
import { mapState, mapGetters, mapActions, mapMutations } from 'vuex'

export default {
  computed: {
    // Map state properties
    ...mapState(['count', 'user']),
    
    // Map getters
    ...mapGetters(['doubleCount', 'isLoggedIn']),
    
    // Map module getters
    ...mapGetters('products', ['availableProducts'])
  },
  methods: {
    // Map mutations
    ...mapMutations(['increment']),
    
    // Map actions
    ...mapActions(['incrementAsync', 'login', 'logout']),
    
    // Map module actions
    ...mapActions('products', ['fetchProducts'])
  }
}
</script>
```

### Pinia (Vue 3 State Management)

```js
// stores/counter.js
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', {
  // Options API style
  state: () => ({
    count: 0,
    lastUpdated: null
  }),
  getters: {
    doubleCount: (state) => state.count * 2
  },
  actions: {
    increment() {
      this.count++
      this.lastUpdated = new Date()
    },
    async fetchRemoteCount() {
      const response = await fetch('/api/count')
      this.count = await response.json()
    }
  }
})

// Alternative Composition API style
export const useUserStore = defineStore('user', () => {
  // state
  const user = ref(null)
  const isLoading = ref(false)
  
  // getters
  const isLoggedIn = computed(() => !!user.value)
  
  // actions
  async function login(credentials) {
    isLoading.value = true
    try {
      user.value = await authAPI.login(credentials)
    } finally {
      isLoading.value = false
    }
  }
  
  function logout() {
    user.value = null
  }
  
  return { user, isLoading, isLoggedIn, login, logout }
})

// Using the store in components
import { useCounterStore } from '@/stores/counter'

export default {
  setup() {
    const counterStore = useCounterStore()
    
    return { 
      // Access state and getters
      count: computed(() => counterStore.count),
      doubleCount: computed(() => counterStore.doubleCount),
      
      // Actions
      increment: () => counterStore.increment()
    }
  }
}
```

## Best Practices

### Performance Optimization

```vue
<!-- Component Lazy Loading -->
<script>
const LazyComponent = () => import('./LazyComponent.vue')
export default {
  components: {
    LazyComponent
  }
}
</script>

<!-- Keep-alive for Caching Components -->
<template>
  <keep-alive>
    <component :is="currentView"></component>
  </keep-alive>
</template>

<!-- v-once for Static Content -->
<template>
  <div v-once>
    <h1>{{ staticTitle }}</h1>
    <expensive-component></expensive-component>
  </div>
</template>

<!-- v-memo for Memoization (Vue 3) -->
<template>
  <div v-for="item in list" :key="item.id" v-memo="[item.id === selected]">
    <!-- Only re-renders when selection changes -->
    {{ item.name }}
  </div>
</template>
```

### Component Design Patterns

```js
// Transparent Wrapper Component
export default {
  name: 'CustomButton',
  inheritAttrs: false,
  props: {
    variant: {
      type: String,
      default: 'primary'
    }
  },
  render() {
    return h('button', {
      ...this.$attrs,
      class: [`btn-${this.variant}`]
    }, this.$slots.default())
  }
}

// Renderless Component (Logic Provider)
export default {
  name: 'FetchData',
  props: {
    url: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      data: null,
      loading: false,
      error: null
    }
  },
  created() {
    this.fetchData()
  },
  methods: {
    async fetchData() {
      this.loading = true
      this.error = null
      try {
        const response = await fetch(this.url)
        this.data = await response.json()
      } catch (error) {
        this.error = error
      } finally {
        this.loading = false
      }
    }
  },
  render() {
    return this.$slots.default({
      data: this.data,
      loading: this.loading,
      error: this.error,
      refetch: this.fetchData
    })
  }
}

// Usage
<template>
  <fetch-data url="/api/users">
    <template v-slot="{ data, loading, error, refetch }">
      <div v-if="loading">Loading...</div>
      <div v-else-if="error">Error: {{ error }}</div>
      <div v-else>
        <ul>
          <li v-for="user in data" :key="user.id">{{ user.name }}</li>
        </ul>
        <button @click="refetch">Refresh</button>
      </div>
    </template>
  </fetch-data>
</template>
```

### Testing

```js
// Component Unit Testing with Vue Test Utils
import { mount } from '@vue/test-utils'
import Counter from '@/components/Counter.vue'

describe('Counter.vue', () => {
  it('increments counter when button is clicked', async () => {
    const wrapper = mount(Counter, {
      props: {
        initialCount: 0
      }
    })
    
    // Check initial state
    expect(wrapper.text()).toContain('Count: 0')
    
    // Trigger button click
    await wrapper.find('button').trigger('click')
    
    // Check updated state
    expect(wrapper.text()).toContain('Count: 1')
    
    // Check emitted events
    expect(wrapper.emitted('update')).toBeTruthy()
    expect(wrapper.emitted('update')[0]).toEqual([1])
  })
})
```

### Security Best Practices

```vue
<!-- NEVER use v-html with untrusted content -->
<div v-html="sanitizedHtml"></div>

<script>
import DOMPurify from 'dompurify'

export default {
  data() {
    return {
      userInput: '<script>alert("XSS")</script>'
    }
  },
  computed: {
    sanitizedHtml() {
      // Always sanitize HTML before using v-html
      return DOMPurify.sanitize(this.userInput)
    }
  }
}
</script>

<!-- Prevent prototype pollution -->
<script>
export default {
  created() {
    // Deep freeze objects from external sources
    const freeze = (obj) => {
      Object.freeze(obj)
      Object.values(obj).forEach(val => {
        if (val && typeof val === 'object') {
          freeze(val)
        }
      })
      return obj
    }
    
    this.safeData = freeze(this.externalData)
  }
}
</script>
```

### General Best Practices

1. **Naming Conventions**
- Components: PascalCase (`UserProfile.vue`)
- Props: camelCase
- Events: kebab-case (`@user-updated`)
- Files: PascalCase or kebab-case (`UserProfile.vue` or `user-profile.vue`)
1. **Component Structure**
- Keep components small and focused
- Group related functionality
- Use mixins or composables for shared code
1. **Performance Tips**
- Optimize v-for with :key
- Use functional components for stateless rendering
- Avoid deeply nested reactive objects
- Consider computed properties for expensive operations
1. **Vue 3 Migration Tips**
- Use Composition API for complex components
- Migrate to `<script setup>` for cleaner code
- Replace Vuex with Pinia for state management
- Use Vue 3’s improved reactivity system