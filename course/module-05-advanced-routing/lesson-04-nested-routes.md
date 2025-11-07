# Lesson 4: Nested Routes and Advanced Layouts

Complex applications need nested routing structures.

## Deep Nesting

```
app/routes/
├── dashboard/
│   ├── projects/
│   │   ├── $projectId/
│   │   │   ├── tasks/
│   │   │   │   └── $taskId.tsx
```

## Shared Layouts

```typescript
// Parent route
export const Route = createFileRoute('/dashboard')({
  loader: async () => {
    const user = await fetchUser()
    return { user }
  },
  component: DashboardLayout,
})

// Child routes access parent data
```

Next: [Lesson 5: Route Context and Dependency Injection](./lesson-05-route-context.md)
