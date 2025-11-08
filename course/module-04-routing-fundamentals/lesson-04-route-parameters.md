# Lesson 4: Route Parameters and Dynamic Routes

## Introduction

Dynamic routes allow you to create one route component that handles many URLs. Instead of creating separate files for every blog post or user profile, you use route parameters. Let's master dynamic routing in TanStack Start.

## What Are Route Parameters?

Route parameters are dynamic segments in your URL:

```
/posts/123          ← "123" is a parameter
/posts/hello-world  ← "hello-world" is a parameter
/users/alice        ← "alice" is a parameter
```

One route file handles all these URLs!

## Creating Dynamic Routes

Use `$` prefix in the filename:

```
app/routes/
├── posts/
│   └── $postId.tsx    → /posts/:postId
```

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

## Accessing Parameters

### Using `Route.useParams()`

```typescript
// app/routes/posts/$postId.tsx
export const Route = createFileRoute('/posts/$postId')({
  component: PostPage,
})

function PostPage() {
  // Get parameter from URL
  const { postId } = Route.useParams()

  return <div>Viewing post: {postId}</div>
}
```

**Fully typed**:
```typescript
const { postId } = Route.useParams()
// postId is string (TypeScript knows this!)
```

### Multiple Parameters

```typescript
// app/routes/users/$userId/posts/$postId.tsx
export const Route = createFileRoute('/users/$userId/posts/$postId')({
  component: UserPostPage,
})

function UserPostPage() {
  const { userId, postId } = Route.useParams()

  return (
    <div>
      <p>User: {userId}</p>
      <p>Post: {postId}</p>
    </div>
  )
}
```

**URL**: `/users/alice/posts/123`
- `userId = "alice"`
- `postId = "123"`

## Parameters in Loaders

Use parameters to fetch data:

```typescript
// app/routes/posts/$postId.tsx
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    // params is fully typed!
    const post = await fetchPost(params.postId)
    return { post }
  },
  component: PostPage,
})

function PostPage() {
  const { post } = Route.useLoaderData()

  return (
    <article>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </article>
  )
}
```

## Parameter Validation

### Runtime Validation with Zod

```typescript
// app/routes/posts/$postId.tsx
import { z } from 'zod'
import { createFileRoute } from '@tanstack/react-router'

const postIdSchema = z.string().regex(/^\d+$/, 'Post ID must be numeric')

export const Route = createFileRoute('/posts/$postId')({
  parseParams: (params) => ({
    postId: postIdSchema.parse(params.postId),
  }),
  loader: async ({ params }) => {
    // postId is validated and guaranteed to be numeric
    const post = await fetchPost(params.postId)
    return { post }
  },
  component: PostPage,
})
```

### Custom Validation

```typescript
export const Route = createFileRoute('/posts/$postId')({
  parseParams: (params) => {
    const postId = params.postId

    // Custom validation
    if (!/^\d+$/.test(postId)) {
      throw new Error('Invalid post ID')
    }

    return { postId }
  },
  component: PostPage,
})
```

## Parameter Types

### String Parameters (Default)

```typescript
// app/routes/posts/$postId.tsx
const { postId } = Route.useParams()
// postId is string
```

### Numeric Parameters

```typescript
// app/routes/posts/$postId.tsx
import { z } from 'zod'

export const Route = createFileRoute('/posts/$postId')({
  parseParams: (params) => ({
    postId: z.number().parse(Number(params.postId)),
  }),
  component: PostPage,
})

function PostPage() {
  const { postId } = Route.useParams()
  // postId is now number!

  return <div>Post #{postId}</div>
}
```

### Enum Parameters

```typescript
// app/routes/products/$category.tsx
import { z } from 'zod'

const categorySchema = z.enum(['electronics', 'clothing', 'books'])

export const Route = createFileRoute('/products/$category')({
  parseParams: (params) => ({
    category: categorySchema.parse(params.category),
  }),
  component: ProductsPage,
})

function ProductsPage() {
  const { category } = Route.useParams()
  // category is 'electronics' | 'clothing' | 'books'

  return <h1>{category}</h1>
}
```

## Nested Dynamic Routes

### Multiple Levels

```
app/routes/
├── users/
│   └── $userId/
│       ├── index.tsx         → /users/:userId
│       ├── profile.tsx       → /users/:userId/profile
│       ├── settings.tsx      → /users/:userId/settings
│       └── posts/
│           ├── index.tsx     → /users/:userId/posts
│           └── $postId.tsx   → /users/:userId/posts/:postId
```

```typescript
// app/routes/users/$userId/posts/$postId.tsx
export const Route = createFileRoute('/users/$userId/posts/$postId')({
  loader: async ({ params }) => {
    const { userId, postId } = params

    const [user, post] = await Promise.all([
      fetchUser(userId),
      fetchPost(postId),
    ])

    return { user, post }
  },
  component: UserPostPage,
})

function UserPostPage() {
  const { user, post } = Route.useLoaderData()

  return (
    <article>
      <h1>{post.title}</h1>
      <p>By {user.name}</p>
      <p>{post.content}</p>
    </article>
  )
}
```

