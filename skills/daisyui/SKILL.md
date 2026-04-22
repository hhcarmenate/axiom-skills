---
name: daisyUI
description: >
  daisyUI: Tailwind CSS component library with built-in themes, responsive components, and dark mode.
  Trigger: Building accessible component UIs with daisyUI's Tailwind-based components.
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
- Building component-based UIs with Tailwind CSS components
- Needing pre-designed, accessible components without custom CSS
- Implementing themes (light, dark, cupcake, synthwave, etc.)
- Creating forms, tables, cards, and navigation with daisyUI
- Rapid prototyping with minimal custom styling
- Supporting dark mode with minimal effort

## Critical Patterns

### Pattern 1: Installation and Basic Setup
Add daisyUI to a Tailwind CSS project.

```bash
# Install daisyUI
npm install daisyui

# In tailwind.config.js
export default {
  content: ["./index.html", "./src/**/*.{js,jsx,ts,tsx}"],
  plugins: [require("daisyui")],
  daisyui: {
    themes: ["light", "dark", "cupcake", "bumblebee", "emerald", "corporate", "synthwave"],
    darkMode: "class",
    prefix: "daisy-",
  },
}
```

### Pattern 2: Buttons and Basic Components
Use semantic HTML with daisyUI classes for styled components.

```html
<!-- Buttons with variants -->
<button class="btn btn-primary">Primary</button>
<button class="btn btn-secondary">Secondary</button>
<button class="btn btn-accent">Accent</button>
<button class="btn btn-info">Info</button>
<button class="btn btn-success">Success</button>
<button class="btn btn-warning">Warning</button>
<button class="btn btn-error">Error</button>
<button class="btn btn-ghost">Ghost</button>

<!-- Button sizes -->
<button class="btn btn-lg">Large</button>
<button class="btn btn-md">Medium</button>
<button class="btn btn-sm">Small</button>
<button class="btn btn-xs">Extra Small</button>

<!-- Button states -->
<button class="btn" disabled>Disabled</button>
<button class="btn loading">Loading</button>
<button class="btn btn-wide">Wide Button</button>
<button class="btn btn-block">Full Width</button>

<!-- Button group -->
<div class="btn-group">
  <button class="btn">Button 1</button>
  <button class="btn">Button 2</button>
  <button class="btn">Button 3</button>
</div>
```

### Pattern 3: Cards and Containers
Build card-based layouts.

```html
<!-- Simple card -->
<div class="card bg-base-100 shadow-xl">
  <div class="card-body">
    <h2 class="card-title">Card Title</h2>
    <p>Card content goes here...</p>
    <div class="card-actions justify-end">
      <button class="btn btn-primary">Action</button>
    </div>
  </div>
</div>

<!-- Card with image -->
<div class="card bg-base-100 shadow-xl image-full">
  <figure>
    <img src="image.jpg" alt="Card image" />
  </figure>
  <div class="card-body">
    <h2 class="card-title text-white">Scenic Card</h2>
    <p class="text-white">Content overlays the image</p>
  </div>
</div>

<!-- Card grid -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
  <div class="card bg-base-100 shadow-md"><!-- ... --></div>
  <div class="card bg-base-100 shadow-md"><!-- ... --></div>
  <div class="card bg-base-100 shadow-md"><!-- ... --></div>
</div>
```

### Pattern 4: Forms with daisyUI
Build accessible forms with semantic HTML and daisyUI classes.

```html
<!-- Form group -->
<form class="space-y-4">
  <!-- Text input -->
  <div class="form-control w-full max-w-xs">
    <label class="label">
      <span class="label-text">What is your name?</span>
    </label>
    <input type="text" placeholder="Type here" class="input input-bordered w-full max-w-xs" />
    <label class="label">
      <span class="label-text-alt">Alt label text</span>
    </label>
  </div>

  <!-- Select/Dropdown -->
  <div class="form-control w-full max-w-xs">
    <label class="label">
      <span class="label-text">Pick your favorite genre</span>
    </label>
    <select class="select select-bordered">
      <option disabled selected>You haven't picked yet</option>
      <option>Science Fiction</option>
      <option>Drama</option>
      <option>Action</option>
    </select>
  </div>

  <!-- Checkbox -->
  <div class="form-control">
    <label class="label cursor-pointer">
      <span class="label-text">Remember me</span>
      <input type="checkbox" class="checkbox checkbox-primary" />
    </label>
  </div>

  <!-- Radio buttons -->
  <div class="form-control">
    <label class="label">
      <span class="label-text">Choose one</span>
    </label>
    <div class="space-y-2">
      <label class="label cursor-pointer">
        <input type="radio" name="choice" class="radio radio-primary" />
        <span class="label-text">Option 1</span>
      </label>
      <label class="label cursor-pointer">
        <input type="radio" name="choice" class="radio radio-primary" />
        <span class="label-text">Option 2</span>
      </label>
    </div>
  </div>

  <!-- Textarea -->
  <div class="form-control">
    <label class="label">
      <span class="label-text">Your message</span>
    </label>
    <textarea class="textarea textarea-bordered h-24" placeholder="Bio"></textarea>
  </div>

  <!-- Submit button -->
  <button type="submit" class="btn btn-primary">Submit</button>
</form>
```

