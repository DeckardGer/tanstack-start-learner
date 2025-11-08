# Lesson 2: Creating Route Components

## Introduction

Route components are the building blocks of your application. In this lesson, you'll learn how to create route components with TanStack Router's powerful type-safe API.

## Basic Route Component Structure

Every route file exports a `Route` object created with `createFileRoute()`:

```typescript
// app/routes/about.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/about')({
  component: AboutPage,
})

function AboutPage() {
  return (
    <div>
      <h1>About Us</h1>
      <p>Welcome to our about page!</p>
    </div>
  )
}
```

## The `createFileRoute()` Function

```typescript
createFileRoute(path)(options)
```

**Parameters**:
- `path`: The route's URL path (must match the file location)
- `options`: Configuration object

**Options**:

```typescript
export const Route = createFileRoute('/example')({
  // Component to render
  component: Component,

  // Or lazy load it
  component: () => import('./component').then(m => m.Component),

  // Load data before rendering
  loader: async () => ({ data: 'value' }),

  // Validate search params
  validateSearch: (search) => search,

  // Error boundary
  errorComponent: ErrorComponent,

  // Pending component
  pendingComponent: LoadingComponent,

  // Before load hook
  beforeLoad: async () => { /* ... */ },

  // Context for child routes
  context: { /* ... */ },
})
```

## Component Definition

### Inline Component

```typescript
export const Route = createFileRoute('/about')({
  component: function AboutPage() {
    return <h1>About</h1>
  },
})
```

### Separate Component

```typescript
export const Route = createFileRoute('/about')({
  component: AboutPage,
})

function AboutPage() {
  return <h1>About</h1>
}
```

**Recommended**: Use separate components for better readability.

### Component with Props

Route components don't receive props directly. Access data through hooks:

```typescript
export const Route = createFileRoute('/profile')({
  component: ProfilePage,
})

function ProfilePage() {
  // Access route data via hooks
  const navigate = Route.useNavigate()
  const params = Route.useParams()
  const search = Route.useSearch()

  return <div>Profile Page</div>
}
```

## Using Route Hooks

### `Route.useParams()` - Access URL Parameters

```typescript
// app/routes/posts/$postId.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  component: PostPage,
})

function PostPage() {
  // Get the postId from URL
  const { postId } = Route.useParams()

  return <h1>Post: {postId}</h1>
}
```

**Fully Typed**:
```typescript
const { postId } = Route.useParams()
// postId is typed as string (not any!)
```

### `Route.useSearch()` - Access Search Parameters

```typescript
// app/routes/products/index.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/products/')({
  component: ProductsPage,
})

function ProductsPage() {
  // Get search params from URL
  const search = Route.useSearch()

  // URL: /products?category=electronics&sort=price
  // search = { category: 'electronics', sort: 'price' }

  return (
    <div>
      <h1>Products</h1>
      <p>Category: {search.category}</p>
      <p>Sort: {search.sort}</p>
    </div>
  )
}
```

### `Route.useNavigate()` - Programmatic Navigation

```typescript
export const Route = createFileRoute('/login')({
  component: LoginPage,
})

function LoginPage() {
  const navigate = Route.useNavigate()

  const handleLogin = async (credentials) => {
    await login(credentials)
    // Navigate after successful login
    navigate({ to: '/dashboard' })
  }

  return <form onSubmit={handleLogin}>...</form>
}
```

### `Route.useLoaderData()` - Access Loaded Data

```typescript
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    const post = await fetchPost(params.postId)
    return { post }
  },
  component: PostPage,
})

function PostPage() {
  // Access data from loader
  const { post } = Route.useLoaderData()

  return (
    <article>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </article>
  )
}
```

## Route Options

### Component Option

```typescript
export const Route = createFileRoute('/about')({
  component: AboutPage,
})
```

Or lazy load:

```typescript
export const Route = createFileRoute('/about')({
  component: () => import('./AboutPage').then(m => m.AboutPage),
})
```

### Loader Option

Load data before rendering:

```typescript
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    const post = await fetch(`/api/posts/${params.postId}`)
      .then(res => res.json())
    return { post }
  },
  component: PostPage,
})

function PostPage() {
  const { post } = Route.useLoaderData()
  return <div>{post.title}</div>
}
```

