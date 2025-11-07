# Lesson 3: Cache Invalidation and Refetching

## Invalidating Queries

Mark queries as stale and refetch:

```typescript
// Invalidate all posts
queryClient.invalidateQueries({ queryKey: ['posts'] })

// Invalidate specific post
queryClient.invalidateQueries({ queryKey: ['posts', postId] })

// Invalidate multiple
queryClient.invalidateQueries({ queryKey: ['posts'] })
queryClient.invalidateQueries({ queryKey: ['users'] })
```

## Partial Invalidation

```typescript
// Invalidate all posts (including nested keys)
queryClient.invalidateQueries({ queryKey: ['posts'] })
// Invalidates: ['posts'], ['posts', '123'], ['posts', '123', 'comments']

// Exact match only
queryClient.invalidateQueries({ 
  queryKey: ['posts'],
  exact: true 
})
// Only invalidates: ['posts']
```

## After Mutations

```typescript
const createMutation = useMutation({
  mutationFn: createPost,
  onSuccess: () => {
    // Invalidate and refetch
    queryClient.invalidateQueries({ queryKey: ['posts'] })
  },
})
```

## Selective Refetching

```typescript
queryClient.invalidateQueries({
  queryKey: ['posts'],
  refetchType: 'active', // Only refetch active queries
})

// Options: 'active' | 'inactive' | 'all' | 'none'
```

## Removing Queries

```typescript
// Remove from cache completely
queryClient.removeQueries({ queryKey: ['posts', postId] })
```

## Reset Queries

```typescript
// Reset to initial state
queryClient.resetQueries({ queryKey: ['posts'] })
```

## Next Steps

Continue to [Lesson 4: Optimistic Updates](./lesson-04-optimistic-updates.md)
