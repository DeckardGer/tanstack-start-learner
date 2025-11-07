# Lesson 5: Route Context and Dependency Injection

Route context allows sharing data across routes.

## Router Context

```typescript
interface RouterContext {
  queryClient: QueryClient
  auth: AuthContext
}

export function createRouter() {
  return createTanStackRouter({
    routeTree,
    context: {
      queryClient: new QueryClient(),
      auth: getAuthContext(),
    },
  })
}
```

## Using Context in Routes

```typescript
export const Route = createFileRoute('/dashboard')({
  beforeLoad: ({ context }) => {
    if (!context.auth.user) {
      throw redirect({ to: '/login' })
    }
  },
})
```

Next: [Lesson 6: Route Guards and Authentication](./lesson-06-route-guards.md)
