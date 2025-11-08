# Lesson 1: Type-Safe Routing

## Introduction

TanStack Router provides exceptional type safety, giving you autocomplete for routes, parameters, and search params throughout your application. This eliminates a whole class of bugs and makes refactoring safer.

## Route Type Safety

### Automatic Route Types

TanStack Router automatically generates types for all your routes:

```typescript
import { Link } from '@tanstack/react-router'

// TypeScript knows all valid routes!
<Link to="/">Home</Link>                    // ✅
<Link to="/about">About</Link>              // ✅
<Link to="/posts/$postId" params={{...}}>   // ✅
<Link to="/invalid">Invalid</Link>          // ❌ Type error!
```

### Type-Safe Parameters

Parameters are fully typed:

```typescript
// app/routes/posts/$postId.tsx
export const Route = createFileRoute('/posts/$postId')({
  component: PostPage,
})

function PostPage() {
  const { postId } = Route.useParams()
  //     ^? string - TypeScript knows this!
  
  return <div>Post: {postId}</div>
}
```

### Type-Safe Search Params

```typescript
import { z } from 'zod'

const searchSchema = z.object({
  page: z.number().optional(),
  category: z.string().optional(),
})

export const Route = createFileRoute('/products/')({
  validateSearch: searchSchema,
  component: ProductsPage,
})

function ProductsPage() {
  const search = Route.useSearch()
  //    ^? { page?: number; category?: string }
  
  return <div>Page: {search.page}</div>
}
```

## Type Registration

Register your router for global type safety:

```typescript
// app/router.tsx
import { createRouter } from '@tanstack/react-router'
import { routeTree } from './routeTree.gen'

export function createRouter() {
  return createTanStackRouter({
    routeTree,
  })
}

// Register for type safety
declare module '@tanstack/react-router' {
  interface Register {
    router: ReturnType<typeof createRouter>
  }
}
```

Now all router hooks are typed throughout your app!

## Type-Safe Navigation

```typescript
import { useNavigate } from '@tanstack/react-router'

function MyComponent() {
  const navigate = useNavigate()

  const goToPost = (id: string) => {
    // Fully typed navigation
    navigate({
      to: '/posts/$postId',
      params: { postId: id },  // Required params enforced
      search: { tab: 'comments' }  // Optional
    })
  }

  return <button onClick={() => goToPost('123')}>View Post</button>
}
```

## Parameter Parsing with Types

```typescript
import { z } from 'zod'

const paramsSchema = z.object({
  postId: z.string().uuid(),
})

export const Route = createFileRoute('/posts/$postId')({
  parseParams: (params) => paramsSchema.parse(params),
  component: PostPage,
})

function PostPage() {
  const { postId } = Route.useParams()
  // postId is validated as UUID at runtime
  // and typed as string at compile time
}
```

## Loader Data Types

Loader return types are automatically inferred:

```typescript
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    const post = await fetchPost(params.postId)
    const comments = await fetchComments(params.postId)
    
    return { post, comments }
  },
  component: PostPage,
})

function PostPage() {
  const data = Route.useLoaderData()
  //    ^? { post: Post; comments: Comment[] }
  // Fully typed from loader return!
}
```

## Summary

Type-safe routing eliminates common bugs and provides excellent developer experience with autocomplete and compile-time checking.

## Next Steps

Continue to [Lesson 2: Route Loaders and Data Loading](./lesson-02-route-loaders.md)
