---
name: Next.js
description: >
  Next.js 15 App Router: server-first architecture with server components, server actions, and file-based routing.
  Trigger: Building full-stack applications with Next.js using the App Router.
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
- Building full-stack applications with Next.js 15+
- Creating server components for data fetching at the edge
- Handling form submissions and mutations with server actions
- Setting up route handlers for API endpoints
- Implementing middleware for authentication, redirects, or logging

## Critical Patterns

### Pattern 1: Server Component with Data Fetching
Server components fetch data directly without extra API calls.

```jsx
// app/posts/[id]/page.jsx
import { notFound } from 'next/navigation';

async function getPost(id) {
  const response = await fetch(`https://api.example.com/posts/${id}`, {
    next: { revalidate: 60 } // ISR: revalidate every 60 seconds
  });
  if (!response.ok) return null;
  return response.json();
}

export async function generateMetadata({ params }) {
  const post = await getPost(params.id);
  return { title: post?.title || 'Post Not Found' };
}

export default async function PostPage({ params }) {
  const post = await getPost(params.id);
  if (!post) notFound();

  return (
    <article>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </article>
  );
}
```

### Pattern 2: Server Actions for Form Submissions
Use server actions to handle form submissions without creating separate API routes.

```jsx
// app/actions.js
'use server';

import { revalidatePath } from 'next/cache';

export async function createPost(formData) {
  const title = formData.get('title');
  const content = formData.get('content');

  if (!title || !content) {
    return { error: 'Title and content are required' };
  }

  try {
    const response = await fetch('https://api.example.com/posts', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ title, content })
    });

    if (!response.ok) throw new Error('Failed to create post');

    const newPost = await response.json();
    revalidatePath('/posts');
    
    return { success: true, id: newPost.id };
  } catch (error) {
    return { error: error.message };
  }
}

// app/posts/new/page.jsx
'use client';

import { useActionState } from 'react';
import { createPost } from '@/app/actions';

export default function NewPostPage() {
  const [state, formAction, isPending] = useActionState(createPost, {});

  return (
    <form action={formAction}>
      <input name="title" type="text" placeholder="Title" required />
      <textarea name="content" placeholder="Content" required />
      <button type="submit" disabled={isPending}>
        {isPending ? 'Creating...' : 'Create Post'}
      </button>
      {state.error && <p className="error">{state.error}</p>}
      {state.success && <p className="success">Post created!</p>}
    </form>
  );
}
```

### Pattern 3: Route Handlers for REST APIs
Create REST API endpoints using route handlers.

```jsx
// app/api/posts/[id]/route.js
import { NextResponse } from 'next/server';

export async function GET(request, { params }) {
  try {
    const response = await fetch(`https://api.example.com/posts/${params.id}`);
    if (!response.ok) return NextResponse.json({ error: 'Not found' }, { status: 404 });
    const data = await response.json();
    return NextResponse.json(data);
  } catch (error) {
    return NextResponse.json({ error: error.message }, { status: 500 });
  }
}

export async function PUT(request, { params }) {
  const body = await request.json();
  try {
    const response = await fetch(`https://api.example.com/posts/${params.id}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(body)
    });
    if (!response.ok) throw new Error('Update failed');
    const data = await response.json();
    return NextResponse.json(data);
  } catch (error) {
    return NextResponse.json({ error: error.message }, { status: 500 });
  }
}

export async function DELETE(request, { params }) {
  try {
    const response = await fetch(`https://api.example.com/posts/${params.id}`, {
      method: 'DELETE'
    });
    if (!response.ok) throw new Error('Delete failed');
    return NextResponse.json({ success: true });
  } catch (error) {
    return NextResponse.json({ error: error.message }, { status: 500 });
  }
}
```

### Pattern 4: Middleware for Authentication
Protect routes and handle redirects at the edge.

```javascript
// middleware.js
import { NextResponse } from 'next/server';

const protectedRoutes = ['/dashboard', '/admin', '/settings'];

export function middleware(request) {
  const { pathname } = request.nextUrl;
  const token = request.cookies.get('authToken')?.value;

  // Redirect unauthenticated users to login
  if (protectedRoutes.some(route => pathname.startsWith(route)) && !token) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  // Redirect authenticated users away from login
  if (pathname === '/login' && token) {
    return NextResponse.redirect(new URL('/dashboard', request.url));
  }

  return NextResponse.next();
}

export const config = {
  matcher: ['/((?!_next/static|_next/image|favicon.ico).*)']
};
```

## Folder Structure
```
app/
├── layout.jsx                    # Root layout
├── page.jsx                      # Home page
├── error.jsx                     # Error boundary
├── not-found.jsx                 # 404 page
├── (auth)/
│   ├── login/page.jsx
│   ├── signup/page.jsx
│   └── layout.jsx
├── dashboard/
│   ├── page.jsx
│   ├── layout.jsx
│   └── [id]/page.jsx
├── api/
│   ├── posts/
│   │   ├── route.js              # GET, POST /api/posts
│   │   └── [id]/route.js         # GET, PUT, DELETE /api/posts/:id
│   ├── auth/
│   │   └── [...nextauth]/route.js
│   └── health/route.js
├── actions.js                    # Server actions
└── middleware.js                 # Edge middleware

public/
├── logo.svg
└── favicon.ico

lib/
├── db.js
├── auth.js
└── utils.js

components/
├── Header.jsx
├── Footer.jsx
└── Navigation.jsx
```

## Key Principles
- **Server components by default**: Use client components only when necessary (interactivity, hooks).
- **No data fetching in layout**: Fetch data in page components or use server actions.
- **Revalidate strategically**: Use `revalidate` and `revalidatePath` for ISR (Incremental Static Regeneration).
- **Never expose secrets in client components**: Move secrets to server actions or environment variables.
- **Use TypeScript paths**: Configure jsconfig.json or tsconfig.json for `@/*` imports.
- **Error boundaries**: Create error.jsx files at each route segment.
- **Dynamic routes carefully**: Use `generateStaticParams` for SSG; use dynamic routes with care for SEO.

## Commands & Setup
```bash
# Create Next.js app with App Router
npx create-next-app@latest my-app
cd my-app

# Install common dependencies
npm install @tanstack/react-query zod axios

# Start dev server
npm run dev

# Build for production
npm run build
npm start

# Type check
npx tsc --noEmit
```