### Error Component Option

Handle errors gracefully:

```typescript
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    const post = await fetchPost(params.postId)
    if (!post) throw new Error('Post not found')
    return { post }
  },
  component: PostPage,
  errorComponent: ErrorComponent,
})

function ErrorComponent({ error }) {
  return (
    <div className="error">
      <h1>Error</h1>
      <p>{error.message}</p>
    </div>
  )
}
```

### Pending Component Option

Show loading state:

```typescript
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    const post = await fetchPost(params.postId)
    return { post }
  },
  component: PostPage,
  pendingComponent: () => (
    <div className="loading">
      <div className="spinner" />
      <p>Loading post...</p>
    </div>
  ),
})
```

### Before Load Hook

Run code before loading:

```typescript
export const Route = createFileRoute('/admin/dashboard')({
  beforeLoad: async ({ context }) => {
    // Check authentication
    if (!context.user) {
      throw redirect({ to: '/login' })
    }
  },
  component: AdminDashboard,
})
```

## Complete Example

Here's a fully-featured route component:

```typescript
// app/routes/posts/$postId.tsx
import { createFileRoute, ErrorComponent } from '@tanstack/react-router'

// Define types
interface Post {
  id: string
  title: string
  content: string
  author: string
  createdAt: string
}

// Create route
export const Route = createFileRoute('/posts/$postId')({
  // Load data
  loader: async ({ params, context }) => {
    const post = await fetch(`/api/posts/${params.postId}`)
      .then(res => {
        if (!res.ok) throw new Error('Post not found')
        return res.json()
      })

    return { post: post as Post }
  },

  // Main component
  component: PostPage,

  // Error handling
  errorComponent: PostErrorComponent,

  // Loading state
  pendingComponent: PostLoadingComponent,
})

// Loading component
function PostLoadingComponent() {
  return (
    <div className="flex items-center justify-center p-8">
      <div className="animate-spin h-8 w-8 border-4 border-blue-500 border-t-transparent rounded-full" />
    </div>
  )
}

// Error component
function PostErrorComponent({ error }: { error: Error }) {
  return (
    <div className="p-8 bg-red-50 border border-red-200 rounded">
      <h2 className="text-xl font-bold text-red-700">Error Loading Post</h2>
      <p className="text-red-600">{error.message}</p>
      <button
        onClick={() => window.location.reload()}
        className="mt-4 px-4 py-2 bg-red-600 text-white rounded hover:bg-red-700"
      >
        Try Again
      </button>
    </div>
  )
}

// Main component
function PostPage() {
  const { post } = Route.useLoaderData()
  const navigate = Route.useNavigate()

  return (
    <article className="max-w-4xl mx-auto p-8">
      <button
        onClick={() => navigate({ to: '/posts' })}
        className="mb-4 text-blue-600 hover:text-blue-800"
      >
        ← Back to posts
      </button>

      <header className="mb-8">
        <h1 className="text-4xl font-bold mb-2">{post.title}</h1>
        <div className="text-gray-600">
          By {post.author} • {new Date(post.createdAt).toLocaleDateString()}
        </div>
      </header>

      <div className="prose max-w-none">
        {post.content}
      </div>
    </article>
  )
}
```

## TypeScript Types

TanStack Router provides excellent type inference:

```typescript
// Types are automatically inferred!
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    // params.postId is typed as string
    const post = await fetchPost(params.postId)
    return { post }
  },
  component: PostPage,
})

function PostPage() {
  // Fully typed!
  const { post } = Route.useLoaderData()
  //     ^ Post type is inferred from loader

  const { postId } = Route.useParams()
  //       ^ string (inferred from route path)
}
```

### Explicit Types

You can also be explicit:

```typescript
interface PostLoaderData {
  post: Post
}

export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }): Promise<PostLoaderData> => {
    const post = await fetchPost(params.postId)
    return { post }
  },
  component: PostPage,
})
```

## Layout Routes with Outlets

Routes can render child routes using `<Outlet />`:

