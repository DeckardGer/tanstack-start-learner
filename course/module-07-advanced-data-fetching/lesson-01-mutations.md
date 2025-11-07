# Lesson 1: Mutations and Data Updates

## What are Mutations?

Mutations are used to create, update, or delete data.

## Basic Mutation

```typescript
import { useMutation, useQueryClient } from '@tanstack/react-query'

function CreatePost() {
  const queryClient = useQueryClient()

  const mutation = useMutation({
    mutationFn: (newPost) => {
      return fetch('/api/posts', {
        method: 'POST',
        body: JSON.stringify(newPost),
      }).then(res => res.json())
    },
    onSuccess: () => {
      // Invalidate and refetch
      queryClient.invalidateQueries({ queryKey: ['posts'] })
    },
  })

  const handleSubmit = (data) => {
    mutation.mutate(data)
  }

  return (
    <form onSubmit={handleSubmit}>
      {mutation.isPending && <div>Creating...</div>}
      {mutation.isError && <div>Error: {mutation.error.message}</div>}
      {mutation.isSuccess && <div>Post created!</div>}
      <button type="submit">Create</button>
    </form>
  )
}
```

## Mutation States

```typescript
const mutation = useMutation({...})

mutation.isPending    // Mutation in progress
mutation.isError      // Error occurred
mutation.isSuccess    // Success
mutation.data         // Response data
mutation.error        // Error object
```

## Mutation Callbacks

```typescript
const mutation = useMutation({
  mutationFn: createPost,
  onMutate: async (variables) => {
    // Before mutation starts
    console.log('Creating post...', variables)
  },
  onSuccess: (data, variables, context) => {
    // On successful mutation
    console.log('Post created!', data)
  },
  onError: (error, variables, context) => {
    // On error
    console.error('Error:', error)
  },
  onSettled: (data, error, variables, context) => {
    // Always runs (success or error)
    queryClient.invalidateQueries({ queryKey: ['posts'] })
  },
})
```

## Update Mutation

```typescript
const updateMutation = useMutation({
  mutationFn: ({ id, data }) => {
    return fetch(`/api/posts/${id}`, {
      method: 'PUT',
      body: JSON.stringify(data),
    }).then(res => res.json())
  },
  onSuccess: (data, variables) => {
    // Update specific post in cache
    queryClient.setQueryData(['posts', variables.id], data)
  },
})

// Usage
updateMutation.mutate({
  id: '123',
  data: { title: 'Updated Title' }
})
```

## Delete Mutation

```typescript
const deleteMutation = useMutation({
  mutationFn: (postId) => {
    return fetch(`/api/posts/${postId}`, {
      method: 'DELETE',
    })
  },
  onSuccess: (_, postId) => {
    queryClient.invalidateQueries({ queryKey: ['posts'] })
  },
})

<button onClick={() => deleteMutation.mutate(post.id)}>
  Delete
</button>
```

## Next Steps

Continue to [Lesson 2: Caching Strategies](./lesson-02-caching.md)
