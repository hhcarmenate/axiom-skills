---
name: Bootstrap
description: >
  Bootstrap 5: 12-column grid, components, theming, and accessibility.
  Trigger: Building responsive layouts and component-based UIs with Bootstrap 5.
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
- Building responsive layouts with Bootstrap's 12-column grid
- Using pre-built components (buttons, cards, modals, forms)
- Implementing accessible, standards-compliant HTML
- Theming Bootstrap with Sass variables
- Creating mobile-first responsive designs
- Ensuring browser compatibility and standardization

## Critical Patterns

### Pattern 1: Grid and Layout System
Bootstrap uses a 12-column responsive grid.

```html
<!-- Container wraps grid -->
<div class="container-lg">
  <!-- Row groups columns -->
  <div class="row">
    <!-- Column spans (auto, 1-12 columns) -->
    <div class="col-12 col-sm-6 col-md-4 col-lg-3">
      <p>Takes 12 cols on mobile, 6 on tablet, 4 on desktop, 3 on large screens</p>
    </div>
    <div class="col-12 col-sm-6 col-md-4 col-lg-3"></div>
    <div class="col-12 col-sm-6 col-md-4 col-lg-3"></div>
    <div class="col-12 col-sm-6 col-md-4 col-lg-3"></div>
  </div>

  <!-- Auto-sizing columns -->
  <div class="row">
    <div class="col">Auto width</div>
    <div class="col">Auto width</div>
    <div class="col">Auto width</div>
  </div>

  <!-- Offset columns -->
  <div class="row">
    <div class="col-4 offset-4">Centered (4 cols offset by 4)</div>
  </div>

  <!-- Nested grid -->
  <div class="row">
    <div class="col-8">
      <div class="row">
        <div class="col-6">Nested</div>
        <div class="col-6">Nested</div>
      </div>
    </div>
  </div>
</div>
```

### Pattern 2: Components and Utilities
Bootstrap provides ready-made components and utility classes.

```html
<!-- Buttons with variants -->
<button class="btn btn-primary">Primary</button>
<button class="btn btn-secondary">Secondary</button>
<button class="btn btn-danger">Delete</button>
<button class="btn btn-lg">Large</button>
<button class="btn btn-sm" disabled>Disabled</button>

<!-- Cards (flexible container) -->
<div class="card">
  <img src="image.jpg" class="card-img-top" alt="...">
  <div class="card-body">
    <h5 class="card-title">Card title</h5>
    <p class="card-text">Content here</p>
    <a href="#" class="btn btn-primary">Go somewhere</a>
  </div>
</div>

<!-- Form controls -->
<div class="mb-3">
  <label for="email" class="form-label">Email *</label>
  <input type="email" class="form-control" id="email" required>
  <small class="form-text text-muted">We'll never share your email</small>
</div>

<div class="mb-3">
  <label for="select" class="form-label">Select option</label>
  <select class="form-select" id="select">
    <option>Option 1</option>
    <option>Option 2</option>
  </select>
</div>

<!-- Alerts (feedback) -->
<div class="alert alert-success" role="alert">
  Success! Your changes were saved.
</div>
<div class="alert alert-warning">
  Warning: This action cannot be undone.
</div>
<div class="alert alert-danger">
  Error: Something went wrong.
</div>

<!-- Modal (dialog) -->
<button type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#exampleModal">
  Open Modal
</button>

<div class="modal fade" id="exampleModal" tabindex="-1">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title">Modal title</h5>
        <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
      </div>
      <div class="modal-body">
        Content here...
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
        <button type="button" class="btn btn-primary">Save</button>
      </div>
    </div>
  </div>
</div>
```

### Pattern 3: Theming with Sass
Customize Bootstrap colors and fonts via Sass variables.

