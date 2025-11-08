# Lesson 3: SSR and Streaming Fundamentals

## Server-Side Rendering

TanStack Start renders on the server by default:

```typescript
// app/routes/posts/$postId.tsx
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    // Runs on server
    const post = await fetchPost(params.postId)
    return { post }
  },
  component: PostPage,
})

// HTML is sent to browser with data already loaded
```

## Streaming

Stream HTML as it's generated:

```typescript
// app/ssr.tsx
export default createStartHandler({
  createRouter,
  getRouterManifest,
})(defaultStreamHandler)
// Uses streaming by default!
```

## Benefits of SSR

1. **SEO** - Search engines see content
2. **Performance** - Faster initial render
3. **Accessibility** - Works without JS
4. **Social Sharing** - Correct meta tags

## Next Steps

Continue to [Lesson 4: Server-Side Data Fetching](./lesson-04-server-data-fetching.md)
