# Lesson 3: Query Keys and Organization

## Query Key Structure

Query keys uniquely identify queries:

```typescript
// Simple key
['posts']

// With ID
['posts', postId]

// With filters
['posts', { category: 'tech', page: 1 }]

// Nested structure
['users', userId, 'posts', { status: 'published' }]
```

## Query Key Best Practices

### 1. Use Array Format

```typescript
// ✅ Good
queryKey: ['posts', postId]

// ❌ Avoid
queryKey: `posts-${postId}`
```

### 2. Hierarchical Keys

```typescript
['posts']                          // All posts
['posts', postId]                  // Single post
['posts', postId, 'comments']      // Post comments
```

### 3. Query Key Factory

```typescript
// lib/queryKeys.ts
export const queryKeys = {
  posts: {
    all: ['posts'] as const,
    lists: () => [...queryKeys.posts.all, 'list'] as const,
    list: (filters: string) => [...queryKeys.posts.lists(), filters] as const,
    details: () => [...queryKeys.posts.all, 'detail'] as const,
    detail: (id: string) => [...queryKeys.posts.details(), id] as const,
  },
  users: {
    all: ['users'] as const,
    detail: (id: string) => [...queryKeys.users.all, id] as const,
  },
}

// Usage
useQuery({
  queryKey: queryKeys.posts.detail(postId),
  queryFn: () => fetchPost(postId),
})
```

## Invalidating Queries

```typescript
// Invalidate all posts
queryClient.invalidateQueries({ queryKey: ['posts'] })

// Invalidate specific post
queryClient.invalidateQueries({ queryKey: ['posts', postId] })

// Invalidate with filters
queryClient.invalidateQueries({
  queryKey: ['posts'],
  predicate: (query) => query.queryKey[1] === 'published',
})
```

## Next Steps

Continue to [Lesson 4: Loading, Error, and Success States](./lesson-04-loading-states.md)
