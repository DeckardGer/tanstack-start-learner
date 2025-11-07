# Lesson 1: Introduction to TanStack Query

## What is TanStack Query?

TanStack Query (formerly React Query) is a powerful data fetching and caching library that makes server state management effortless.

## Why TanStack Query?

Without TanStack Query:
```typescript
function Posts() {
  const [posts, setPosts] = useState([])
  const [loading, setLoading] = useState(false)
  const [error, setError] = useState(null)

  useEffect(() => {
    setLoading(true)
    fetch('/api/posts')
      .then(res => res.json())
      .then(setPosts)
      .catch(setError)
      .finally(() => setLoading(false))
  }, [])

  if (loading) return <div>Loading...</div>
  if (error) return <div>Error</div>
  return <div>{/* render posts */}</div>
}
```

With TanStack Query:
```typescript
function Posts() {
  const { data: posts, isLoading, error } = useQuery({
    queryKey: ['posts'],
    queryFn: () => fetch('/api/posts').then(res => res.json()),
  })

  if (isLoading) return <div>Loading...</div>
  if (error) return <div>Error</div>
  return <div>{/* render posts */}</div>
}
```

## Key Features

1. **Automatic Caching** - Data is cached automatically
2. **Background Refetching** - Keep data fresh
3. **Deduplication** - Multiple requests combined
4. **Optimistic Updates** - Instant UI updates
5. **DevTools** - Visual debugging

## Installation

Already included in TanStack Start!

```bash
npm install @tanstack/react-query
```

## Setup QueryClient

```typescript
// app/router.tsx
import { QueryClient } from '@tanstack/react-query'

export function createRouter() {
  const queryClient = new QueryClient({
    defaultOptions: {
      queries: {
        staleTime: 1000 * 60, // 1 minute
      },
    },
  })

  return createTanStackRouter({
    routeTree,
    context: { queryClient },
  })
}
```

## Basic Concepts

### Queries
Fetch and cache data:
```typescript
const { data } = useQuery({
  queryKey: ['posts'],
  queryFn: fetchPosts,
})
```

### Mutations
Update data:
```typescript
const mutation = useMutation({
  mutationFn: createPost,
  onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: ['posts'] })
  },
})
```

### Query Keys
Unique identifiers for queries:
```typescript
['posts']              // All posts
['posts', postId]      // Specific post
['posts', { page: 1 }] // Paginated posts
```

## Next Steps

Continue to [Lesson 2: Creating Your First Query](./lesson-02-first-query.md)