```scss
// custom.scss - Override before importing Bootstrap

// Color variables
$primary: #0ea5e9;
$secondary: #6b7280;
$success: #10b981;
$danger: #ef4444;
$warning: #f59e0b;
$info: #06b6d4;

// Typography
$font-family-base: 'Inter', sans-serif;
$font-size-base: 1rem;
$headings-font-weight: 600;

// Spacing (customize if needed)
$spacer: 1rem;

// Border radius
$border-radius: 0.375rem;

// Shadows
$box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);

// Import Bootstrap after overrides
@import 'bootstrap/scss/bootstrap';

// Add custom styles
.btn-custom {
  padding: 0.75rem 1.5rem;
  font-weight: 500;
}
```

### Pattern 4: Responsive Utilities
Use utility classes for spacing, display, alignment.

```html
<!-- Spacing: margin and padding -->
<!-- m = margin, p = padding -->
<!-- t = top, b = bottom, l = left, r = right, x = horizontal, y = vertical -->
<div class="mt-3 mb-5 px-4 py-2">
  Responsive spacing
</div>

<!-- Display and visibility -->
<div class="d-none d-sm-block">Visible on tablet and up</div>
<div class="d-block d-lg-none">Visible on mobile and tablet</div>

<!-- Flexbox -->
<div class="d-flex justify-content-between align-items-center gap-3">
  <span>Left</span>
  <span>Center</span>
  <span>Right</span>
</div>

<!-- Text alignment -->
<p class="text-start">Left aligned</p>
<p class="text-center">Center aligned</p>
<p class="text-end">Right aligned</p>

<!-- Text utilities -->
<p class="fw-bold">Bold text</p>
<p class="text-muted">Muted text</p>
<p class="text-uppercase">Uppercase</p>

<!-- Colors -->
<p class="text-primary">Primary color</p>
<div class="bg-success text-white p-3">Success background</div>
```

### Pattern 5: Navbar and Navigation
Build responsive navigation bars.

```html
<!-- Navbar -->
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <div class="container-lg">
    <a class="navbar-brand" href="#">Brand</a>
    
    <!-- Hamburger menu for mobile -->
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
      <span class="navbar-toggler-icon"></span>
    </button>
    
    <!-- Navigation items (collapsed on mobile) -->
    <div class="collapse navbar-collapse" id="navbarNav">
      <ul class="navbar-nav ms-auto">
        <li class="nav-item">
          <a class="nav-link active" href="#">Home</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="#">Features</a>
        </li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">
            Dropdown
          </a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Action</a></li>
            <li><a class="dropdown-item" href="#">Another action</a></li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</nav>
```

## Folder Structure
```
src/
├── scss/
│   ├── custom.scss          # Custom variables, then @import bootstrap
│   └── styles.scss          # Additional custom styles
├── pages/
│   ├── index.html
│   ├── about.html
│   └── contact.html
├── js/
│   └── main.js              # For Bootstrap JS (modals, dropdowns, etc.)
└── css/
    └── (compiled from scss)

node_modules/
bootstrap/
```

## Key Principles
- **Mobile-first breakpoints**: Base styles are mobile; xs (0), sm (576px), md (768px), lg (992px), xl (1200px), xxl (1400px).
- **12-column grid**: All layouts fit a 12-column system.
- **Semantic HTML**: Use proper heading hierarchy, semantic tags.
- **Keyboard accessible**: All interactive components work with keyboard.
- **Customizable via Sass**: Override variables before importing Bootstrap.
- **Utility-driven**: Bootstrap uses utility classes; learn them well.
- **JavaScript optional**: Some components (modals, dropdowns) need Bootstrap JS, others work fine with CSS.
- **ARIA attributes**: Bootstrap components include proper ARIA for accessibility.

## Commands & Setup
```bash
# Install Bootstrap
npm install bootstrap

# Or with Sass for customization
npm install bootstrap sass

# Import in your project
// In your main JS file
import 'bootstrap/dist/css/bootstrap.min.css';
import 'bootstrap/dist/js/bootstrap.bundle.min.js';

// Or import from SCSS
@import 'bootstrap/scss/bootstrap';

# Build CSS from Sass
npm run sass

# Or compile manually
npx sass src/scss:dist/css
```
