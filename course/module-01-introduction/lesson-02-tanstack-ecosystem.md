# Lesson 2: The TanStack Ecosystem

## Introduction

TanStack Start is part of a larger ecosystem of high-quality, type-safe libraries. Understanding how these pieces fit together will help you make the most of TanStack Start.

## The TanStack Family

TanStack (formerly React Query) has evolved into a comprehensive suite of libraries for building modern web applications. All TanStack libraries share common principles:

- **Type-safety first**
- **Framework agnostic** (with specific adapters for React, Vue, Solid, etc.)
- **Performance-focused**
- **Developer experience**
- **Zero dependencies** (where possible)

## Core Libraries in TanStack Start

### 1. TanStack Router

**Purpose**: Type-safe, file-based routing for modern web applications

**Key Features**:
- File-based routing with full type inference
- Nested routes and layouts
- Route loaders for data fetching
- Search parameter validation and type safety
- Built-in code splitting

**Example**:
```typescript
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    return fetchPost(params.postId) // params.postId is type-safe!
  },
  component: PostComponent,
})
```

**Why it matters in Start**: TanStack Router is the foundation of TanStack Start, providing the routing layer and enabling SSR capabilities.

### 2. TanStack Query

**Purpose**: Powerful async state management and data fetching

**Key Features**:
- Automatic caching and background updates
- Request deduplication
- Optimistic updates
- Infinite queries and pagination
- DevTools for debugging
- SSR support

**Example**:
```typescript
import { useQuery } from '@tanstack/react-query'

function Posts() {
  const { data, isLoading } = useQuery({
    queryKey: ['posts'],
    queryFn: fetchPosts,
  })

  if (isLoading) return <div>Loading...</div>
  return <div>{data.map(post => <Post key={post.id} {...post} />)}</div>
}
```

**Why it matters in Start**: Provides the data layer, handles caching, and integrates seamlessly with routing and SSR.

## Additional TanStack Libraries

While TanStack Start primarily uses Router and Query, the ecosystem includes other powerful tools:

### 3. TanStack Table

**Purpose**: Headless UI for building powerful tables and datagrids

**Key Features**:
- Sorting, filtering, pagination
- Column resizing and reordering
- Grouping and aggregation
- Virtual scrolling
- Fully type-safe

**Usage in Start**:
```typescript
import { useReactTable } from '@tanstack/react-table'

function DataTable() {
  const table = useReactTable({
    data,
    columns,
    getCoreRowModel: getCoreRowModel(),
  })

  // Render your table with full control
}
```

### 4. TanStack Form

**Purpose**: Type-safe, performant form state management

**Key Features**:
- Field-level validation
- Async validation
- Type-safe form schemas
- Framework adapters
- Optimized re-renders

**Usage in Start**:
```typescript
import { useForm } from '@tanstack/react-form'

function ContactForm() {
  const form = useForm({
    defaultValues: {
      name: '',
      email: '',
    },
    onSubmit: async (values) => {
      await submitForm(values)
    },
  })

  return <form>...</form>
}
```

### 5. TanStack Virtual

**Purpose**: Headless UI for virtualizing large lists

**Key Features**:
- Virtual scrolling
- Variable size items
- Horizontal and vertical
- Smooth scrolling
- SSR compatible

## How the Ecosystem Works Together

TanStack Start integrates these libraries seamlessly:

```typescript
// A complete example showing integration
import { createFileRoute } from '@tanstack/react-router'
import { useQuery } from '@tanstack/react-query'
import { useReactTable } from '@tanstack/react-table'

export const Route = createFileRoute('/users')({
  loader: async () => {
    // Pre-fetch data for SSR
    const users = await fetchUsers()
    return { users }
  },
})

function UsersPage() {
  // TanStack Query for data
  const { data: users } = useQuery({
    queryKey: ['users'],
    queryFn: fetchUsers,
  })

  // TanStack Table for display
  const table = useReactTable({
    data: users,
    columns: userColumns,
    getCoreRowModel: getCoreRowModel(),
  })

  return <div>{/* Render table */}</div>
}
```

