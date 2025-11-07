# Lesson 4: Error Handling and Error Boundaries

## Error Boundaries

```typescript
// app/routes/__root.tsx
export const Route = createRootRoute({
  component: RootComponent,
  errorComponent: GlobalErrorBoundary,
})

function GlobalErrorBoundary({ error }) {
  return (
    <div>
      <h1>Something went wrong</h1>
      <p>{error.message}</p>
    </div>
  )
}
```

## Route-Level Errors

```typescript
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    const post = await fetchPost(params.postId)
    
    if (!post) {
      throw new Error('Post not found')
    }
    
    return { post }
  },
  errorComponent: ({ error }) => (
    <div>Error: {error.message}</div>
  ),
})
```

## Mutation Errors

```typescript
const mutation = useMutation({
  mutationFn: createPost,
  onError: (error) => {
    toast.error(error.message)
  },
})
```

## Next Steps

Continue to [Lesson 5: File Uploads](./lesson-05-file-uploads.md)
