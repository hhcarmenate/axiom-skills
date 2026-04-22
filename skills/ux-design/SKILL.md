---
name: UX Design
description: >
  Senior UX/UI specialist: design systems, accessibility (WCAG 2.1), responsive design, and modern patterns.
  Trigger: Designing user-centered interfaces with accessibility and modern design principles.
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
- Creating design systems with design tokens and component patterns
- Ensuring accessibility compliance (WCAG 2.1 AA minimum)
- Designing responsive layouts for multiple device sizes
- Establishing typography, color, and spacing systems
- Conducting user research and creating personas
- Implementing micro-interactions and feedback patterns
- Handoff between design and development

## Critical Patterns

### Pattern 1: Design System with Tokens
Build scalable design systems using design tokens.

```css
/* Design Tokens (CSS Custom Properties) */
:root {
  /* Color Palette */
  --color-primary-50: #f0f9ff;
  --color-primary-100: #e0f2fe;
  --color-primary-500: #0ea5e9;
  --color-primary-900: #0c2d6b;
  
  --color-gray-50: #f9fafb;
  --color-gray-900: #111827;
  
  --color-semantic-success: #10b981;
  --color-semantic-warning: #f59e0b;
  --color-semantic-error: #ef4444;
  
  /* Typography */
  --font-family-sans: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  --font-size-xs: 0.75rem;   /* 12px */
  --font-size-sm: 0.875rem;  /* 14px */
  --font-size-base: 1rem;    /* 16px */
  --font-size-lg: 1.125rem;  /* 18px */
  --font-size-xl: 1.25rem;   /* 20px */
  --font-weight-regular: 400;
  --font-weight-semibold: 600;
  --font-weight-bold: 700;
  
  /* Spacing Scale */
  --space-xs: 0.25rem;       /* 4px */
  --space-sm: 0.5rem;        /* 8px */
  --space-md: 1rem;          /* 16px */
  --space-lg: 1.5rem;        /* 24px */
  --space-xl: 2rem;          /* 32px */
  --space-2xl: 3rem;         /* 48px */
  
  /* Shadows */
  --shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
  --shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
  
  /* Border Radius */
  --radius-sm: 0.375rem;     /* 6px */
  --radius-md: 0.5rem;       /* 8px */
  --radius-lg: 0.75rem;      /* 12px */
  
  /* Z-index Scale */
  --z-dropdown: 1000;
  --z-modal: 1050;
  --z-tooltip: 1100;
}
```

### Pattern 2: Responsive Breakpoints (Mobile-First)
Design for mobile first, then enhance for larger screens.

```css
/* Mobile-first: base styles apply to all screens */
.card {
  padding: var(--space-md);
  font-size: var(--font-size-sm);
}

/* Tablet and up (768px+) */
@media (min-width: 768px) {
  .card {
    padding: var(--space-lg);
    font-size: var(--font-size-base);
    display: grid;
    grid-template-columns: repeat(2, 1fr);
  }
}

/* Desktop and up (1024px+) */
@media (min-width: 1024px) {
  .card {
    padding: var(--space-xl);
    grid-template-columns: repeat(3, 1fr);
  }
}

/* Extra large screens (1280px+) */
@media (min-width: 1280px) {
  .card {
    grid-template-columns: repeat(4, 1fr);
  }
}

/* Standard breakpoints */
/* xs: 0px (default), sm: 640px, md: 768px, lg: 1024px, xl: 1280px, 2xl: 1536px */
```

### Pattern 3: Accessibility (WCAG 2.1 AA)
Build accessible components from the start.

```html
<!-- Good: Semantic HTML, proper heading hierarchy -->
<main>
  <h1>Dashboard</h1> <!-- Only one h1 per page -->
  
  <section aria-labelledby="section-title">
    <h2 id="section-title">Recent Activity</h2>
  </section>
  
  <!-- Form with labels -->
  <form>
    <div class="form-group">
      <label for="email">Email *</label>
      <input
        id="email"
        type="email"
        name="email"
        required
        aria-required="true"
        aria-describedby="email-hint"
      />
      <small id="email-hint">We'll never share your email.</small>
    </div>
  </form>
  
  <!-- Buttons with clear labels -->
  <button aria-label="Close modal">✕</button>
  
  <!-- Icon with text fallback -->
  <button>
    <svg aria-hidden="true"><!-- Icon SVG --></svg>
    Save
  </button>
  
  <!-- Skip to main content link -->
  <a href="#main" class="sr-only">Skip to main content</a>
</main>

<style>
  /* Hide skip link visually but keep accessible to screen readers */
  .sr-only {
    position: absolute;
    width: 1px;
    height: 1px;
    padding: 0;
    margin: -1px;
    overflow: hidden;
    clip: rect(0, 0, 0, 0);
    border: 0;
  }
  
  .sr-only:focus {
    position: static;
    width: auto;
    height: auto;
    overflow: visible;
    clip: auto;
    padding: auto;
    margin: auto;
  }
  
  /* Focus visible for keyboard navigation */
  button:focus-visible {
    outline: 2px solid var(--color-primary-500);
    outline-offset: 2px;
  }
  
  /* Color contrast: WCAG AA requires 4.5:1 for normal text, 3:1 for large text */
  /* Test with: https://webaim.org/resources/contrastchecker/ */
</style>
```

