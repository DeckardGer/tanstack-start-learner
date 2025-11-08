# Lesson 1: File-Based Routing Concepts

## Introduction

File-based routing means your URL structure matches your file system structure. TanStack Router takes this concept and adds powerful type safety and features. Let's understand how it works.

## What is File-Based Routing?

Instead of manually configuring routes like this:

```typescript
// Traditional routing (other frameworks)
const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About },
  { path: '/blog/:id', component: BlogPost },
]
```

Your file structure **IS** your routing configuration:

```
app/routes/
├── __root.tsx      → Layout for all routes
├── index.tsx       → / (homepage)
├── about.tsx       → /about
└── blog/
    ├── index.tsx   → /blog
    └── $id.tsx     → /blog/:id
```

## How File Names Map to URLs

### Basic Routes

```
File                        URL
--------------------------------------------------
app/routes/index.tsx        /
app/routes/about.tsx        /about
app/routes/contact.tsx      /contact
app/routes/pricing.tsx      /pricing
```

### Nested Routes

```
File                           URL
--------------------------------------------------
app/routes/blog/index.tsx      /blog
app/routes/blog/new.tsx        /blog/new
app/routes/blog/published.tsx  /blog/published
```

### Dynamic Routes

```
File                           URL
--------------------------------------------------
app/routes/posts/$postId.tsx   /posts/123
                               /posts/hello-world
                               /posts/any-value

app/routes/users/$userId/
  profile.tsx                  /users/123/profile
```

### Catch-All Routes

```
File                           URL
--------------------------------------------------
app/routes/docs/$.tsx          /docs/anything
                               /docs/a/b/c
                               /docs/nested/path
```

## Special File Names

### `__root.tsx` - The Root Layout

Every app needs a root route that wraps all other routes:

```typescript
// app/routes/__root.tsx
import { createRootRoute, Outlet } from '@tanstack/react-router'
import { TanStackRouterDevtools } from '@tanstack/router-devtools'

export const Route = createRootRoute({
  component: RootComponent,
})

function RootComponent() {
  return (
    <html lang="en">
      <head>
        <meta charSet="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>My App</title>
      </head>
      <body>
        <nav>
          <a href="/">Home</a>
          <a href="/about">About</a>
        </nav>

        <main>
          <Outlet /> {/* Child routes render here */}
        </main>

        <footer>
          © 2024 My App
        </footer>

        {import.meta.env.DEV && <TanStackRouterDevtools />}
      </body>
    </html>
  )
}
```

**Key points**:
- Must be named `__root.tsx` (double underscore)
- Must export a Route created with `createRootRoute()`
- Must render `<Outlet />` where child routes appear
- Wraps all other routes in your app

### `index.tsx` - Default Route

Index routes are the default route for a path:

```typescript
// app/routes/index.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/')({
  component: HomePage,
})

function HomePage() {
  return <h1>Welcome Home!</h1>
}
```

**URL**: `/` (root of your app)

```typescript
// app/routes/blog/index.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/blog/')({
  component: BlogIndexPage,
})

function BlogIndexPage() {
  return <h1>Blog Posts</h1>
}
```

**URL**: `/blog` or `/blog/` (both work)

### `$param.tsx` - Dynamic Parameters

The `$` prefix creates a dynamic segment:

```typescript
// app/routes/posts/$postId.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  component: PostPage,
})

function PostPage() {
  const { postId } = Route.useParams()
  return <h1>Post ID: {postId}</h1>
}
```

**Matches**:
- `/posts/123` → `postId = "123"`
- `/posts/hello-world` → `postId = "hello-world"`
- `/posts/any-value` → `postId = "any-value"`

**Does not match**:
- `/posts` (no parameter)
- `/posts/123/edit` (extra segment)

### `$.tsx` - Catch-All Routes

Matches any number of segments:

```typescript
// app/routes/docs/$.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/docs/$')({
  component: DocsPage,
})

function DocsPage() {
  const { _splat } = Route.useParams()
  return <h1>Docs: {_splat}</h1>
}
```

