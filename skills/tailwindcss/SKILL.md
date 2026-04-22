---
name: Tailwind CSS
description: >
  Tailwind CSS 4: utility-first CSS with custom configuration, dark mode, responsive design, and animations.
  Trigger: Styling React, Vue, or other components with Tailwind CSS utility classes.
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
- Styling components with utility-first CSS approach
- Building responsive designs with Tailwind's breakpoint system
- Implementing dark mode support
- Creating custom components with @apply or utility classes
- Optimizing CSS bundle size with PurgeCSS
- Extending Tailwind with custom colors, spacing, and fonts

## Critical Patterns

### Pattern 1: Utility-First Styling (No CSS Files)
Apply styles directly via class names; no separate CSS files needed.

```html
<!-- Button component with utilities -->
<button class="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-300 disabled:opacity-50 disabled:cursor-not-allowed transition-colors duration-200">
  Click me
</button>

<!-- Card with responsive padding -->
<div class="p-4 sm:p-6 md:p-8 bg-white rounded-lg shadow-md">
  <h2 class="text-lg md:text-2xl font-bold text-gray-900">Title</h2>
  <p class="text-gray-600 mt-2">Content goes here</p>
</div>

<!-- Responsive grid -->
<div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4">
  <!-- Grid items -->
</div>

<!-- Flexbox layout -->
<div class="flex items-center justify-between gap-4">
  <span class="text-sm text-gray-500">Label</span>
  <button class="px-3 py-1 bg-green-500 text-white rounded">Save</button>
</div>
```

### Pattern 2: Responsive Design (Mobile-First)
Use breakpoint prefixes for responsive layouts.

```html
<!-- Mobile-first responsive design -->
<div class="block sm:hidden">Mobile menu</div>
<div class="hidden sm:block">Desktop menu</div>

<!-- Responsive typography -->
<h1 class="text-2xl sm:text-3xl md:text-4xl lg:text-5xl font-bold">
  Responsive Heading
</h1>

<!-- Responsive spacing -->
<div class="mx-4 sm:mx-6 md:mx-8 lg:mx-12">
  Content with responsive margins
</div>

<!-- Responsive columns -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4">
  <!-- Items -->
</div>

<!-- Responsive display -->
<img 
  src="mobile.jpg" 
  class="sm:hidden" 
  alt="Mobile view"
/>
<img 
  src="desktop.jpg" 
  class="hidden sm:block" 
  alt="Desktop view"
/>
```

### Pattern 3: Dark Mode Support
Implement dark mode with theme-aware utilities.

```html
<!-- tailwind.config.js -->
export default {
  darkMode: 'class', // Toggle dark mode with class
  theme: {
    extend: {},
  },
}

<!-- HTML -->
<html class="dark">
  <body class="bg-white dark:bg-gray-900 text-gray-900 dark:text-white">
    <div class="bg-gray-100 dark:bg-gray-800 p-4 rounded-lg">
      <h2 class="text-gray-900 dark:text-white">Dark Mode Example</h2>
      <p class="text-gray-600 dark:text-gray-300">
        This text changes color in dark mode.
      </p>
    </div>
  </body>
</html>

<!-- React component with dark mode toggle -->
<script>
export function DarkModeToggle() {
  const [isDark, setIsDark] = useState(false);

  const toggleDarkMode = () => {
    setIsDark(!isDark);
    document.documentElement.classList.toggle('dark');
  };

  return (
    <button onClick={toggleDarkMode}>
      {isDark ? '☀️ Light' : '🌙 Dark'}
    </button>
  );
}
</script>
```

### Pattern 4: Custom Configuration
Extend Tailwind with custom colors, fonts, and spacing.