### Pattern 5: Navigation and Layout
Build responsive navigation with daisyUI.

```html
<!-- Navbar -->
<div class="navbar bg-base-100 shadow-lg">
  <div class="flex-1">
    <a class="btn btn-ghost text-xl">Brand</a>
  </div>

  <div class="flex-none gap-2">
    <!-- Search input -->
    <div class="form-control">
      <input type="text" placeholder="Search..." class="input input-bordered w-24 md:w-auto" />
    </div>

    <!-- Dropdown menu -->
    <div class="dropdown dropdown-end">
      <tabindex="0" role="button" class="btn btn-ghost btn-circle avatar">
        <div class="w-10 rounded-full">
          <img alt="User avatar" src="avatar.jpg" />
        </div>
      </tabindex>
      <ul tabindex="0" class="dropdown-content z-[1] menu p-2 shadow bg-base-100 rounded-box w-52">
        <li><a>Profile</a></li>
        <li><a>Settings</a></li>
        <li><a>Logout</a></li>
      </ul>
    </div>
  </div>
</div>

<!-- Hero section -->
<div class="hero min-h-screen bg-base-200">
  <div class="hero-content text-center">
    <div class="max-w-md">
      <h1 class="text-5xl font-bold">Hello there</h1>
      <p class="py-6">Provident cupiditate voluptatem et in. Quaerat fugiat ut assumenda excepturi exercitationem.</p>
      <button class="btn btn-primary">Get Started</button>
    </div>
  </div>
</div>

<!-- Breadcrumbs -->
<div class="breadcrumbs text-sm">
  <ul>
    <li><a href="/">Home</a></li>
    <li><a href="/about">About</a></li>
    <li>Contact</li>
  </ul>
</div>
```

### Pattern 6: Alerts and Modals
Display feedback and dialogs.

```html
<!-- Alerts -->
<div class="alert alert-info">
  <span>Info: This is an informational message.</span>
</div>

<div class="alert alert-success">
  <span>Success: Your changes have been saved.</span>
</div>

<div class="alert alert-warning">
  <span>Warning: Be careful, this action is irreversible.</span>
</div>

<div class="alert alert-error">
  <span>Error: Something went wrong. Please try again.</span>
</div>

<!-- Modal -->
<dialog id="my_modal" class="modal">
  <div class="modal-box">
    <h3 class="font-bold text-lg">Confirm Action</h3>
    <p class="py-4">Are you sure you want to proceed? This cannot be undone.</p>
    <div class="modal-action">
      <form method="dialog">
        <button class="btn">Cancel</button>
      </form>
      <button class="btn btn-primary">Confirm</button>
    </div>
  </div>
  <form method="dialog" class="modal-backdrop">
    <button>close</button>
  </form>
</dialog>

<!-- Open modal -->
<button class="btn" onclick="my_modal.showModal()">Open Modal</button>
```

## Folder Structure
```
src/
├── pages/
│   ├── index.html
│   ├── about.html
│   └── contact.html
├── components/
│   ├── navbar.html
│   ├── hero.html
│   ├── cards.html
│   └── forms.html
├── styles/
│   └── globals.css              # @tailwind directives
└── App.jsx                       # React app (if using React)

tailwind.config.js
postcss.config.js
```

## Key Principles
- **Component classes**: Use daisyUI's semantic component classes (btn, card, form-control, etc.).
- **Tailwind integration**: daisyUI extends Tailwind; all Tailwind utilities are still available.
- **Themes built-in**: Switch themes via data-theme attribute or CSS class.
- **Responsive by default**: Components are responsive with Tailwind's breakpoint prefixes.
- **Accessibility**: Components follow WAI-ARIA patterns and semantic HTML.
- **Dark mode**: Use data-theme="dark" or class="dark" for dark mode.
- **No custom CSS needed**: Most use cases are covered by daisyUI and Tailwind.
- **Composable**: Combine daisyUI components with Tailwind utilities freely.

## Commands & Setup
```bash
# Create Vite project with Tailwind
npm create vite@latest my-app -- --template react
cd my-app
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

# Add daisyUI
npm install daisyui

# Or with Next.js
npx create-next-app@latest
npm install daisyui

# Start dev server
npm run dev

# Build for production
npm run build
```