**Matches**:
- `/docs/getting-started` → `_splat = "getting-started"`
- `/docs/api/reference` → `_splat = "api/reference"`
- `/docs/a/b/c/d` → `_splat = "a/b/c/d"`

### `_layout.tsx` - Layout Routes

Underscore prefix creates a layout without adding to the URL:

```typescript
// app/routes/_layout.tsx
import { createFileRoute, Outlet } from '@tanstack/react-router'

export const Route = createFileRoute('/_layout')({
  component: LayoutComponent,
})

function LayoutComponent() {
  return (
    <div className="sidebar-layout">
      <aside>Sidebar</aside>
      <main>
        <Outlet />
      </main>
    </div>
  )
}
```

Now wrap routes in this layout:

```
app/routes/
├── _layout.tsx           → Layout (no URL)
├── _layout/
│   ├── dashboard.tsx     → /dashboard (uses layout)
│   └── settings.tsx      → /settings (uses layout)
```

Both routes share the sidebar but have different URLs!

### `(group)` - Route Groups

Parentheses create groups without affecting the URL:

```
app/routes/
├── (auth)/              → No URL segment
│   ├── login.tsx        → /login
│   ├── register.tsx     → /register
│   └── forgot.tsx       → /forgot
```

All three routes are grouped logically but the URL doesn't include "(auth)".

## Route Priority

When multiple routes could match, TanStack Router uses this priority:

1. **Static routes** (exact matches)
2. **Dynamic routes** (with parameters)
3. **Catch-all routes** (wildcards)

**Example**:

```
app/routes/posts/
├── new.tsx              → /posts/new (Priority 1)
├── $postId.tsx          → /posts/123 (Priority 2)
└── $.tsx                → /posts/a/b/c (Priority 3)
```

Visiting `/posts/new`:
- Matches `new.tsx` ✅ (static route wins)
- Could match `$postId.tsx` (but lower priority)

## Nested Routes

Folders create nested URL segments:

```
app/routes/
├── __root.tsx
├── index.tsx                    → /
└── dashboard/
    ├── index.tsx                → /dashboard
    ├── analytics.tsx            → /dashboard/analytics
    ├── settings.tsx             → /dashboard/settings
    └── users/
        ├── index.tsx            → /dashboard/users
        └── $userId.tsx          → /dashboard/users/123
```

Each level can have its own layout:

```typescript
// app/routes/dashboard.tsx
import { createFileRoute, Outlet } from '@tanstack/react-router'

export const Route = createFileRoute('/dashboard')({
  component: DashboardLayout,
})

function DashboardLayout() {
  return (
    <div>
      <h1>Dashboard</h1>
      <nav>
        <a href="/dashboard/analytics">Analytics</a>
        <a href="/dashboard/settings">Settings</a>
      </nav>
      <Outlet /> {/* Child routes render here */}
    </div>
  )
}
```

## Route Tree Visualization

Given this structure:

```
app/routes/
├── __root.tsx
├── index.tsx
├── about.tsx
└── blog/
    ├── index.tsx
    └── $postId.tsx
```

The route tree looks like:

```
__root
├── / (index.tsx)
├── /about (about.tsx)
└── /blog
    ├── /blog (index.tsx)
    └── /blog/:postId ($postId.tsx)
```

## Route Generation

TanStack Router automatically generates route types:

```typescript
// app/routeTree.gen.ts (auto-generated)
import { Route as rootRoute } from './routes/__root'
import { Route as IndexRoute } from './routes/index'
import { Route as AboutRoute } from './routes/about'
import { Route as BlogIndexRoute } from './routes/blog/index'
import { Route as BlogPostRoute } from './routes/blog/$postId'

export const routeTree = rootRoute.addChildren([
  IndexRoute,
  AboutRoute,
  BlogIndexRoute,
  BlogPostRoute,
])
```

**Important**: This file is auto-generated. Don't edit it manually!

## File-Based Routing Benefits

### 1. Intuitive Organization

Your file structure matches your URL structure. Want to know where `/blog/post` is handled? Check `app/routes/blog/post.tsx`!

### 2. Type Safety

Routes are fully typed:

