# Lesson 2: Creating Your First Query

## Basic Query

```typescript
import { useQuery } from '@tanstack/react-query'

function Posts() {
  const { data, isLoading, error } = useQuery({
    queryKey: ['posts'],
    queryFn: async () => {
      const response = await fetch('/api/posts')
      if (!response.ok) throw new Error('Failed to fetch')
      return response.json()
    },
  })

  if (isLoading) return <div>Loading...</div>
  if (error) return <div>Error: {error.message}</div>

  return (
    <ul>
      {data.map(post => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  )
}
```

## Query with Route Loader

```typescript
// app/routes/posts/index.tsx
export const Route = createFileRoute('/posts/')({
  loader: ({ context: { queryClient } }) => {
    return queryClient.ensureQueryData({
      queryKey: ['posts'],
      queryFn: fetchPosts,
    })
  },
  component: PostsPage,
})

function PostsPage() {
  const { data: posts } = useQuery({
    queryKey: ['posts'],
    queryFn: fetchPosts,
  })

  return <div>{/* render posts */}</div>
}
```

## Query States

```typescript
const { 
  data,           // The data
  error,          // Error if any
  isLoading,      // Initial load
  isFetching,     // Any fetch (including background)
  isSuccess,      // Successfully fetched
  isError,        // Error occurred
  status,         // 'pending' | 'error' | 'success'
} = useQuery({...})
```

## Dependent Queries

```typescript
// Fetch user first
const { data: user } = useQuery({
  queryKey: ['user'],
  queryFn: fetchUser,
})

// Then fetch user's posts
const { data: posts } = useQuery({
  queryKey: ['posts', user?.id],
  queryFn: () => fetchUserPosts(user.id),
  enabled: !!user, // Only run if user exists
})
```

## Next Steps

Continue to [Lesson 3: Query Keys and Organization](./lesson-03-query-keys.md)