## The Philosophy Behind TanStack

All TanStack libraries follow these principles:

### 1. Headless UI

TanStack libraries provide logic and state management without imposing UI decisions:

```typescript
// TanStack provides the logic
const table = useReactTable({ ... })

// You control the UI
return (
  <table className="your-styles">
    {/* Your markup */}
  </table>
)
```

### 2. Type Safety

Every library is built with TypeScript and provides excellent type inference:

```typescript
// Route params are typed
const { postId } = Route.useParams() // postId: string

// Search params are typed
const { filter } = Route.useSearch() // filter: { status: 'active' | 'inactive' }

// Query data is typed
const { data } = useQuery({
  queryKey: ['post', postId],
  queryFn: () => fetchPost(postId),
}) // data: Post | undefined
```

### 3. Framework Agnostic

While TanStack Start uses React, the core libraries support multiple frameworks:

- **React**: `@tanstack/react-query`, `@tanstack/react-router`
- **Vue**: `@tanstack/vue-query`, `@tanstack/vue-router`
- **Solid**: `@tanstack/solid-query`, `@tanstack/solid-router`
- **Svelte**: `@tanstack/svelte-query`
- **Angular**: `@tanstack/angular-query-experimental`

### 4. Composability

Libraries are designed to work independently or together:

```typescript
// Use Router alone
import { createFileRoute } from '@tanstack/react-router'

// Use Query alone
import { useQuery } from '@tanstack/react-query'

// Use together in Start
import { createFileRoute } from '@tanstack/react-router'
import { useQuery } from '@tanstack/react-query'
```

## Package Architecture

When you install TanStack Start, you get:

```json
{
  "dependencies": {
    "@tanstack/start": "latest",        // Main framework
    "@tanstack/react-router": "latest", // Routing (peer dependency)
    "@tanstack/react-query": "latest"   // Data fetching (optional but recommended)
  }
}
```

The architecture looks like:

```
@tanstack/start
├── Built on @tanstack/react-router
├── Integrates @tanstack/react-query
└── Adds server capabilities
    ├── Server functions
    ├── SSR support
    ├── Bundling (Vite)
    └── Deployment adapters
```

## Version Compatibility

TanStack libraries are versioned independently but maintain compatibility:

- **TanStack Start**: Currently in RC (Release Candidate)
- **TanStack Router**: v1.x (stable)
- **TanStack Query**: v5.x (stable)

Always check the official documentation for the latest compatibility matrix.

## Community and Resources

The TanStack ecosystem has a thriving community:

- **GitHub**: Individual repos for each library
- **Discord**: Active community support
- **Twitter/X**: [@TanStack](https://twitter.com/tanstack)
- **Documentation**: Comprehensive docs for each library
- **Examples**: Extensive example repositories

## Summary

The TanStack ecosystem consists of:

1. **TanStack Router**: Type-safe routing (core of Start)
2. **TanStack Query**: Data fetching and caching (integrated in Start)
3. **TanStack Table**: Advanced table/datagrid functionality
4. **TanStack Form**: Type-safe form management
5. **TanStack Virtual**: Virtualization for large lists

All libraries share:
- Type-safety first approach
- Framework-agnostic design
- Headless UI philosophy
- Excellent developer experience
- Performance optimization

TanStack Start brings Router and Query together with server capabilities to create a complete full-stack framework.

## Next Steps

Continue to [Lesson 3: Why Choose TanStack Start?](./lesson-03-why-tanstack-start.md) to understand the benefits and use cases.

## Further Reading

- [TanStack Official Website](https://tanstack.com)
- [TanStack Router Docs](https://tanstack.com/router)
- [TanStack Query Docs](https://tanstack.com/query)
- [TanStack Table Docs](https://tanstack.com/table)
- [TanStack Form Docs](https://tanstack.com/form)