## Optional Parameters

Use a layout with optional segments:

```
app/routes/
├── blog/
│   ├── index.tsx                → /blog
│   ├── $category.tsx            → /blog/:category
│   └── $category/$postSlug.tsx  → /blog/:category/:postSlug
```

Or use catch-all:

```typescript
// app/routes/docs/$.tsx
export const Route = createFileRoute('/docs/$')({
  component: DocsPage,
})

function DocsPage() {
  const { _splat } = Route.useParams()

  // _splat contains everything after /docs/
  // /docs/getting-started → "getting-started"
  // /docs/api/reference → "api/reference"
  // /docs → "" (empty string)

  return <div>Docs: {_splat || 'Home'}</div>
}
```

## Linking to Dynamic Routes

### Basic Link

```typescript
import { Link } from '@tanstack/react-router'

<Link
  to="/posts/$postId"
  params={{ postId: '123' }}
>
  View Post
</Link>
```

### Dynamic Link from Data

```typescript
function PostsList({ posts }) {
  return (
    <div>
      {posts.map(post => (
        <Link
          key={post.id}
          to="/posts/$postId"
          params={{ postId: post.id }}
        >
          {post.title}
        </Link>
      ))}
    </div>
  )
}
```

### Multiple Parameters

```typescript
<Link
  to="/users/$userId/posts/$postId"
  params={{
    userId: '456',
    postId: '123'
  }}
>
  View User's Post
</Link>
```

## Programmatic Navigation

```typescript
function CreatePost() {
  const navigate = Route.useNavigate()

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

## Complete Example: Blog Post

```typescript
// app/routes/posts/$postId.tsx
import { createFileRoute, Link, ErrorComponent } from '@tanstack/react-router'
import { z } from 'zod'

// Validate postId is numeric
const postIdSchema = z.string().regex(/^\d+$/)

// Type for our post
interface Post {
  id: string
  title: string
  content: string
  author: {
    id: string
    name: string
  }
  createdAt: string
  tags: string[]
}

export const Route = createFileRoute('/posts/$postId')({
  // Validate parameters
  parseParams: (params) => ({
    postId: postIdSchema.parse(params.postId),
  }),

  // Load post data
  loader: async ({ params }) => {
    const response = await fetch(`/api/posts/${params.postId}`)

    if (!response.ok) {
      if (response.status === 404) {
        throw new Error('Post not found')
      }
      throw new Error('Failed to load post')
    }

    const post: Post = await response.json()
    return { post }
  },

  // Main component
  component: PostPage,

  // Error handling
  errorComponent: ({ error }) => (
    <div className="p-8 bg-red-50 border border-red-200 rounded">
      <h1 className="text-xl font-bold text-red-700">Error</h1>
      <p className="text-red-600">{error.message}</p>
      <Link
        to="/posts"
        className="mt-4 inline-block text-blue-600 hover:underline"
      >
        ← Back to posts
      </Link>
    </div>
  ),

  // Loading state
  pendingComponent: () => (
    <div className="flex items-center justify-center p-8">
      <div className="animate-spin h-8 w-8 border-4 border-blue-500 border-t-transparent rounded-full" />
    </div>
  ),
})

function PostPage() {
  const { post } = Route.useLoaderData()
  const { postId } = Route.useParams()

  return (
    <article className="max-w-4xl mx-auto p-8">
      {/* Back button */}
      <Link
        to="/posts"
        className="mb-4 inline-block text-blue-600 hover:underline"
      >
        ← Back to posts
      </Link>

      {/* Post header */}
      <header className="mb-8">
        <h1 className="text-4xl font-bold mb-2">{post.title}</h1>

        <div className="flex items-center gap-4 text-gray-600">
          <Link
            to="/users/$userId"
            params={{ userId: post.author.id }}
            className="hover:text-blue-600"
          >
            {post.author.name}
          </Link>

          <span>•</span>

          <time dateTime={post.createdAt}>
            {new Date(post.createdAt).toLocaleDateString()}
          </time>
        </div>

        {/* Tags */}
        <div className="flex gap-2 mt-4">
          {post.tags.map(tag => (
            <Link
              key={tag}
              to="/posts"
              search={{ tag }}
              className="px-3 py-1 bg-gray-200 rounded-full text-sm hover:bg-gray-300"
            >
              {tag}
            </Link>
          ))}
        </div>
      </header>

      {/* Post content */}
      <div className="prose max-w-none">
        {post.content}
      </div>

      {/* Actions */}
      <footer className="mt-8 pt-8 border-t">
        <div className="flex gap-4">
          <Link
            to="/posts/$postId/edit"
            params={{ postId }}
            className="px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700"
          >
            Edit Post
          </Link>

          <button className="px-4 py-2 bg-red-600 text-white rounded hover:bg-red-700">
            Delete Post
          </button>
        </div>
      </footer>
    </article>
  )
}
```

## Catch-All Routes

### Basic Catch-All

```typescript
// app/routes/docs/$.tsx
export const Route = createFileRoute('/docs/$')({
  component: DocsPage,
})

