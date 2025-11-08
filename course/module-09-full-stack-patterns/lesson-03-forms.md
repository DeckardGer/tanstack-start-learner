# Lesson 3: Form Handling and Validation

## Form with Validation

```typescript
import { useForm } from '@tanstack/react-form'
import { z } from 'zod'

const postSchema = z.object({
  title: z.string().min(1).max(100),
  content: z.string().min(10),
})

function CreatePostForm() {
  const form = useForm({
    defaultValues: {
      title: '',
      content: '',
    },
    onSubmit: async ({ value }) => {
      // Validate
      const validated = postSchema.parse(value)
      
      // Submit
      await createPost(validated)
    },
  })

  return (
    <form onSubmit={(e) => {
      e.preventDefault()
      form.handleSubmit()
    }}>
      <form.Field name="title">
        {(field) => (
          <input
            value={field.state.value}
            onChange={(e) => field.handleChange(e.target.value)}
          />
        )}
      </form.Field>
      
      <button type="submit">Submit</button>
    </form>
  )
}
```

## Server-Side Validation

```typescript
export const createPost = createServerFn('POST', async (data: unknown) => {
  // Validate on server
  const validated = postSchema.parse(data)
  
  // Create post
  const post = await db.posts.create({
    data: validated
  })
  
  return post
})
```

## Next Steps

Continue to [Lesson 4: Error Handling](./lesson-04-error-handling.md)