### Pattern 4: Micro-interactions and Feedback
Provide clear, purposeful feedback to users.

```css
/* Loading state */
@keyframes spin {
  to { transform: rotate(360deg); }
}

.loading-spinner {
  animation: spin 1s linear infinite;
}

/* Success, warning, error feedback */
.alert {
  padding: var(--space-md);
  border-radius: var(--radius-md);
  border-left: 4px solid;
}

.alert.success {
  background: #d1fae5;
  border-color: var(--color-semantic-success);
  color: #065f46;
}

.alert.warning {
  background: #fef3c7;
  border-color: var(--color-semantic-warning);
  color: #92400e;
}

.alert.error {
  background: #fee2e2;
  border-color: var(--color-semantic-error);
  color: #7f1d1d;
}

/* Smooth transitions (not every property needs animation) */
button {
  background: var(--color-primary-500);
  color: white;
  transition: background-color 200ms ease;
}

button:hover {
  background: var(--color-primary-600);
}

button:active {
  transform: scale(0.98);
}

/* Disabled state */
button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
  transform: none;
}
```

### Pattern 5: Color Theory and Contrast
Accessible, harmonious color palettes.

```css
/* Accessible color palette (WCAG AA compliant) */
:root {
  /* Primary: main action color */
  --primary-light: #2563eb;   /* good on white */
  --primary-dark: #1e40af;    /* darker for hover */
  
  /* Secondary: complementary */
  --secondary-light: #ec4899; /* pink */
  --secondary-dark: #be185d;
  
  /* Semantic colors */
  --success: #10b981;         /* 5.5:1 contrast on white */
  --warning: #f59e0b;         /* 4.5:1 contrast on white */
  --error: #dc2626;           /* 5.3:1 contrast on white */
  --info: #3b82f6;            /* 5.3:1 contrast on white */
  
  /* Neutrals */
  --text-primary: #1f2937;    /* dark gray for primary text */
  --text-secondary: #6b7280;  /* lighter gray for secondary */
  --text-disabled: #9ca3af;   /* light gray for disabled */
  --bg-primary: #ffffff;      /* main background */
  --bg-secondary: #f3f4f6;    /* subtle background */
  --border: #e5e7eb;          /* border color */
}

/* Dark mode */
@media (prefers-color-scheme: dark) {
  :root {
    --text-primary: #f3f4f6;
    --text-secondary: #d1d5db;
    --text-disabled: #9ca3af;
    --bg-primary: #111827;
    --bg-secondary: #1f2937;
    --border: #374151;
  }
}
```

## Folder Structure
```
design/
├── design-system/
│   ├── colors.md
│   ├── typography.md
│   ├── spacing.md
│   ├── shadows.md
│   └── tokens.css
├── components/
│   ├── button.md
│   ├── card.md
│   ├── modal.md
│   └── form.md
├── patterns/
│   ├── forms.md
│   ├── tables.md
│   ├── navigation.md
│   └── feedback.md
├── accessibility/
│   ├── wcag.md
│   ├── keyboard-nav.md
│   └── screen-readers.md
└── research/
    ├── personas.md
    ├── user-journeys.md
    └── usability-tests.md
```

## Key Principles
- **Mobile-first**: Design for small screens first, enhance for larger ones.
- **Accessibility from start**: WCAG 2.1 AA is the minimum standard.
- **Consistent spacing**: Use a spacing scale (4, 8, 12, 16, 24, 32, 48, 64).
- **Hierarchy with typography**: Limit to 2-3 font families, use size and weight for hierarchy.
- **Feedback always**: Loading, success, warning, error states must be clear.
- **Keyboard navigation**: Every interactive element must be keyboard accessible.
- **Color contrast**: 4.5:1 for normal text, 3:1 for large text (WCAG AA).
- **User research first**: Validate decisions with real users.

## Commands & Setup
```bash
# Accessibility testing
# Lighthouse (built into Chrome DevTools)
# axe DevTools: https://www.deque.com/axe/devtools/

# Color contrast checker
# WebAIM: https://webaim.org/resources/contrastchecker/

# Accessible color palette generator
# https://coolors.co/
# https://accessible-colors.com/

# Typography scale calculator
# https://www.modularscale.com/
```
