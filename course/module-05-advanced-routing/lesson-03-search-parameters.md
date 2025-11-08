# Lesson 3: Search Parameters with Type Safety

Search parameters (query strings) can be validated and typed using schemas.

## Type-Safe Search Params

```typescript
import { z } from 'zod'

const searchSchema = z.object({
  page: z.number().int().positive().default(1),
  sort: z.enum(['name', 'date', 'price']).default('date'),
  category: z.string().optional(),
})

export const Route = createFileRoute('/products/')({
  validateSearch: searchSchema,
  component: ProductsPage,
})

function ProductsPage() {
  const { page, sort, category } = Route.useSearch()
  // All params are validated and typed!
}
```

## Updating Search Params

```typescript
<Link search={(prev) => ({ ...prev, page: 2 })}>
  Page 2
</Link>
```

## Summary

Type-safe search parameters prevent bugs and provide autocomplete.

Next: [Lesson 4: Nested Routes and Advanced Layouts](./lesson-04-nested-routes.md)
