# Lesson 2: Route Loaders and Data Loading

## Introduction

Route loaders allow you to fetch data before rendering a route. This ensures data is ready when components mount, improving user experience.

## Basic Loader

```typescript
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    const post = await fetchPost(params.postId)
    return { post }
  },
  component: PostPage,
})

function PostPage() {
  const { post } = Route.useLoaderData()
  return <h1>{post.title}</h1>
}
```

## Loader Parameters

Loaders receive context:

```typescript
loader: async ({ params, context, location, search }) => {
  // params: Route parameters
  // context: Router context
  // location: Current location
  // search: Search parameters
  
  const data = await fetchData(params.id)
  return { data }
}
```

## Parallel Data Loading

```typescript
loader: async ({ params }) => {
  // Load in parallel
  const [post, comments, related] = await Promise.all([
    fetchPost(params.postId),
    fetchComments(params.postId),
    fetchRelatedPosts(params.postId),
  ])
  
  return { post, comments, related }
}
```

## Error Handling

```typescript
loader: async ({ params }) => {
  const post = await fetchPost(params.postId)
  
  if (!post) {
    throw new Error('Post not found')
  }
  
  return { post }
},
errorComponent: ({ error }) => <div>Error: {error.message}</div>
```

## Loading States

```typescript
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    const post = await fetchPost(params.postId)
    return { post }
  },
  pendingComponent: () => <div>Loading...</div>,
  component: PostPage,
})
```

## Preloading

```typescript
// Preload on hover
<Link 
  to="/posts/$postId" 
  params={{ postId: '123' }}
  preload="intent"
>
  View Post
</Link>
```

## Summary

Loaders provide a clean way to fetch data before rendering, with built-in error handling and loading states.

## Next Steps

Continue to [Lesson 3: Search Parameters with Type Safety](./lesson-03-search-parameters.md)
