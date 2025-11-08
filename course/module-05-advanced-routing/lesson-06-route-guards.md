# Lesson 6: Route Guards and Authentication

Protect routes with authentication and authorization.

## Authentication Guard

```typescript
export const Route = createFileRoute('/admin')({
  beforeLoad: async ({ context }) => {
    if (!context.auth.isAuthenticated) {
      throw redirect({
        to: '/login',
        search: { redirect: '/admin' }
      })
    }
  },
  component: AdminPage,
})
```

## Role-Based Access

```typescript
beforeLoad: async ({ context }) => {
  if (!context.auth.user?.roles.includes('admin')) {
    throw redirect({ to: '/' })
  }
}
```

## Summary

Route guards provide secure, type-safe protection for your routes.

Module 5 Complete! Next: [Module 6: Data Fetching Basics](../module-06-data-fetching-basics/README.md)
