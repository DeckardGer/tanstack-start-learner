# Lesson 3: Navigation and Links

## Introduction

Navigation is a core part of any web application. TanStack Router provides powerful, type-safe navigation through the `Link` component and navigation hooks. Let's explore all the ways to navigate in your application.

## The `Link` Component

The `Link` component is the primary way to navigate between routes.

### Basic Usage

```typescript
import { Link } from '@tanstack/react-router'

function Navigation() {
  return (
    <nav>
      <Link to="/">Home</Link>
      <Link to="/about">About</Link>
      <Link to="/contact">Contact</Link>
    </nav>
  )
}
```

**Benefits over `<a>` tags**:
- Client-side navigation (no full page reload)
- Preloading support
- Active link styling
- Type-safe routes

## Type-Safe Links

TanStack Router knows all your routes:

```typescript
import { Link } from '@tanstack/react-router'

function Navigation() {
  return (
    <nav>
      {/* ✅ Valid routes - TypeScript knows these exist */}
      <Link to="/">Home</Link>
      <Link to="/about">About</Link>

      {/* ❌ TypeScript error - route doesn't exist */}
      <Link to="/nonexistent">Invalid</Link>
    </nav>
  )
}
```

You get **autocomplete** for all routes!

## Links with Parameters

### Dynamic Route Parameters

```typescript
// Link to /posts/123
<Link
  to="/posts/$postId"
  params={{ postId: '123' }}
>
  View Post
</Link>

// Link to /users/456/profile
<Link
  to="/users/$userId/profile"
  params={{ userId: '456' }}
>
  User Profile
</Link>
```

**Type Safety**: TypeScript enforces that you provide required params!

```typescript
// ❌ Error: Property 'postId' is missing
<Link to="/posts/$postId">View Post</Link>

// ✅ Correct
<Link to="/posts/$postId" params={{ postId: '123' }}>
  View Post
</Link>
```

### Multiple Parameters

```typescript
// Route: /products/$category/$productId
<Link
  to="/products/$category/$productId"
  params={{
    category: 'electronics',
    productId: 'laptop-123'
  }}
>
  View Product
</Link>
```

## Links with Search Parameters

### Adding Search Params

```typescript
<Link
  to="/products"
  search={{ category: 'electronics', sort: 'price' }}
>
  View Electronics
</Link>

// URL: /products?category=electronics&sort=price
```

### Combining Params and Search

```typescript
<Link
  to="/posts/$postId"
  params={{ postId: '123' }}
  search={{ comments: 'true' }}
>
  View Post with Comments
</Link>

// URL: /posts/123?comments=true
```

### Updating Search Params

Keep existing params while updating:

```typescript
import { Link } from '@tanstack/react-router'

function ProductFilters() {
  return (
    <div>
      {/* Keep existing search params, update sort */}
      <Link search={(prev) => ({ ...prev, sort: 'price' })}>
        Sort by Price
      </Link>

      <Link search={(prev) => ({ ...prev, sort: 'name' })}>
        Sort by Name
      </Link>
    </div>
  )
}
```

## Link Styling

### Active Link Styling

Automatically style the active link:

```typescript
import { Link } from '@tanstack/react-router'

function Navigation() {
  return (
    <nav>
      <Link
        to="/"
        activeOptions={{ exact: true }}
        activeProps={{ className: 'font-bold text-blue-600' }}
        inactiveProps={{ className: 'text-gray-600' }}
      >
        Home
      </Link>

      <Link
        to="/about"
        activeProps={{ className: 'font-bold text-blue-600' }}
      >
        About
      </Link>
    </nav>
  )
}
```

**Options**:
- `activeProps`: Props when link is active
- `inactiveProps`: Props when link is inactive
- `activeOptions`: Configure what "active" means

### Custom Active Check

```typescript
<Link
  to="/blog"
  activeOptions={{
    exact: false,  // Match /blog and /blog/*
    includeSearch: false,  // Ignore search params
  }}
  activeProps={{
    className: 'active-link'
  }}
>
  Blog
</Link>
```

### Using CSS Classes

```typescript
<Link
  to="/dashboard"
  className="nav-link"
  activeProps={{
    className: 'nav-link nav-link-active'
  }}
>
  Dashboard
</Link>
```

```css
.nav-link {
  color: #666;
  text-decoration: none;
}

.nav-link-active {
  color: #0066cc;
  font-weight: bold;
  border-bottom: 2px solid #0066cc;
}
```

### Using Inline Styles

```typescript
<Link
  to="/profile"
  activeProps={{
    style: {
      color: 'blue',
      fontWeight: 'bold'
    }
  }}
>
  Profile
</Link>
```

## Link Preloading

Preload route data for faster navigation.

### Preload on Intent (Default)