function DocsPage() {
  const { _splat } = Route.useParams()

  // Parse the path
  const segments = _splat?.split('/').filter(Boolean) || []

  return (
    <div>
      <h1>Documentation</h1>
      <p>Path: {segments.join(' > ')}</p>
    </div>
  )
}
```

**Matches**:
- `/docs/getting-started` → `_splat = "getting-started"`
- `/docs/api/reference` → `_splat = "api/reference"`
- `/docs/guides/routing/basics` → `_splat = "guides/routing/basics"`

### With Data Loading

```typescript
// app/routes/docs/$.tsx
export const Route = createFileRoute('/docs/$')({
  loader: async ({ params }) => {
    const path = params._splat || 'index'

    // Load markdown file based on path
    const content = await fetchDocContent(path)

    return { content, path }
  },
  component: DocsPage,
})

function DocsPage() {
  const { content, path } = Route.useLoaderData()

  return (
    <div>
      <h1>Documentation: {path}</h1>
      <div dangerouslySetInnerHTML={{ __html: content }} />
    </div>
  )
}
```

## Best Practices

### 1. Validate Parameters

Always validate parameters, especially if using them in API calls:

```typescript
import { z } from 'zod'

const paramsSchema = z.object({
  postId: z.string().uuid(),
})

export const Route = createFileRoute('/posts/$postId')({
  parseParams: (params) => paramsSchema.parse(params),
  component: PostPage,
})
```

### 2. Handle Not Found

```typescript
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    const post = await fetchPost(params.postId)

    if (!post) {
      throw new Error('Post not found')
    }

    return { post }
  },
  errorComponent: PostNotFound,
})

function PostNotFound() {
  return (
    <div>
      <h1>Post Not Found</h1>
      <Link to="/posts">View all posts</Link>
    </div>
  )
}
```

### 3. Type Your Data

```typescript
interface Post {
  id: string
  title: string
  content: string
}

export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }): Promise<{ post: Post }> => {
    const post = await fetchPost(params.postId)
    return { post }
  },
  component: PostPage,
})
```

### 4. Preload Related Routes

```typescript
function PostsList({ posts }) {
  return (
    <div>
      {posts.map(post => (
        <Link
          key={post.id}
          to="/posts/$postId"
          params={{ postId: post.id }}
          preload="intent"  // Preload on hover
        >
          {post.title}
        </Link>
      ))}
    </div>
  )
}
```

## Exercises

### Exercise 1: User Profile

Create a route at `/users/$userId` that:
- Displays user information
- Validates userId
- Handles user not found

### Exercise 2: Blog with Categories

Create routes for:
- `/blog/$category` - List posts in category
- `/blog/$category/$postSlug` - Single post
- Validate category is valid

### Exercise 3: Nested Resources

Create:
- `/projects/$projectId` - Project details
- `/projects/$projectId/tasks/$taskId` - Task details
- Both parameters validated

### Exercise 4: Docs with Catch-All

Create `/docs/$` that:
- Handles any path depth
- Loads content based on path
- Shows breadcrumbs

## Common Issues

### Issue 1: Wrong Parameter Name

```typescript
// ❌ File: app/routes/posts/$postId.tsx
const { id } = Route.useParams()  // Wrong!

// ✅ Correct
const { postId } = Route.useParams()  // Matches filename
```

### Issue 2: Missing $ Prefix

```
// ❌ Wrong
app/routes/posts/postId.tsx  // Static route

// ✅ Correct
app/routes/posts/$postId.tsx  // Dynamic route
```

### Issue 3: Not Validating Parameters

```typescript
// ❌ Dangerous - no validation
loader: async ({ params }) => {
  await fetchPost(params.postId)  // Could be anything!
}

// ✅ Safe - validated
parseParams: (params) => ({
  postId: z.string().uuid().parse(params.postId)
})
```

## Summary

You now understand:

- Creating dynamic routes with `$` prefix
- Accessing parameters with `useParams()`
- Multiple and nested parameters
- Parameter validation
- Catch-all routes
- Linking to dynamic routes
- Best practices for dynamic routing

## Next Steps

Continue to [Lesson 5: Index Routes and Layouts](./lesson-05-index-routes-layouts.md) to master route organization.

## Further Reading

- [TanStack Router Params](https://tanstack.com/router/latest/docs/params)
- [Route Validation](https://tanstack.com/router/latest/docs/validation)
- [Dynamic Routes Guide](https://tanstack.com/router/latest/docs/dynamic-routes)
