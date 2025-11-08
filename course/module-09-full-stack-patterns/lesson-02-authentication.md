# Lesson 2: Authentication and Authorization

## Session Management

```typescript
// app/lib/auth.ts
import { createServerFn } from '@tanstack/start'

export const login = createServerFn('POST', async (credentials) => {
  const user = await validateCredentials(credentials)
  
  if (!user) {
    throw new Error('Invalid credentials')
  }
  
  // Create session
  const session = await createSession(user.id)
  
  return { user, session }
})

export const logout = createServerFn('POST', async () => {
  await destroySession()
})

export const getCurrentUser = createServerFn('GET', async () => {
  const userId = await getUserIdFromSession()
  
  if (!userId) {
    return null
  }
  
  return await db.users.findUnique({
    where: { id: userId }
  })
})
```

## Protected Routes

```typescript
export const Route = createFileRoute('/dashboard')({
  beforeLoad: async ({ context }) => {
    const user = await getCurrentUser()
    
    if (!user) {
      throw redirect({ to: '/login' })
    }
    
    return { user }
  },
  component: Dashboard,
})
```

## Role-Based Access

```typescript
export const Route = createFileRoute('/admin')({
  beforeLoad: async ({ context }) => {
    const user = await getCurrentUser()
    
    if (!user || !user.roles.includes('admin')) {
      throw redirect({ to: '/' })
    }
  },
  component: AdminDashboard,
})
```

## Next Steps

Continue to [Lesson 3: Form Handling and Validation](./lesson-03-forms.md)
