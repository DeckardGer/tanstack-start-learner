# Lesson 5: Index Routes and Layouts

## Introduction

Index routes and layouts are powerful concepts that help you organize your application structure. Index routes handle default paths, while layouts provide consistent UI across multiple routes. Let's master both concepts.

## Index Routes

Index routes are the default route for a path segment.

### What Are Index Routes?

Think of index routes like `index.html` in traditional web servers - they're the default page for a directory.

```
app/routes/
├── index.tsx           → / (root index)
└── blog/
    ├── index.tsx       → /blog (blog index)
    └── $postId.tsx     → /blog/123 (specific post)
```

### Creating Index Routes

File must be named `index.tsx`:

```typescript
// app/routes/index.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/')({
  component: HomePage,
})

function HomePage() {
  return (
    <div>
      <h1>Welcome Home!</h1>
      <p>This is the homepage at /</p>
    </div>
  )
}
```

### Nested Index Routes

```typescript
// app/routes/blog/index.tsx
import { createFileRoute, Link } from '@tanstack/react-router'

export const Route = createFileRoute('/blog/')({
  loader: async () => {
    const posts = await fetchPosts()
    return { posts }
  },
  component: BlogIndexPage,
})

function BlogIndexPage() {
  const { posts } = Route.useLoaderData()

  return (
    <div>
      <h1>Blog Posts</h1>
      <div className="posts-grid">
        {posts.map(post => (
          <Link
            key={post.id}
            to="/blog/$postId"
            params={{ postId: post.id }}
          >
            {post.title}
          </Link>
        ))}
      </div>
    </div>
  )
}
```

### Index vs Named Routes

```
/blog       → blog/index.tsx (index route)
/blog/123   → blog/$postId.tsx (named route)
/blog/new   → blog/new.tsx (named route)
```

**When to use index routes**:
- List or overview pages
- Default content for a section
- Landing pages

## Layout Routes

Layouts wrap child routes with consistent UI.

### Basic Layout

```typescript
// app/routes/__root.tsx
import { createRootRoute, Outlet } from '@tanstack/react-router'

export const Route = createRootRoute({
  component: RootLayout,
})

function RootLayout() {
  return (
    <html>
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
      </body>
    </html>
  )
}
```

### Nested Layouts

```
app/routes/
├── __root.tsx              → Root layout
├── index.tsx               → Home page
├── dashboard.tsx           → Dashboard layout
└── dashboard/
    ├── index.tsx           → Dashboard home
    ├── analytics.tsx       → Dashboard analytics
    └── settings.tsx        → Dashboard settings
```

```typescript
// app/routes/dashboard.tsx
import { createFileRoute, Outlet, Link } from '@tanstack/react-router'

export const Route = createFileRoute('/dashboard')({
  component: DashboardLayout,
})

function DashboardLayout() {
  return (
    <div className="dashboard">
      {/* Sidebar */}
      <aside className="sidebar">
        <nav>
          <Link to="/dashboard" activeProps={{ className: 'active' }}>
            Overview
          </Link>
          <Link to="/dashboard/analytics" activeProps={{ className: 'active' }}>
            Analytics
          </Link>
          <Link to="/dashboard/settings" activeProps={{ className: 'active' }}>
            Settings
          </Link>
        </nav>
      </aside>

      {/* Main content */}
      <main className="content">
        <Outlet /> {/* Child routes render here */}
      </main>
    </div>
  )
}
```

### Layout Routes Without URL Segment

Use `_` prefix for layouts that don't add to the URL:

```
app/routes/
├── _layout.tsx             → Layout (no URL)
└── _layout/
    ├── dashboard.tsx       → /dashboard
    ├── analytics.tsx       → /analytics
    └── settings.tsx        → /settings
```

```typescript
// app/routes/_layout.tsx
import { createFileRoute, Outlet } from '@tanstack/react-router'

export const Route = createFileRoute('/_layout')({
  component: LayoutComponent,
})

function LayoutComponent() {
  return (
    <div className="two-column-layout">
      <aside>Shared Sidebar</aside>
      <main>
        <Outlet />
      </main>
    </div>
  )
}
```

All routes under `_layout/` share this layout but don't have `_layout` in their URL!

## The `<Outlet />` Component

The `Outlet` component is where child routes render.

### Basic Outlet

```typescript
import { Outlet } from '@tanstack/react-router'

function Layout() {
  return (
    <div>
      <header>Header</header>
      <Outlet /> {/* Child routes here */}
      <footer>Footer</footer>
    </div>
  )
}
```

### Multiple Outlets (Not Directly Supported)

TanStack Router uses nested routes instead of named outlets:

