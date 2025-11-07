# Lesson 1: What is TanStack Start?

## Introduction

TanStack Start is a modern, full-stack React framework that brings together the power of TanStack Router and TanStack Query to create a complete solution for building production-ready web applications.

## What is TanStack Start?

TanStack Start is:

**A Full-Stack Framework**: Unlike client-only solutions, TanStack Start provides both client and server capabilities in a unified framework.

**Built on TanStack Router**: It leverages TanStack Router's type-safe, file-based routing system as its foundation.

**Server-Side Rendering (SSR)**: Provides full-document SSR with streaming support for optimal performance and SEO.

**Type-Safe by Default**: End-to-end type safety from server to client, catching errors at compile time.

**Framework Agnostic Core**: While it has excellent React support, the core architecture supports other frameworks like Solid.js.

## Core Capabilities

### 1. Full-Document SSR

TanStack Start renders your entire application on the server, sending fully-formed HTML to the client:

```typescript
// Your routes automatically support SSR
export const Route = createFileRoute('/about')({
  component: About,
})

function About() {
  return <div>This content is server-rendered!</div>
}
```

### 2. Streaming

Support for React Suspense and streaming, allowing parts of your page to load progressively:

```typescript
export const Route = createFileRoute('/dashboard')({
  component: Dashboard,
})

function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      <Suspense fallback={<Spinner />}>
        <AsyncData />
      </Suspense>
    </div>
  )
}
```

### 3. Server Functions

Write server-side code directly in your components with full type safety:

```typescript
import { createServerFn } from '@tanstack/start'

const getUser = createServerFn('GET', async (userId: string) => {
  // This code runs ONLY on the server
  const user = await db.user.findUnique({ where: { id: userId } })
  return user
})

function UserProfile() {
  const user = await getUser('123')
  return <div>{user.name}</div>
}
```

### 4. Built-in Bundling

TanStack Start includes Vite-based bundling out of the box:

- Fast development with Hot Module Replacement (HMR)
- Optimized production builds
- Code splitting and lazy loading
- Asset optimization

### 5. Type-Safe Data Loading

Integration with TanStack Query for type-safe, efficient data fetching:

```typescript
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    const post = await fetchPost(params.postId)
    return { post }
  },
  component: PostPage,
})
```

## Architecture Overview

TanStack Start follows a layered architecture:

```
┌─────────────────────────────────────┐
│         Your Application            │
├─────────────────────────────────────┤
│        TanStack Start               │
│  (Framework & Server Functions)     │
├─────────────────────────────────────┤
│       TanStack Router               │
│  (Type-safe Routing & Navigation)   │
├─────────────────────────────────────┤
│       TanStack Query                │
│  (Data Fetching & Caching)          │
├─────────────────────────────────────┤
│            React                    │
└─────────────────────────────────────┘
```

## Key Concepts

### File-Based Routing

Routes are defined by your file structure:

```
routes/
├── index.tsx          → /
├── about.tsx          → /about
└── posts/
    ├── index.tsx      → /posts
    └── $postId.tsx    → /posts/:postId
```

### Server vs Client Code

TanStack Start intelligently separates server and client code:

- **Server Functions**: Marked with `createServerFn`, run only on server
- **Route Loaders**: Run on server during SSR, on client during navigation
- **Components**: Can render on both server and client

### Progressive Enhancement

Applications work with JavaScript disabled when possible, then enhance with interactivity:

1. Server renders HTML
2. Client receives functional page
3. React hydrates for interactivity
4. Full SPA experience

## Current Status

As of January 2025, TanStack Start is in **Release Candidate (RC)** status:

- Feature-complete and production-ready
- API is stable and unlikely to change
- Being used in production by early adopters
- Active development and community support

## When to Use TanStack Start?

TanStack Start is ideal for:

- **Full-stack applications** that need both server and client logic
- **SEO-critical sites** that require server-side rendering
- **Type-safe projects** where catching errors early is crucial
- **Data-heavy applications** that benefit from advanced caching
- **Teams familiar with React** who want a modern, type-safe framework

## What Makes It Different?

Unlike other frameworks, TanStack Start:

1. **Type safety first**: Built from the ground up with TypeScript
2. **Composable**: Use only what you need, skip what you don't
3. **No vendor lock-in**: Built on open standards and protocols
4. **Transparent**: Clear separation between server and client code
5. **Performance-focused**: Streaming, code-splitting, and optimized by default

## Summary

TanStack Start is a modern full-stack React framework that combines:

- Type-safe routing with TanStack Router
- Powerful data fetching with TanStack Query
- Server-side rendering and streaming
- Server functions for full-stack capabilities
- Built-in bundling and optimization

It provides a complete solution for building fast, type-safe, production-ready web applications.

## Next Steps

Continue to [Lesson 2: The TanStack Ecosystem](./lesson-02-tanstack-ecosystem.md) to learn about the broader TanStack family of libraries.

## Further Reading

- [Official TanStack Start Documentation](https://tanstack.com/start)
- [TanStack Start GitHub Repository](https://github.com/TanStack/router)
- [TanStack Start Overview](https://tanstack.com/start/latest/docs/framework/react/overview)
