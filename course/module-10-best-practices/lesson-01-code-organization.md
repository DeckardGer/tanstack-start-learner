# Lesson 1: Code Organization and Architecture

## Feature-Based Structure

Organize by features, not file types:

```
app/
├── features/
│   ├── auth/
│   │   ├── components/
│   │   │   ├── LoginForm.tsx
│   │   │   └── RegisterForm.tsx
│   │   ├── hooks/
│   │   │   └── useAuth.ts
│   │   ├── lib/
│   │   │   └── auth.ts
│   │   └── types.ts
│   ├── posts/
│   │   ├── components/
│   │   ├── hooks/
│   │   └── lib/
│   └── users/
├── components/       # Shared components
├── lib/             # Shared utilities
└── routes/          # Route components
```

## Separation of Concerns

```typescript
// ✅ Good - Separated concerns
// features/posts/lib/posts.ts
export async function fetchPosts() {
  return await db.posts.findMany()
}

// features/posts/hooks/usePosts.ts
export function usePosts() {
  return useQuery({
    queryKey: ['posts'],
    queryFn: fetchPosts,
  })
}

// features/posts/components/PostsList.tsx
export function PostsList() {
  const { data: posts } = usePosts()
  return <div>{/* render */}</div>
}

// routes/posts/index.tsx
export const Route = createFileRoute('/posts/')({
  component: PostsList,
})
```

## Barrel Exports

```typescript
// features/posts/index.ts
export * from './components'
export * from './hooks'
export * from './types'

// Usage
import { PostsList, usePosts, Post } from '~/features/posts'
```

## Next Steps

Continue to [Lesson 2: Performance Optimization](./lesson-02-performance.md)
