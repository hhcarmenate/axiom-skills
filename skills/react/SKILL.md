---
name: React
description: >
  React 19 with React Compiler: building performant, composable UIs using functional components and hooks.
  Trigger: Building component-based user interfaces with React.
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
- Building component-based UIs with React 19+
- Creating reusable component hierarchies with props and state
- Implementing custom hooks for shared logic
- Optimizing render performance without useMemo/useCallback (React Compiler handles this)
- Managing side effects, async operations, or lifecycle logic

## Critical Patterns

### Pattern 1: Functional Component with Hooks
React 19 uses functional components exclusively. All state and side effects are managed via hooks.

```jsx
import { useState, useEffect } from 'react';

export function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchUser = async () => {
      try {
        setIsLoading(true);
        const response = await fetch(`/api/users/${userId}`);
        if (!response.ok) throw new Error('Failed to fetch user');
        const data = await response.json();
        setUser(data);
      } catch (err) {
        setError(err.message);
      } finally {
        setIsLoading(false);
      }
    };

    fetchUser();
  }, [userId]);

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div className="error">{error}</div>;
  if (!user) return <div>No user found</div>;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
```

### Pattern 2: Custom Hooks for Logic Reuse
Extract stateful logic into custom hooks to share between components.

```jsx
function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = typeof window !== 'undefined' ? window.localStorage.getItem(key) : null;
      return item ? JSON.parse(item) : initialValue;
    } catch {
      return initialValue;
    }
  });

  const setValue = (value) => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      if (typeof window !== 'undefined') {
        window.localStorage.setItem(key, JSON.stringify(valueToStore));
      }
    } catch (error) {
      console.error('Failed to save to localStorage:', error);
    }
  };

  return [storedValue, setValue];
}

export function Settings() {
  const [theme, setTheme] = useLocalStorage('theme', 'light');

  return (
    <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
      Switch to {theme === 'light' ? 'dark' : 'light'} mode
    </button>
  );
}
```

### Pattern 3: Error Boundary
Catch rendering errors in child components and display fallback UI.

```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    console.error('Error caught:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return (
        <div className="error-boundary">
          <h2>Something went wrong</h2>
          <p>{this.state.error?.message}</p>
        </div>
      );
    }

    return this.props.children;
  }
}

export function App() {
  return (
    <ErrorBoundary>
      <Dashboard />
    </ErrorBoundary>
  );
}
```

### Pattern 4: Code Splitting with Suspense
Lazy load components to reduce bundle size.

```jsx
import { Suspense, lazy } from 'react';

const HeavyComponent = lazy(() => import('./HeavyComponent'));
const AdminPanel = lazy(() => import('./AdminPanel'));

export function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <HeavyComponent />
      <AdminPanel />
    </Suspense>
  );
}
```

## Folder Structure
```
src/
├── components/
│   ├── Button.jsx
│   ├── Card.jsx
│   ├── Modal.jsx
│   └── layout/
│       ├── Header.jsx
│       ├── Sidebar.jsx
│       └── Footer.jsx
├── hooks/
│   ├── useLocalStorage.js
│   ├── useFetch.js
│   ├── useForm.js
│   └── useDebounce.js
├── pages/
│   ├── HomePage.jsx
│   ├── ProfilePage.jsx
│   └── NotFound.jsx
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
├── App.jsx
└── index.jsx
```

## Key Principles
- **Functional components only**: No class components. Use hooks for all logic.
- **Props are immutable**: Never mutate props directly.
- **Dependency arrays matter**: List all dependencies in useEffect to prevent stale closures.
- **React Compiler**: Eliminates need for useMemo/useCallback; trust the compiler.
- **Keys matter in lists**: Use stable, unique keys (never array index).
- **Avoid prop drilling**: Use Context API for deeply nested data.
- **Separation of concerns**: Keep components focused on a single responsibility.
- **Controlled components**: Form inputs should be controlled by state, not DOM.

## Commands & Setup
```bash
# Create React app with Vite (recommended, faster than Create React App)
npm create vite@latest my-app -- --template react
cd my-app
npm install

# Or with Create React App
npx create-react-app my-app
cd my-app

# Install common dependencies
npm install axios zustand @tanstack/react-query

# Start dev server
npm run dev

# Build for production
npm run build
```