```typescript
<Link
  to="/posts/$postId"
  params={{ postId: '123' }}
  preload="intent"  // Preload on hover/focus
>
  View Post
</Link>
```

**Preload strategies**:
- `intent`: Preload on hover or focus (default)
- `render`: Preload when link renders
- `viewport`: Preload when link enters viewport
- `false`: Don't preload

### Preload Delay

```typescript
<Link
  to="/posts/$postId"
  params={{ postId: '123' }}
  preload="intent"
  preloadDelay={100}  // Wait 100ms before preloading
>
  View Post
</Link>
```

### Example: Instant Navigation

```typescript
function PostList({ posts }) {
  return (
    <div>
      {posts.map(post => (
        <Link
          key={post.id}
          to="/posts/$postId"
          params={{ postId: post.id }}
          preload="intent"  // Preload on hover
          className="block p-4 hover:bg-gray-100"
        >
          <h3>{post.title}</h3>
          <p>{post.excerpt}</p>
        </Link>
      ))}
    </div>
  )
}
```

## Programmatic Navigation

Navigate with code using hooks.

### `useNavigate()` Hook

```typescript
import { useNavigate } from '@tanstack/react-router'

function LoginForm() {
  const navigate = useNavigate()

  const handleLogin = async (credentials) => {
    await login(credentials)

    // Navigate to dashboard
    navigate({ to: '/dashboard' })
  }

  return <form onSubmit={handleLogin}>...</form>
}
```

### Navigate with Params

```typescript
function CreatePostForm() {
  const navigate = useNavigate()

  const handleSubmit = async (data) => {
    const post = await createPost(data)

    // Navigate to the new post
    navigate({
      to: '/posts/$postId',
      params: { postId: post.id }
    })
  }

  return <form onSubmit={handleSubmit}>...</form>
}
```

### Navigate with Search Params

```typescript
function ProductFilters() {
  const navigate = useNavigate()

  const applyFilters = (filters) => {
    navigate({
      to: '/products',
      search: filters
    })
  }

  return (
    <button onClick={() => applyFilters({ category: 'electronics' })}>
      View Electronics
    </button>
  )
}
```

### Navigate with Options

```typescript
const navigate = useNavigate()

// Replace current history entry (no back button)
navigate({
  to: '/dashboard',
  replace: true
})

// Reset scroll position
navigate({
  to: '/about',
  resetScroll: true
})

// Keep existing search params
navigate({
  to: '/products',
  search: (prev) => ({ ...prev, page: 2 })
})
```

## Route-Specific Navigation

Use `Route.useNavigate()` for type-safe navigation within a route:

```typescript
// app/routes/posts/$postId.tsx
export const Route = createFileRoute('/posts/$postId')({
  component: PostPage,
})

function PostPage() {
  const navigate = Route.useNavigate()

  const goBack = () => {
    navigate({ to: '/posts' })
  }

  return (
    <button onClick={goBack}>
      Back to Posts
    </button>
  )
}
```

## Back/Forward Navigation

```typescript
import { useRouter } from '@tanstack/react-router'

function Navigation() {
  const router = useRouter()

  return (
    <div>
      <button onClick={() => router.history.back()}>
        ← Back
      </button>

      <button onClick={() => router.history.forward()}>
        Forward →
      </button>
    </div>
  )
}
```

## External Links

For external URLs, use regular `<a>` tags:

```typescript
function ExternalLinks() {
  return (
    <nav>
      {/* Internal routes - use Link */}
      <Link to="/about">About</Link>

      {/* External links - use <a> */}
      <a href="https://example.com" target="_blank" rel="noopener noreferrer">
        External Site
      </a>
    </nav>
  )
}
```

## Complete Navigation Example

```typescript
// app/routes/posts/index.tsx
import { createFileRoute, Link, useNavigate } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/')({
  loader: async () => {
    const posts = await fetchPosts()
    return { posts }
  },
  component: PostsPage,
})

function PostsPage() {
  const { posts } = Route.useLoaderData()
  const navigate = useNavigate()

  const handleCreateNew = () => {
    navigate({ to: '/posts/new' })
  }

  return (
    <div className="max-w-4xl mx-auto p-8">
      <header className="flex justify-between items-center mb-8">
        <h1 className="text-3xl font-bold">Blog Posts</h1>

        <button
          onClick={handleCreateNew}
          className="px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700"
        >
          Create New Post
        </button>
      </header>

      {/* Filters */}
      <div className="mb-6 space-x-2">
        <Link
          to="/posts"
          search={{ category: 'all' }}
          activeProps={{ className: 'font-bold text-blue-600' }}
        >
          All
        </Link>
        <Link
          to="/posts"
          search={{ category: 'tutorials' }}
          activeProps={{ className: 'font-bold text-blue-600' }}
        >
          Tutorials
        </Link>
        <Link
          to="/posts"
          search={{ category: 'news' }}
          activeProps={{ className: 'font-bold text-blue-600' }}
        >
          News
        </Link>
      </div>

      {/* Posts list */}
      <div className="space-y-4">
        {posts.map(post => (
          <Link
            key={post.id}
            to="/posts/$postId"
            params={{ postId: post.id }}
            preload="intent"
            className="block p-6 bg-white border rounded-lg hover:shadow-lg transition-shadow"
          >
            <h2 className="text-xl font-semibold mb-2">{post.title}</h2>
            <p className="text-gray-600 mb-2">{post.excerpt}</p>
            <div className="text-sm text-gray-500">
              {post.author} • {new Date(post.createdAt).toLocaleDateString()}
            </div>
          </Link>
        ))}
      </div>
    </div>
  )
}
```

