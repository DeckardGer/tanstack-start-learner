# Lesson 4: Optimistic Updates

## What are Optimistic Updates?

Update UI immediately before server responds.

## Basic Optimistic Update

```typescript
const updateMutation = useMutation({
  mutationFn: updatePost,
  onMutate: async (newPost) => {
    // Cancel outgoing refetches
    await queryClient.cancelQueries({ queryKey: ['posts', newPost.id] })

    // Snapshot previous value
    const previousPost = queryClient.getQueryData(['posts', newPost.id])

    // Optimistically update
    queryClient.setQueryData(['posts', newPost.id], newPost)

    // Return context with snapshot
    return { previousPost }
  },
  onError: (err, newPost, context) => {
    // Rollback on error
    queryClient.setQueryData(
      ['posts', newPost.id],
      context.previousPost
    )
  },
  onSettled: (newPost) => {
    // Refetch after mutation
    queryClient.invalidateQueries({ queryKey: ['posts', newPost.id] })
  },
})
```

## Optimistic List Update

```typescript
const createMutation = useMutation({
  mutationFn: createPost,
  onMutate: async (newPost) => {
    await queryClient.cancelQueries({ queryKey: ['posts'] })

    const previousPosts = queryClient.getQueryData(['posts'])

    // Add to list optimistically
    queryClient.setQueryData(['posts'], (old) => [
      { ...newPost, id: 'temp-id' },
      ...old
    ])

    return { previousPosts }
  },
  onError: (err, newPost, context) => {
    queryClient.setQueryData(['posts'], context.previousPosts)
  },
  onSuccess: (data) => {
    // Replace temp post with real one
    queryClient.setQueryData(['posts'], (old) =>
      old.map(post => post.id === 'temp-id' ? data : post)
    )
  },
})
```

## Next Steps

Continue to [Lesson 5: Query DevTools](./lesson-05-devtools.md)