```typescript
import { Link } from '@tanstack/react-router'

// TypeScript knows all routes!
<Link to="/">Home</Link>
<Link to="/blog">Blog</Link>
<Link to="/posts/$postId" params={{ postId: '123' }}>Post</Link>

// Error: Route doesn't exist
<Link to="/nonexistent">❌</Link>
```

### 3. Automatic Code Splitting

Each route becomes its own chunk:

```
dist/
├── route-index.js
├── route-about.js
└── route-blog-postId.js
```

Routes load on-demand, improving performance.

### 4. Colocated Data Loading

Data loading stays with the route:

```typescript
// app/routes/blog/$postId.tsx
export const Route = createFileRoute('/blog/$postId')({
  loader: async ({ params }) => {
    const post = await fetchPost(params.postId)
    return { post }
  },
  component: PostPage,
})
```

Everything for this route is in one file!

## Common Patterns

### Blog with Posts

```
app/routes/
├── blog/
│   ├── index.tsx         → /blog (list of posts)
│   ├── $postId.tsx       → /blog/123 (single post)
│   └── new.tsx           → /blog/new (create post)
```

### Dashboard with Sections

```
app/routes/
├── dashboard/
│   ├── index.tsx         → /dashboard (overview)
│   ├── analytics.tsx     → /dashboard/analytics
│   ├── settings.tsx      → /dashboard/settings
│   └── users/
│       ├── index.tsx     → /dashboard/users (list)
│       └── $userId.tsx   → /dashboard/users/123 (detail)
```

### Docs with Nested Pages

```
app/routes/
├── docs/
│   ├── index.tsx         → /docs (docs home)
│   └── $.tsx             → /docs/** (catch-all for any path)
```

### Authentication Flow

```
app/routes/
├── (auth)/               → Route group (no URL segment)
│   ├── login.tsx         → /login
│   ├── register.tsx      → /register
│   ├── forgot.tsx        → /forgot
│   └── reset.tsx         → /reset
```

## Exercises

### Exercise 1: Create Basic Routes

Create these routes:
1. Homepage at `/`
2. About page at `/about`
3. Contact page at `/contact`

### Exercise 2: Nested Routes

Create a blog with:
1. Blog index at `/blog`
2. Individual posts at `/blog/:postId`
3. Create new post at `/blog/new`

### Exercise 3: Layout Routes

1. Create a dashboard layout
2. Add two child routes
3. Verify the layout appears on both

### Exercise 4: Dynamic Routes

1. Create a users route at `/users/:userId`
2. Display the userId parameter
3. Test with different values

## Common Mistakes

### Mistake 1: Forgetting `__root.tsx`

```
Error: No root route found
```

**Solution**: Create `app/routes/__root.tsx` with a root route.

### Mistake 2: Not Using `createFileRoute()`

```typescript
// ❌ Wrong
export const Route = createRootRoute({...})

// ✅ Correct (for non-root routes)
export const Route = createFileRoute('/about')({...})
```

### Mistake 3: Wrong File Name for Dynamic Routes

```
// ❌ Wrong
app/routes/posts/postId.tsx

// ✅ Correct
app/routes/posts/$postId.tsx
```

The `$` prefix is required for dynamic routes!

### Mistake 4: Forgetting `<Outlet />`

```typescript
// ❌ Wrong - child routes won't render
function Layout() {
  return <div>Layout</div>
}

// ✅ Correct
function Layout() {
  return (
    <div>
      Layout
      <Outlet />  {/* Child routes render here */}
    </div>
  )
}
```

## Summary

You now understand:

- File-based routing concepts
- How file names map to URLs
- Special file naming conventions
- Route priority and matching
- Nested routes and layouts
- The benefits of file-based routing

## Next Steps

Continue to [Lesson 2: Creating Route Components](./lesson-02-route-components.md) to learn how to build route components.

## Further Reading

- [TanStack Router File-Based Routing](https://tanstack.com/router/latest/docs/file-based-routing)
- [Route Tree Generation](https://tanstack.com/router/latest/docs/route-trees)
- [Type-Safe Routing](https://tanstack.com/router/latest/docs/type-safety)