```typescript
// Instead of multiple outlets, use nested layouts
app/routes/
├── dashboard.tsx           → Outer layout
└── dashboard/
    ├── _layout.tsx         → Inner layout
    └── _layout/
        └── content.tsx     → Content page
```

## Complete Example: Blog with Layouts

```
app/routes/
├── __root.tsx              → Root layout
├── index.tsx               → Homepage
├── blog.tsx                → Blog layout
└── blog/
    ├── index.tsx           → Blog list
    ├── $postId.tsx         → Blog post
    └── new.tsx             → Create post
```

### Root Layout

```typescript
// app/routes/__root.tsx
import { createRootRoute, Outlet, Link } from '@tanstack/react-router'
import { TanStackRouterDevtools } from '@tanstack/router-devtools'

export const Route = createRootRoute({
  component: RootLayout,
})

function RootLayout() {
  return (
    <html lang="en">
      <head>
        <meta charSet="UTF-8" />
        <title>My Blog</title>
      </head>
      <body>
        {/* Global navigation */}
        <nav className="top-nav">
          <Link to="/">Home</Link>
          <Link to="/blog">Blog</Link>
          <Link to="/about">About</Link>
        </nav>

        {/* Main content */}
        <Outlet />

        {/* Global footer */}
        <footer className="global-footer">
          © 2024 My Blog
        </footer>

        {/* DevTools */}
        {import.meta.env.DEV && <TanStackRouterDevtools />}
      </body>
    </html>
  )
}
```

### Blog Layout

```typescript
// app/routes/blog.tsx
import { createFileRoute, Outlet, Link } from '@tanstack/react-router'

export const Route = createFileRoute('/blog')({
  loader: async () => {
    const categories = await fetchCategories()
    return { categories }
  },
  component: BlogLayout,
})

function BlogLayout() {
  const { categories } = Route.useLoaderData()

  return (
    <div className="blog-layout">
      {/* Blog header */}
      <header className="blog-header">
        <h1>Blog</h1>
        <Link
          to="/blog/new"
          className="new-post-button"
        >
          New Post
        </Link>
      </header>

      <div className="blog-container">
        {/* Sidebar */}
        <aside className="blog-sidebar">
          <h3>Categories</h3>
          <ul>
            {categories.map(category => (
              <li key={category.id}>
                <Link
                  to="/blog"
                  search={{ category: category.slug }}
                >
                  {category.name}
                </Link>
              </li>
            ))}
          </ul>
        </aside>

        {/* Main content - child routes render here */}
        <main className="blog-main">
          <Outlet />
        </main>
      </div>
    </div>
  )
}
```

### Blog Index (List)

```typescript
// app/routes/blog/index.tsx
import { createFileRoute, Link } from '@tanstack/react-router'

export const Route = createFileRoute('/blog/')({
  loader: async ({ context }) => {
    const posts = await fetchPosts()
    return { posts }
  },
  component: BlogIndexPage,
})

function BlogIndexPage() {
  const { posts } = Route.useLoaderData()

  return (
    <div>
      <h2>Recent Posts</h2>
      <div className="posts-grid">
        {posts.map(post => (
          <article key={post.id} className="post-card">
            <Link
              to="/blog/$postId"
              params={{ postId: post.id }}
            >
              <h3>{post.title}</h3>
              <p>{post.excerpt}</p>
              <span>{new Date(post.createdAt).toLocaleDateString()}</span>
            </Link>
          </article>
        ))}
      </div>
    </div>
  )
}
```

### Blog Post Detail

```typescript
// app/routes/blog/$postId.tsx
import { createFileRoute, Link } from '@tanstack/react-router'

export const Route = createFileRoute('/blog/$postId')({
  loader: async ({ params }) => {
    const post = await fetchPost(params.postId)
    return { post }
  },
  component: BlogPostPage,
})

function BlogPostPage() {
  const { post } = Route.useLoaderData()

  return (
    <article className="post-detail">
      <Link to="/blog" className="back-link">
        ← Back to blog
      </Link>

      <header>
        <h2>{post.title}</h2>
        <div className="meta">
          {post.author} • {new Date(post.createdAt).toLocaleDateString()}
        </div>
      </header>

      <div className="content">
        {post.content}
      </div>
    </article>
  )
}
```

## Layout Hierarchy

Routes can have multiple nested layouts:

```
__root (Root Layout)
  └── Header, Footer
      └── blog (Blog Layout)
          └── Blog header, Sidebar
              └── blog/index (Blog List)
                  └── List of posts
              └── blog/$postId (Blog Post)
                  └── Single post
```

Each layout wraps its children!

## Passing Data Through Layouts

### Using Context

