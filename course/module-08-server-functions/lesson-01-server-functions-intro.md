# Lesson 1: Introduction to Server Functions

## What are Server Functions?

Server functions run exclusively on the server, allowing you to access databases, APIs, and secrets securely.

## Creating a Server Function

```typescript
// app/functions/posts.ts
import { createServerFn } from '@tanstack/start'

export const getPosts = createServerFn('GET', async () => {
  // This runs only on the server
  const posts = await db.posts.findMany()
  return posts
})
```

## Using Server Functions

```typescript
// app/routes/posts/index.tsx
export const Route = createFileRoute('/posts/')({
  loader: async () => {
    const posts = await getPosts()
    return { posts }
  },
  component: PostsPage,
})
```

## Server-Only Code

```typescript
import { createServerFn } from '@tanstack/start'
import { db } from './db' // This import never reaches the client!

export const getUser = createServerFn('GET', async (userId: string) => {
  // Access database directly
  const user = await db.users.findUnique({
    where: { id: userId }
  })
  
  // Use secrets safely
  const apiKey = process.env.SECRET_API_KEY
  
  return user
})
```

## Type Safety

Server functions are fully typed:

```typescript
export const createPost = createServerFn('POST', async (data: CreatePostInput) => {
  const post = await db.posts.create({ data })
  return post  // Type: Post
})

// Usage
const post = await createPost({ title: 'Hello', content: 'World' })
//    ^? Post
```

## Next Steps

Continue to [Lesson 2: Creating Server Actions](./lesson-02-server-actions.md)
