---
name: Vue
description: >
  Vue 3 Composition API with script setup syntax: reactive, composable components for modern web UIs.
  Trigger: Building component-based UIs with Vue 3 using Composition API and script setup.
license: MIT
metadata:
  author: Henry Carmenate
  version: "1.0"
  applies-to:
    - claude-code
    - cursor
    - codex
---

## When to Use
Use this skill when:
- Building reactive, component-based UIs with Vue 3
- Using Composition API for reusable, testable logic
- Creating single-file components with <script setup>
- Managing state with reactive() and ref()
- Building composables to share logic across components

## Critical Patterns

### Pattern 1: Script Setup with Reactive State
Vue 3's <script setup> syntax eliminates boilerplate.

```vue
<template>
  <div class="user-profile">
    <h1>{{ user.name }}</h1>
    <p>{{ user.email }}</p>
    <button @click="updateUser">Update Profile</button>
  </div>
</template>

<script setup>
import { ref, reactive, computed } from 'vue';

const user = reactive({
  name: 'John Doe',
  email: 'john@example.com',
  isActive: true
});

const isEditing = ref(false);

const displayName = computed(() => {
  return user.isActive ? user.name : `${user.name} (inactive)`;
});

const updateUser = async () => {
  try {
    const response = await fetch(`/api/users/${user.id}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(user)
    });
    if (!response.ok) throw new Error('Update failed');
    const updated = await response.json();
    Object.assign(user, updated);
  } catch (error) {
    console.error('Failed to update:', error);
  }
};
</script>

<style scoped>
.user-profile {
  padding: 1rem;
  border: 1px solid #ccc;
  border-radius: 4px;
}

button {
  margin-top: 1rem;
  padding: 0.5rem 1rem;
  background: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
</style>
```

### Pattern 2: Composables for Reusable Logic
Extract stateful logic into composables (like React hooks).

```javascript
// composables/useLocalStorage.js
import { ref, watch } from 'vue';

export function useLocalStorage(key, initialValue) {
  const storedValue = ref(() => {
    try {
      const item = localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch {
      return initialValue;
    }
  });

  const setValue = (value) => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue.value) : value;
      storedValue.value = valueToStore;
      localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error('Failed to save to localStorage:', error);
    }
  };

  watch(storedValue, (newValue) => {
    localStorage.setItem(key, JSON.stringify(newValue));
  }, { deep: true });

  return { storedValue, setValue };
}

// Usage in component
<script setup>
import { useLocalStorage } from '@/composables/useLocalStorage';

const { storedValue: theme, setValue: setTheme } = useLocalStorage('theme', 'light');

const toggleTheme = () => {
  setTheme(theme.value === 'light' ? 'dark' : 'light');
};
</script>
```

### Pattern 3: Conditional Rendering and Looping
Vue's directive syntax for rendering and loops.

```vue
<template>
  <div class="post-list">
    <p v-if="isLoading">Loading posts...</p>
    <p v-else-if="error" class="error">{{ error }}</p>
    <div v-else-if="posts.length > 0">
      <article v-for="post in posts" :key="post.id" class="post-card">
        <h2>{{ post.title }}</h2>
        <p>{{ post.excerpt }}</p>
        <span v-if="post.featured" class="badge">Featured</span>
      </article>
    </div>
    <p v-else>No posts found.</p>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const posts = ref([]);
const isLoading = ref(false);
const error = ref(null);

onMounted(async () => {
  isLoading.value = true;
  try {
    const response = await fetch('/api/posts');
    if (!response.ok) throw new Error('Failed to fetch posts');
    posts.value = await response.json();
  } catch (err) {
    error.value = err.message;
  } finally {
    isLoading.value = false;
  }
});
</script>

<style scoped>
.post-card {
  border: 1px solid #ddd;
  padding: 1rem;
  margin: 1rem 0;
  border-radius: 4px;
}

.badge {
  background: #28a745;
  color: white;
  padding: 0.25rem 0.5rem;
  border-radius: 4px;
  font-size: 0.875rem;
}
</style>
```

### Pattern 4: Event Handling and Two-Way Binding
Handling user interactions with Vue's event system.

```vue
<template>
  <form @submit.prevent="handleSubmit">
    <div class="form-group">
      <label for="name">Name:</label>
      <input
        id="name"
        v-model="form.name"
        type="text"
        placeholder="Enter your name"
        required
      />
      <span v-if="errors.name" class="error">{{ errors.name }}</span>
    </div>

    <div class="form-group">
      <label for="email">Email:</label>
      <input
        id="email"
        v-model="form.email"
        type="email"
        placeholder="Enter your email"
        required
      />
      <span v-if="errors.email" class="error">{{ errors.email }}</span>
    </div>

    <button type="submit" :disabled="isSubmitting">
      {{ isSubmitting ? 'Submitting...' : 'Submit' }}
    </button>
  </form>
</template>

<script setup>
import { ref, reactive } from 'vue';

const form = reactive({
  name: '',
  email: ''
});

const errors = reactive({});
const isSubmitting = ref(false);

const handleSubmit = async () => {
  errors.name = !form.name ? 'Name is required' : '';
  errors.email = !form.email ? 'Email is required' : '';

  if (errors.name || errors.email) return;

  isSubmitting.value = true;
  try {
    const response = await fetch('/api/users', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(form)
    });

    if (!response.ok) throw new Error('Submission failed');
    const result = await response.json();
    form.name = '';
    form.email = '';
  } catch (error) {
    errors.submit = error.message;
  } finally {
    isSubmitting.value = false;
  }
};
</script>
```

## Folder Structure
```
src/
├── components/
│   ├── Button.vue
│   ├── Card.vue
│   ├── Modal.vue
│   └── layout/
│       ├── Header.vue
│       ├── Sidebar.vue
│       └── Footer.vue
├── composables/
│   ├── useLocalStorage.js
│   ├── useFetch.js
│   ├── useForm.js
│   └── useDebounce.js
├── pages/
│   ├── HomePage.vue
│   ├── ProfilePage.vue
│   └── NotFound.vue
├── stores/
│   ├── user.js
│   └── posts.js
├── utils/
│   ├── api.js
│   ├── validation.js
│   └── formatting.js
├── types/
│   └── index.d.ts
├── styles/
│   ├── globals.css
│   ├── variables.css
│   └── components.css
├── App.vue
└── main.js
```

## Key Principles
- **Composition API over Options API**: Use Composition API for better code reuse and organization.
- **Reactive with deep reactivity**: `reactive()` for objects, `ref()` for primitives and when you need reassignment.
- **Composables for logic reuse**: Extract logic into composables instead of mixins.
- **v-model binding**: Use v-model for two-way binding on form inputs.
- **Key on loops**: Always use stable, unique keys in v-for loops.
- **Scoped styles**: Use `<style scoped>` to avoid CSS conflicts.
- **Pinia for state**: Use Pinia (not Vuex) for global state management.
- **TypeScript support**: Add `lang="ts"` to <script> tags for type safety.

## Commands & Setup
```bash
# Create Vue app with Vite
npm create vite@latest my-app -- --template vue
cd my-app
npm install

# Install common dependencies
npm install pinia axios @vueuse/core

# Start dev server
npm run dev

# Build for production
npm run build
```