```javascript
// tailwind.config.js
export default {
  theme: {
    extend: {
      // Custom colors
      colors: {
        brand: {
          50: '#f0f9ff',
          500: '#0ea5e9',
          900: '#0c2d6b',
        },
      },
      
      // Custom fonts
      fontFamily: {
        sans: ['Inter', 'sans-serif'],
        mono: ['Fira Code', 'monospace'],
      },
      
      // Custom spacing
      spacing: {
        '13': '3.25rem',
        '15': '3.75rem',
        '128': '32rem',
      },
      
      // Custom animations
      animation: {
        'fade-in': 'fadeIn 300ms ease-in-out',
        'slide-up': 'slideUp 300ms ease-out',
      },
      keyframes: {
        fadeIn: {
          '0%': { opacity: '0' },
          '100%': { opacity: '1' },
        },
        slideUp: {
          '0%': { transform: 'translateY(10px)', opacity: '0' },
          '100%': { transform: 'translateY(0)', opacity: '1' },
        },
      },
      
      // Custom shadows
      boxShadow: {
        'elevation-1': '0 1px 2px 0 rgba(0, 0, 0, 0.05)',
        'elevation-2': '0 4px 6px -1px rgba(0, 0, 0, 0.1)',
        'elevation-3': '0 10px 15px -3px rgba(0, 0, 0, 0.1)',
      },
    },
  },
  
  plugins: [],
  content: [
    './src/**/*.{jsx,tsx}',
    './index.html',
  ],
}
```

### Pattern 5: Reusable Component Classes with @apply
Create component classes while keeping utility-first approach.

```css
/* src/styles/components.css */
@layer components {
  .btn-primary {
    @apply px-4 py-2 bg-blue-500 text-white rounded-md font-semibold hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-300 disabled:opacity-50 transition-colors;
  }

  .btn-secondary {
    @apply px-4 py-2 bg-gray-200 text-gray-900 rounded-md font-semibold hover:bg-gray-300 disabled:opacity-50 transition-colors;
  }

  .card {
    @apply bg-white rounded-lg shadow-md p-6 hover:shadow-lg transition-shadow;
  }

  .form-input {
    @apply w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 dark:bg-gray-800 dark:border-gray-600 dark:text-white;
  }

  .badge {
    @apply inline-block px-2 py-1 text-sm font-semibold rounded-full;
  }
}

<!-- HTML usage -->
<button class="btn-primary">Save</button>
<div class="card">Card content</div>
<input type="text" class="form-input" placeholder="Enter text" />
<span class="badge bg-green-100 text-green-800">New</span>
```

## Folder Structure
```
src/
├── styles/
│   ├── globals.css          # @tailwind directives
│   └── components.css       # @layer components
├── components/
│   ├── Button.jsx
│   ├── Card.jsx
│   ├── Modal.jsx
│   └── Form/
│       ├── Input.jsx
│       └── Select.jsx
├── pages/
│   ├── HomePage.jsx
│   └── DashboardPage.jsx
└── App.jsx

tailwind.config.js
postcss.config.js
```

## Key Principles
- **Utility-first workflow**: Apply styles via classes, not CSS files.
- **Responsive by default**: Use breakpoint prefixes (sm:, md:, lg:, xl:) throughout.
- **No premature abstraction**: Write utilities first, extract @apply components only when patterns repeat 3+ times.
- **Dark mode awareness**: Design dark mode from the start with dark: prefix.
- **Avoid inline styles**: Use Tailwind utilities instead of style attribute.
- **Limit custom CSS**: Most needs can be met with configuration; only add @layer for true reusable components.
- **Performance**: PurgeCSS automatically removes unused utilities in production.
- **Accessibility**: Use focus: and disabled: prefixes for interactive elements.

## Commands & Setup
```bash
# Install Tailwind CSS
npm install -D tailwindcss postcss autoprefixer

# Generate config files
npx tailwindcss init -p

# Start dev server (styles compile automatically)
npm run dev

# Build for production (unused utilities removed)
npm run build

# Use with different frameworks
# React + Vite
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

# Next.js
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

# Vue
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```
