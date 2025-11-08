# Lesson 5: Query DevTools and Debugging

## Installing DevTools

```typescript
// app/routes/__root.tsx
import { ReactQueryDevtools } from '@tanstack/react-query-devtools'

export const Route = createRootRoute({
  component: RootComponent,
})

function RootComponent() {
  return (
    <>
      <Outlet />
      {import.meta.env.DEV && <ReactQueryDevtools />}
    </>
  )
}
```

## DevTools Features

1. **Query Explorer** - View all queries
2. **Query Details** - Inspect query state
3. **Cache Inspector** - View cached data
4. **Timeline** - See query lifecycle
5. **Mutations** - Track mutations

## Debugging Tips

### 1. Query Keys

```typescript
// Use descriptive keys
queryKey: ['posts', 'published', { page: 1 }]
```

### 2. Logger

```typescript
const queryClient = new QueryClient({
  logger: {
    log: console.log,
    warn: console.warn,
    error: console.error,
  },
})
```

### 3. Query Data

```typescript
// Inspect query data
const data = queryClient.getQueryData(['posts'])
console.log('Current posts:', data)
```

## Next Steps

Continue to [Lesson 6: Infinite Queries](./lesson-06-infinite-queries.md)
