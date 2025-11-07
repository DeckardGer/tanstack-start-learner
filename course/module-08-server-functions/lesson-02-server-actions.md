# Lesson 2: Creating Server Actions

## Server Actions

Server actions are server functions that mutate data:

```typescript
import { createServerFn } from '@tanstack/start'

export const createPost = createServerFn('POST', async (data: PostInput) => {
  // Validate input
  const validated = postSchema.parse(data)
  
  // Save to database
  const post = await db.posts.create({
    data: validated
  })
  
  return post
})
```

## Using Actions in Forms

```typescript
function CreatePostForm() {
  const mutation = useMutation({
    mutationFn: createPost,
  })

  const handleSubmit = async (e) => {
    e.preventDefault()
    const formData = new FormData(e.target)
    
    await mutation.mutateAsync({
      title: formData.get('title'),
      content: formData.get('content'),
    })
  }

  return (
    <form onSubmit={handleSubmit}>
      <input name="title" />
      <textarea name="content" />
      <button type="submit">Create</button>
    </form>
  )
}
```

## Authentication in Actions

```typescript
export const updateProfile = createServerFn('POST', async (data: ProfileUpdate) => {
  // Get user from session
  const user = await getUser()
  
  if (!user) {
    throw new Error('Unauthorized')
  }
  
  // Update profile
  await db.users.update({
    where: { id: user.id },
    data
  })
})
```

## Next Steps

Continue to [Lesson 3: SSR and Streaming](./lesson-03-ssr-streaming.md)