```typescript
// app/routes/dashboard.tsx
import { createFileRoute, Outlet } from '@tanstack/react-router'

export const Route = createFileRoute('/dashboard')({
  component: DashboardLayout,
})

function DashboardLayout() {
  return (
    <div className="dashboard">
      <aside className="sidebar">
        <nav>
          <a href="/dashboard">Overview</a>
          <a href="/dashboard/analytics">Analytics</a>
          <a href="/dashboard/settings">Settings</a>
        </nav>
      </aside>

      <main className="content">
        <Outlet /> {/* Child routes render here */}
      </main>
    </div>
  )
}
```

## Multiple Components in One File

You can export multiple helper components:

```typescript
// app/routes/products/index.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/products/')({
  component: ProductsPage,
})

function ProductsPage() {
  return (
    <div>
      <ProductsHeader />
      <ProductsList />
      <ProductsFooter />
    </div>
  )
}

// Helper components
function ProductsHeader() {
  return <header>Products</header>
}

function ProductsList() {
  return <div>Product list...</div>
}

function ProductsFooter() {
  return <footer>© 2024</footer>
}
```

## Common Patterns

### Pattern 1: Redirect After Action

```typescript
export const Route = createFileRoute('/posts/new')({
  component: NewPostPage,
})

function NewPostPage() {
  const navigate = Route.useNavigate()

  const handleSubmit = async (data) => {
    const post = await createPost(data)
    // Redirect to the new post
    navigate({ to: '/posts/$postId', params: { postId: post.id } })
  }

  return <PostForm onSubmit={handleSubmit} />
}
```

### Pattern 2: Conditional Rendering

```typescript
export const Route = createFileRoute('/profile')({
  loader: async ({ context }) => {
    return { user: context.user }
  },
  component: ProfilePage,
})

function ProfilePage() {
  const { user } = Route.useLoaderData()

  if (!user) {
    return <div>Please log in to view your profile</div>
  }

  return (
    <div>
      <h1>Welcome, {user.name}!</h1>
    </div>
  )
}
```

### Pattern 3: Data Dependencies

```typescript
export const Route = createFileRoute('/posts/$postId/comments')({
  loader: async ({ params }) => {
    // Load post and comments in parallel
    const [post, comments] = await Promise.all([
      fetchPost(params.postId),
      fetchComments(params.postId),
    ])

    return { post, comments }
  },
  component: PostCommentsPage,
})
```

## Exercises

### Exercise 1: Basic Route

Create a route at `/about` with:
- A heading
- A paragraph of text
- A link back to home

### Exercise 2: Dynamic Route

Create a user profile route at `/users/$userId`:
- Display the user ID
- Add a back button
- Style it nicely

### Exercise 3: Data Loading

Create a posts route at `/posts/$postId`:
- Load post data in loader
- Display the post
- Show loading state
- Handle errors

### Exercise 4: Layout Route

Create a dashboard layout at `/dashboard`:
- Add a sidebar
- Render child routes
- Add navigation links

## Common Issues

### Issue 1: Path Mismatch

```typescript
// ❌ Wrong - file at app/routes/about.tsx
export const Route = createFileRoute('/contact')({...})

// ✅ Correct
export const Route = createFileRoute('/about')({...})
```

Path must match file location!

### Issue 2: Missing Outlet

```typescript
// ❌ Wrong - child routes won't render
function Layout() {
  return <div>Layout content</div>
}

// ✅ Correct
function Layout() {
  return (
    <div>
      Layout content
      <Outlet />
    </div>
  )
}
```

### Issue 3: Forgetting to Export Route

```typescript
// ❌ Wrong
const Route = createFileRoute('/about')({...})

// ✅ Correct
export const Route = createFileRoute('/about')({...})
```

Must export the Route!

## Summary

You now understand:

- How to create route components
- Using `createFileRoute()`
- Route hooks (useParams, useSearch, etc.)
- Route options (loader, error, pending)
- TypeScript type safety
- Common patterns

## Next Steps

Continue to [Lesson 3: Navigation and Links](./lesson-03-navigation-links.md) to learn about navigating between routes.

## Further Reading

- [TanStack Router API Reference](https://tanstack.com/router/latest/docs/api)
- [Route Components Guide](https://tanstack.com/router/latest/docs/route-components)
- [Type Safety in TanStack Router](https://tanstack.com/router/latest/docs/type-safety)
