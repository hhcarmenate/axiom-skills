---
name: Astro
description: >
  Astro 5 with islands architecture: content-first websites using .astro components, selective hydration, and static-first rendering.
  Trigger: Building fast content sites, blogs, marketing pages, or hybrid apps with Astro.
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
- Building content-first websites with Astro 5+
- Creating static-first pages with minimal client-side JavaScript
- Composing reusable layouts and `.astro` components
- Adding interactivity through islands with React, Vue, Svelte, or other UI frameworks
- Managing blog posts, docs, or marketing content with content collections

## Critical Patterns

### Pattern 1: Astro Components with Typed Props
Use frontmatter for server-side logic and keep the template focused on rendering.

```astro
---
interface Props {
  title: string;
  description?: string;
  ctaHref?: string;
}

const {
  title,
  description = 'Fast websites built with Astro',
  ctaHref = '/contact'
} = Astro.props;
---

<section class="hero">
  <h1>{title}</h1>
  <p>{description}</p>
  <a href={ctaHref} class="button">Get Started</a>
</section>

<style>
  .hero {
    padding: 4rem 1.5rem;
    text-align: center;
  }

  .button {
    display: inline-block;
    margin-top: 1rem;
    padding: 0.75rem 1.25rem;
    background: #111827;
    color: #ffffff;
    text-decoration: none;
    border-radius: 0.5rem;
  }
</style>
```

### Pattern 2: Shared Layouts with Slots
Layouts should own the document shell, metadata, and shared page structure.

```astro
---
interface Props {
  title: string;
  description?: string;
}

const {
  title,
  description = 'Astro site'
} = Astro.props;
---

<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta name="description" content={description} />
    <title>{title}</title>
  </head>
  <body>
    <header>
      <nav>
        <a href="/">Home</a>
        <a href="/blog">Blog</a>
        <a href="/about">About</a>
      </nav>
    </header>

    <main>
      <slot />
    </main>
  </body>
</html>
```

### Pattern 3: Islands for Targeted Interactivity
Hydrate only the components that require client-side behavior.

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
import NewsletterForm from '../components/NewsletterForm.jsx';
import SearchBox from '../components/SearchBox.vue';
---

<BaseLayout title="Home">
  <section>
    <h1>Ship less JavaScript</h1>
    <p>Most of this page stays static and fast.</p>
  </section>

  <NewsletterForm client:load />
  <SearchBox client:visible />
</BaseLayout>
```

```jsx
import { useState } from 'react';

export default function NewsletterForm() {
  const [email, setEmail] = useState('');
  const [status, setStatus] = useState('idle');

  async function handleSubmit(event) {
    event.preventDefault();
    setStatus('submitting');

    try {
      const response = await fetch('/api/newsletter', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ email })
      });

      if (!response.ok) {
        throw new Error('Subscription failed');
      }

      setStatus('success');
      setEmail('');
    } catch (error) {
      console.error(error);
      setStatus('error');
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={email}
        onChange={(event) => setEmail(event.target.value)}
        placeholder="Email address"
        required
      />
      <button type="submit" disabled={status === 'submitting'}>
        {status === 'submitting' ? 'Submitting...' : 'Subscribe'}
      </button>
      {status === 'error' && <p>Unable to subscribe right now.</p>}
      {status === 'success' && <p>Subscribed successfully.</p>}
    </form>
  );
}
```

### Pattern 4: Content Collections with Schema Validation
Use content collections for structured markdown and validate frontmatter with Zod.

```typescript
// src/content/config.ts
import { defineCollection, z } from 'astro:content';

const blog = defineCollection({
  schema: z.object({
    title: z.string(),
    description: z.string(),
    pubDate: z.coerce.date(),
    updatedDate: z.coerce.date().optional(),
    draft: z.boolean().default(false),
    tags: z.array(z.string()).default([])
  })
});

export const collections = {
  blog
};
```

```astro
---
import BaseLayout from '../../layouts/BaseLayout.astro';
import { getCollection } from 'astro:content';

const posts = (await getCollection('blog'))
  .filter((post) => !post.data.draft)
  .sort((a, b) => b.data.pubDate.valueOf() - a.data.pubDate.valueOf());
---

<BaseLayout title="Blog">
  <ul>
    {posts.map((post) => (
      <li>
        <a href={`/blog/${post.slug}`}>{post.data.title}</a>
      </li>
    ))}
  </ul>
</BaseLayout>
```

### Pattern 5: Static Routes with getStaticPaths
Pre-generate dynamic pages for content-driven routes.

```astro
---
import BaseLayout from '../../layouts/BaseLayout.astro';
import { getCollection } from 'astro:content';

export async function getStaticPaths() {
  const posts = await getCollection('blog', ({ data }) => !data.draft);

  return posts.map((post) => ({
    params: { slug: post.slug },
    props: { post }
  }));
}

const { post } = Astro.props;
const { Content } = await post.render();
---

<BaseLayout title={post.data.title} description={post.data.description}>
  <article>
    <h1>{post.data.title}</h1>
    <p>{post.data.pubDate.toDateString()}</p>
    <Content />
  </article>
</BaseLayout>
```

## Folder Structure
```text
src/
├── components/
│   ├── Hero.astro
│   ├── Navigation.astro
│   └── NewsletterForm.jsx
├── content/
│   ├── blog/
│   │   ├── welcome-to-astro.md
│   │   └── launch-checklist.md
│   └── config.ts
├── layouts/
│   └── BaseLayout.astro
├── pages/
│   ├── index.astro
│   ├── about.astro
│   ├── blog/
│   │   ├── index.astro
│   │   └── [slug].astro
│   └── api/
│       └── newsletter.ts
├── styles/
│   └── global.css
└── env.d.ts

public/
├── favicon.svg
└── images/

astro.config.mjs
package.json
tsconfig.json
```

## Key Principles
- **Static by default**: Render pages ahead of time unless a route genuinely needs server output.
- **Hydrate selectively**: Use `client:*` directives only for interactive islands.
- **Keep frontmatter server-only**: Fetch data, derive props, and prepare content in frontmatter instead of shipping that logic to the browser.
- **Use content collections**: Validate content schemas instead of trusting free-form frontmatter.
- **Prefer layouts over duplication**: Centralize document structure, metadata, and navigation.
- **Ship less JavaScript**: Framework components are useful, but the Astro page should stay mostly HTML and CSS.

## Commands & Setup
```bash
# Create a new Astro project
npm create astro@latest

# Install dependencies
npm install

# Start development server
npm run dev

# Build for production
npm run build

# Preview production build locally
npm run preview
```