```typescript
// app/routes/dashboard.tsx
export const Route = createFileRoute('/dashboard')({
  loader: async () => {
    const user = await fetchCurrentUser()
    return { user }
  },
  component: DashboardLayout,
})

function DashboardLayout() {
  const { user } = Route.useLoaderData()

  return (
    <div>
      <header>Welcome, {user.name}!</header>
      <Outlet />
    </div>
  )
}

// Child routes can access parent data
// app/routes/dashboard/settings.tsx
function SettingsPage() {
  // Access parent loader data
  const parentData = Route.useParentLoaderData()
  const { user } = parentData

  return <div>Settings for {user.name}</div>
}
```

## Route Groups

Group routes logically without affecting URLs:

```
app/routes/
├── (marketing)/        → Group (no URL)
│   ├── index.tsx       → /
│   ├── about.tsx       → /about
│   └── pricing.tsx     → /pricing
└── (app)/              → Group (no URL)
    ├── dashboard.tsx   → /dashboard
    └── settings.tsx    → /settings
```

Groups help organize files but don't add URL segments.

## Conditional Layouts

Show different layouts based on conditions:

```typescript
// app/routes/__root.tsx
export const Route = createRootRoute({
  component: RootLayout,
})

function RootLayout() {
  const auth = useAuth()

  return (
    <html>
      <body>
        {auth.isAuthenticated ? (
          <AuthenticatedLayout>
            <Outlet />
          </AuthenticatedLayout>
        ) : (
          <GuestLayout>
            <Outlet />
          </GuestLayout>
        )}
      </body>
    </html>
  )
}
```

## Best Practices

### 1. Keep Layouts Simple

```typescript
// ✅ Good - Simple layout
function Layout() {
  return (
    <div>
      <Nav />
      <Outlet />
      <Footer />
    </div>
  )
}

// ❌ Avoid - Too much logic in layout
function Layout() {
  // Lots of state
  // Complex data fetching
  // Business logic
  // ...
}
```

### 2. Use Layouts for Shared UI

```typescript
// ✅ Good - Shared sidebar
function DashboardLayout() {
  return (
    <div className="flex">
      <Sidebar /> {/* Shared */}
      <Outlet />
    </div>
  )
}
```

### 3. Load Shared Data in Layouts

```typescript
// ✅ Good - Load user once in layout
export const Route = createFileRoute('/dashboard')({
  loader: async () => {
    const user = await fetchUser()
    return { user }
  },
  component: DashboardLayout,
})
```

### 4. Provide Breadcrumbs

```typescript
function DashboardLayout() {
  const matches = useMatches()

  return (
    <div>
      <nav>
        {matches.map(match => (
          <span key={match.id}>
            {match.pathname} /
          </span>
        ))}
      </nav>
      <Outlet />
    </div>
  )
}
```

## Exercises

### Exercise 1: Create a Blog Layout

1. Create a blog layout with sidebar
2. Add navigation
3. Create index and detail routes
4. Verify layout appears on both

### Exercise 2: Dashboard with Multiple Sections

1. Create dashboard layout
2. Add three child routes
3. Add navigation between them
4. Share user data

### Exercise 3: Nested Layouts

1. Create root layout
2. Add section layout
3. Add page routes
4. Verify nesting works

### Exercise 4: Layout Without URL Segment

1. Create `_layout.tsx`
2. Add child routes
3. Verify URL doesn't include layout

## Common Issues

### Issue 1: Missing Outlet

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
      <Outlet />
    </div>
  )
}
```

### Issue 2: Wrong File Structure

```
// ❌ Wrong
app/routes/
└── blog/
    └── layout.tsx  // Won't work as layout

// ✅ Correct
app/routes/
└── blog.tsx  // Layout file
└── blog/
    ├── index.tsx
    └── $postId.tsx
```

### Issue 3: Index Route Path

```typescript
// ❌ Wrong
export const Route = createFileRoute('/blog/index')({...})

// ✅ Correct
export const Route = createFileRoute('/blog/')({...})
```

Note the trailing slash for index routes!

## Summary

You now understand:

- Index routes for default paths
- Layout routes with `<Outlet />`
- Nested layouts and hierarchy
- Layout routes without URL segments
- Route groups
- Passing data through layouts
- Best practices

## Congratulations!

You've completed Module 4: Routing Fundamentals! You now know:
- File-based routing
- Route components
- Navigation and links
- Dynamic parameters
- Index routes and layouts

## Next Steps

Continue to [Module 5: Advanced Routing](../module-05-advanced-routing/README.md) to learn advanced routing techniques!

## Further Reading

- [TanStack Router Layouts](https://tanstack.com/router/latest/docs/layouts)
- [Route Groups](https://tanstack.com/router/latest/docs/route-groups)
- [Index Routes](https://tanstack.com/router/latest/docs/index-routes)
