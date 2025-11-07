# Lesson 2: Caching Strategies and Configuration

## Cache Configuration

```typescript
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 1000 * 60 * 5,     // 5 minutes
      gcTime: 1000 * 60 * 30,       // 30 minutes
      retry: 3,                      // Retry failed requests
      refetchOnWindowFocus: true,    // Refetch on tab focus
      refetchOnReconnect: true,      // Refetch on reconnect
    },
  },
})
```

## Stale Time vs Cache Time

```typescript
// Stale time: How long data is considered fresh
staleTime: 1000 * 60 * 5  // 5 minutes

// Cache time: How long unused data stays in cache
gcTime: 1000 * 60 * 30    // 30 minutes
```

## Cache Strategies

### 1. Cache-First (Default)

```typescript
useQuery({
  queryKey: ['posts'],
  queryFn: fetchPosts,
  staleTime: 1000 * 60 * 5,
})
```

### 2. Network-First

```typescript
useQuery({
  queryKey: ['posts'],
  queryFn: fetchPosts,
  staleTime: 0, // Always stale
  gcTime: 0,    // Don't cache
})
```

### 3. Cache-Only

```typescript
useQuery({
  queryKey: ['posts'],
  queryFn: fetchPosts,
  staleTime: Infinity, // Never stale
})
```

## Setting Cache Data

```typescript
// Set data manually
queryClient.setQueryData(['posts', postId], newPost)

// Update existing data
queryClient.setQueryData(['posts', postId], (old) => ({
  ...old,
  title: 'Updated Title'
}))
```

## Getting Cache Data

```typescript
const post = queryClient.getQueryData(['posts', postId])
```

## Next Steps

Continue to [Lesson 3: Cache Invalidation](./lesson-03-invalidation.md)