## Navigation Guards

Prevent navigation or redirect:

```typescript
// app/routes/admin/dashboard.tsx
export const Route = createFileRoute('/admin/dashboard')({
  beforeLoad: async ({ context, location }) => {
    // Check if user is authenticated
    if (!context.user) {
      throw redirect({
        to: '/login',
        search: {
          redirect: location.href,
        },
      })
    }

    // Check if user is admin
    if (!context.user.isAdmin) {
      throw redirect({ to: '/' })
    }
  },
  component: AdminDashboard,
})
```

## Scroll Behavior

Control scroll position on navigation:

```typescript
// Reset scroll to top
<Link
  to="/about"
  resetScroll={true}
>
  About
</Link>

// Maintain scroll position
<Link
  to="/blog"
  resetScroll={false}
>
  Blog
</Link>

// Programmatic
navigate({
  to: '/about',
  resetScroll: true
})
```

## Navigation Best Practices

### 1. Use Links for Navigation

```typescript
// ✅ Good - Use Link component
<Link to="/about">About</Link>

// ❌ Avoid - Direct href causes full page reload
<a href="/about">About</a>
```

### 2. Preload Important Routes

```typescript
// Preload on hover for instant navigation
<Link to="/posts/$postId" params={{ postId }} preload="intent">
  {post.title}
</Link>
```

### 3. Handle Loading States

```typescript
function NavigationButton() {
  const [isNavigating, setIsNavigating] = useState(false)
  const navigate = useNavigate()

  const handleClick = async () => {
    setIsNavigating(true)
    await navigate({ to: '/slow-route' })
    setIsNavigating(false)
  }

  return (
    <button onClick={handleClick} disabled={isNavigating}>
      {isNavigating ? 'Loading...' : 'Go'}
    </button>
  )
}
```

### 4. Provide Visual Feedback

```typescript
<Link
  to="/dashboard"
  activeProps={{
    className: 'font-bold text-blue-600 border-b-2 border-blue-600'
  }}
  className="px-4 py-2 hover:text-blue-500 transition-colors"
>
  Dashboard
</Link>
```

## Exercises

### Exercise 1: Basic Navigation

Create a navigation menu with:
- Links to Home, About, Contact
- Active link styling
- Hover effects

### Exercise 2: Dynamic Links

Create a product list with:
- Links to individual products
- Preloading enabled
- Click to view details

### Exercise 3: Search Params

Create a products page with:
- Category filter links
- Sort options
- URL reflects current filters

### Exercise 4: Programmatic Navigation

Create a login form that:
- Navigates to dashboard on success
- Handles errors
- Shows loading state

## Common Issues

### Issue 1: Full Page Reload

```typescript
// ❌ Wrong - causes full reload
<a href="/about">About</a>

// ✅ Correct - client-side navigation
<Link to="/about">About</Link>
```

### Issue 2: Missing Required Params

```typescript
// ❌ Error - postId required
<Link to="/posts/$postId">View</Link>

// ✅ Correct
<Link to="/posts/$postId" params={{ postId: '123' }}>
  View
</Link>
```

### Issue 3: Incorrect Active Matching

```typescript
// ❌ Wrong - "/" matches all routes
<Link to="/" activeProps={{...}}>Home</Link>

// ✅ Correct - use exact matching
<Link to="/" activeOptions={{ exact: true }} activeProps={{...}}>
  Home
</Link>
```

## Summary

You now understand:

- Using the Link component
- Type-safe navigation
- Links with parameters and search
- Active link styling
- Preloading strategies
- Programmatic navigation
- Navigation guards
- Best practices

## Next Steps

Continue to [Lesson 4: Route Parameters and Dynamic Routes](./lesson-04-route-parameters.md) to master dynamic routing.

## Further Reading

- [TanStack Router Link API](https://tanstack.com/router/latest/docs/api/router-link)
- [Navigation Guide](https://tanstack.com/router/latest/docs/navigation)
- [Preloading](https://tanstack.com/router/latest/docs/preloading)
