# Lesson 4: Server-Side Data Fetching

## Fetching on Server

Route loaders run on the server during SSR:

```typescript
export const Route = createFileRoute('/dashboard')({
  loader: async ({ context }) => {
    // Runs on server
    const user = await db.users.findUnique({
      where: { id: context.userId }
    })
    
    return { user }
  },
  component: Dashboard,
})
```

## Parallel Data Loading

```typescript
loader: async ({ params }) => {
  const [post, comments, author] = await Promise.all([
    fetchPost(params.postId),
    fetchComments(params.postId),
    fetchAuthor(params.authorId),
  ])
  
  return { post, comments, author }
}
```

## Deferred Loading

Load critical data first, defer non-critical:

```typescript
loader: async ({ params }) => {
  const post = await fetchPost(params.postId)
  
  // Defer comments
  const commentsPromise = fetchComments(params.postId)
  
  return {
    post,
    comments: commentsPromise, // Resolves on client
  }
}
```

## Next Steps

Continue to [Lesson 5: Hydration and Client Transitions](./lesson-05-hydration.md)
