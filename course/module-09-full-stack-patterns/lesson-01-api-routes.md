# Lesson 1: API Routes and Endpoints

## Creating API Routes

Server functions can act as API endpoints:

```typescript
// app/functions/api.ts
import { createServerFn } from '@tanstack/start'

export const getUsers = createServerFn('GET', async () => {
  const users = await db.users.findMany()
  return users
})

export const createUser = createServerFn('POST', async (data: UserInput) => {
  const user = await db.users.create({ data })
  return user
})
```

## REST-like Endpoints

```typescript
// GET /api/posts
export const getPosts = createServerFn('GET', async () => {
  return await db.posts.findMany()
})

// GET /api/posts/:id
export const getPost = createServerFn('GET', async (id: string) => {
  return await db.posts.findUnique({ where: { id } })
})

// POST /api/posts
export const createPost = createServerFn('POST', async (data: PostInput) => {
  return await db.posts.create({ data })
})

// PUT /api/posts/:id
export const updatePost = createServerFn('PUT', async ({ id, data }) => {
  return await db.posts.update({ where: { id }, data })
})

// DELETE /api/posts/:id
export const deletePost = createServerFn('DELETE', async (id: string) => {
  return await db.posts.delete({ where: { id } })
})
```

## Error Handling

```typescript
export const getPost = createServerFn('GET', async (id: string) => {
  const post = await db.posts.findUnique({ where: { id } })
  
  if (!post) {
    throw new Error('Post not found')
  }
  
  return post
})
```

## Next Steps

Continue to [Lesson 2: Authentication and Authorization](./lesson-02-authentication.md)
