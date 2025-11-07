# Lesson 1: Directory Structure Deep Dive

## Introduction

Understanding your project's directory structure is crucial for effective development. TanStack Start follows conventions that make your code organized and maintainable. Let's explore each directory and file in detail.

## The Complete Project Structure

When you create a new TanStack Start project, you'll see this structure:

```
my-app/
├── .vinxi/              # Build cache (gitignored)
├── .output/             # Production build output
├── app/                 # Your application code
│   ├── routes/          # Route components
│   ├── components/      # Reusable components
│   ├── utils/           # Utility functions
│   ├── client.tsx       # Client entry point
│   ├── router.tsx       # Router configuration
│   ├── routes.ts        # Generated routes (auto)
│   └── ssr.tsx          # Server entry point
├── public/              # Static assets
├── node_modules/        # Dependencies
├── .gitignore          # Git ignore rules
├── app.config.ts       # TanStack Start config
├── package.json        # Project metadata
├── tsconfig.json       # TypeScript config
└── vite.config.ts      # Vite configuration
```

## Core Directories

### The `app/` Directory

This is where all your application code lives. It's the heart of your project.

**Purpose**: Contains all your application logic, components, and routing

**Key subdirectories**:

```typescript
app/
├── routes/              // Route components (pages)
│   ├── __root.tsx      // Root layout
│   ├── index.tsx       // Home page (/)
│   ├── about.tsx       // About page (/about)
│   └── posts/          // Nested routes
│       ├── index.tsx   // Posts list (/posts)
│       └── $postId.tsx // Single post (/posts/:postId)
├── components/         // Reusable UI components
│   ├── ui/            // Base UI components
│   ├── layout/        // Layout components
│   └── shared/        // Shared business components
├── utils/             // Utility functions
│   ├── api.ts         // API helpers
│   ├── formatting.ts  // Formatters
│   └── validation.ts  // Validators
├── hooks/             // Custom React hooks
├── lib/               // Third-party library configs
├── types/             // TypeScript type definitions
├── styles/            // Global styles
└── data/              // Data fetching and mutations
```

### The `app/routes/` Directory

This is where TanStack Start's file-based routing magic happens.

**File Naming Convention**:

```
routes/
├── __root.tsx          // Root layout (wraps everything)
├── index.tsx           // / (home page)
├── about.tsx           // /about
├── contact.tsx         // /contact
├── $postId.tsx         // /:postId (dynamic param)
├── _layout.tsx         // Layout route (doesn't add URL segment)
├── posts/              // /posts/* routes
│   ├── index.tsx       // /posts
│   ├── $postId.tsx     // /posts/:postId
│   └── new.tsx         // /posts/new
└── (auth)/             // Route group (no URL segment)
    ├── login.tsx       // /login
    └── register.tsx    // /register
```

**Special Files**:

1. **`__root.tsx`** - The root of your app
   ```typescript
   // app/routes/__root.tsx
   import { createRootRoute, Outlet } from '@tanstack/react-router'

   export const Route = createRootRoute({
     component: RootComponent,
   })

   function RootComponent() {
     return (
       <html>
         <head>
           <meta charSet="UTF-8" />
           <meta name="viewport" content="width=device-width, initial-scale=1.0" />
           <title>My App</title>
         </head>
         <body>
           <Outlet /> {/* Child routes render here */}
         </body>
       </html>
     )
   }
   ```

2. **`index.tsx`** - Default route for a path
   ```typescript
   // app/routes/index.tsx
   import { createFileRoute } from '@tanstack/react-router'

   export const Route = createFileRoute('/')({
     component: Home,
   })

   function Home() {
     return <h1>Welcome Home!</h1>
   }
   ```

3. **Dynamic routes** - Use `$` prefix
   ```typescript
   // app/routes/posts/$postId.tsx
   import { createFileRoute } from '@tanstack/react-router'

   export const Route = createFileRoute('/posts/$postId')({
     component: PostDetail,
   })

   function PostDetail() {
     const { postId } = Route.useParams()
     return <div>Post ID: {postId}</div>
   }
   ```

### The `public/` Directory

Static files that are served directly without processing.

**Purpose**: Host static assets like images, fonts, and files

**Example structure**:

```
public/
├── favicon.ico
├── robots.txt
├── images/
│   ├── logo.png
│   └── hero.jpg
├── fonts/
│   └── custom-font.woff2
└── downloads/
    └── guide.pdf
```

**Usage**:

```typescript
// Reference public files from root
<img src="/images/logo.png" alt="Logo" />
<link rel="icon" href="/favicon.ico" />

// These files are copied as-is to the output
```

**Best Practices**:

- Keep files small and optimized
- Use image formats like WebP for better compression
- Version assets for cache busting if needed
- Don't put sensitive data here (it's publicly accessible)

### The `.output/` Directory

Production build output (generated by `npm run build`).

**Structure**:

```
.output/
├── public/           // Static assets for serving
│   ├── assets/      // Hashed JS/CSS bundles
│   └── ...          // Copied from public/
└── server/          // Server-side code
    └── index.mjs    // Server entry point
```

**Important**: This directory is git-ignored and regenerated on each build.

### The `.vinxi/` Directory

Build cache and development artifacts.

**Purpose**: Speeds up rebuilds by caching compiled files

**Note**: Always git-ignore this directory. It's automatically managed.

## Configuration Files

### `app.config.ts`

TanStack Start's main configuration file.

```typescript
// app.config.ts
import { defineConfig } from '@tanstack/start/config'

export default defineConfig({
  // Server configuration
  server: {
    preset: 'node-server', // or 'vercel', 'netlify', 'cloudflare'
    port: 3000,
  },

  // Router configuration
  routers: {
    // SSR router
    ssr: {
      entry: './app/ssr.tsx',
    },
    // Client router
    client: {
      entry: './app/client.tsx',
    },
  },

  // Vite configuration
  vite: {
    // Additional Vite config here
  },
})
```

### `tsconfig.json`

TypeScript configuration.

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "moduleResolution": "bundler",
    "jsx": "react-jsx",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "paths": {
      "~/*": ["./app/*"]
    }
  },
  "include": ["app/**/*"],
  "exclude": ["node_modules"]
}
```

**Key options explained**:
- `jsx: "react-jsx"`: Modern JSX transform (no need to import React)
- `strict: true`: Enable all strict type checking
- `paths`: Path aliases for cleaner imports

### `package.json`

Project metadata and scripts.

```json
{
  "name": "my-tanstack-start-app",
  "version": "0.1.0",
  "type": "module",
  "scripts": {
    "dev": "vinxi dev",
    "build": "vinxi build",
    "start": "vinxi start"
  },
  "dependencies": {
    "@tanstack/react-router": "^1.0.0",
    "@tanstack/start": "^1.0.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "vinxi": "^0.1.0"
  },
  "devDependencies": {
    "@types/react": "^18.2.0",
    "@types/react-dom": "^18.2.0",
    "typescript": "^5.0.0",
    "vite": "^5.0.0"
  }
}
```

**Important**: `"type": "module"` enables ES modules

## Entry Points

### `app/client.tsx`

Client-side entry point.

```typescript
// app/client.tsx
import { hydrateRoot } from 'react-dom/client'
import { StartClient } from '@tanstack/start'
import { createRouter } from './router'

const router = createRouter()

hydrateRoot(document, <StartClient router={router} />)
```

**Purpose**: Hydrates the server-rendered HTML on the client

### `app/ssr.tsx`

Server-side entry point.

```typescript
// app/ssr.tsx
import { createStartHandler, defaultStreamHandler } from '@tanstack/start/server'
import { getRouterManifest } from '@tanstack/start/router-manifest'
import { createRouter } from './router'

export default createStartHandler({
  createRouter,
  getRouterManifest,
})(defaultStreamHandler)
```

**Purpose**: Handles server-side rendering

### `app/router.tsx`

Router configuration.

```typescript
// app/router.tsx
import { createRouter as createTanStackRouter } from '@tanstack/react-router'
import { routeTree } from './routeTree.gen'

export function createRouter() {
  return createTanStackRouter({
    routeTree,
    defaultPreload: 'intent',
  })
}

declare module '@tanstack/react-router' {
  interface Register {
    router: ReturnType<typeof createRouter>
  }
}
```

## Organizing Your Code

### Recommended Structure for Growth

As your app grows, organize like this:

```
app/
├── routes/                    // Routes only
│   └── products/
│       ├── index.tsx
│       └── $productId.tsx
├── features/                  // Feature-based organization
│   ├── products/
│   │   ├── components/       // Product-specific components
│   │   ├── hooks/            // Product-specific hooks
│   │   ├── utils/            // Product utilities
│   │   └── types.ts          // Product types
│   ├── auth/
│   │   ├── components/
│   │   ├── hooks/
│   │   └── utils/
│   └── cart/
├── components/               // Shared components
│   ├── ui/                  // Base UI (Button, Input, etc.)
│   └── layout/              // Layout components
├── lib/                     // Third-party configurations
│   ├── queryClient.ts       // TanStack Query setup
│   └── api.ts               // API client setup
├── hooks/                   // Shared hooks
├── utils/                   // Shared utilities
└── types/                   // Shared types
```

### Import Path Aliases

Configure clean imports in `tsconfig.json`:

```json
{
  "compilerOptions": {
    "paths": {
      "~/*": ["./app/*"],
      "@/components/*": ["./app/components/*"],
      "@/features/*": ["./app/features/*"],
      "@/utils/*": ["./app/utils/*"],
      "@/types/*": ["./app/types/*"]
    }
  }
}
```

**Usage**:

```typescript
// Instead of:
import { Button } from '../../../components/ui/Button'

// Use:
import { Button } from '@/components/ui/Button'
// or
import { Button } from '~/components/ui/Button'
```

## Common Patterns

### Feature-Based Organization

Group related code by feature:

```typescript
// app/features/products/components/ProductCard.tsx
export function ProductCard({ product }) {
  return <div>{product.name}</div>
}

// app/features/products/hooks/useProducts.ts
export function useProducts() {
  return useQuery({
    queryKey: ['products'],
    queryFn: fetchProducts,
  })
}

// app/routes/products/index.tsx
import { ProductCard } from '~/features/products/components/ProductCard'
import { useProducts } from '~/features/products/hooks/useProducts'

export const Route = createFileRoute('/products/')({
  component: ProductsPage,
})

function ProductsPage() {
  const { data: products } = useProducts()
  return (
    <div>
      {products?.map(product => (
        <ProductCard key={product.id} product={product} />
      ))}
    </div>
  )
}
```

### Separation of Concerns

```
app/
├── routes/              // Presentation & routing
├── features/            // Business logic
├── components/          // UI components
├── data/               // Data fetching
└── lib/                // External integrations
```

## Best Practices

### 1. Keep Routes Simple

Routes should be thin - they coordinate components but don't contain heavy logic:

```typescript
// Good: Route delegates to components
function ProductPage() {
  return (
    <div>
      <ProductHeader />
      <ProductDetails />
      <RelatedProducts />
    </div>
  )
}

// Avoid: Route contains all logic
function ProductPage() {
  // ... 200 lines of component logic
}
```

### 2. Colocate Related Files

Keep related files close:

```
features/products/
├── components/
│   ├── ProductCard.tsx
│   └── ProductCard.test.tsx    // Test next to component
├── hooks/
│   ├── useProducts.ts
│   └── useProducts.test.ts
└── utils/
    ├── pricing.ts
    └── pricing.test.ts
```

### 3. Use Barrel Exports

Make imports cleaner:

```typescript
// app/features/products/index.ts
export { ProductCard } from './components/ProductCard'
export { ProductDetails } from './components/ProductDetails'
export { useProducts } from './hooks/useProducts'
export { calculatePrice } from './utils/pricing'

// Now import from one place:
import { ProductCard, useProducts } from '~/features/products'
```

### 4. Type Definitions

Organize types clearly:

```typescript
// app/features/products/types.ts
export interface Product {
  id: string
  name: string
  price: number
}

export interface ProductFilters {
  category?: string
  minPrice?: number
  maxPrice?: number
}
```

## Exercises

### Exercise 1: Explore Your Project

1. Create a new TanStack Start project
2. List all directories and files
3. Identify the purpose of each directory
4. Find the root route file
5. Locate the client and server entry points

### Exercise 2: Create a New Feature

1. Create a `features/blog` directory structure
2. Add components, hooks, and utils folders
3. Create a `types.ts` file with blog post types
4. Create a barrel export `index.ts`
5. Import from your feature in a route

### Exercise 3: Configure Path Aliases

1. Add path aliases to `tsconfig.json`
2. Create a shared component
3. Import it using the alias
4. Verify it works in development

## Common Issues

### Issue: Import path not resolving

**Problem**:
```typescript
import { Button } from '@/components/Button'
// Error: Cannot find module
```

**Solution**:
```json
// tsconfig.json
{
  "compilerOptions": {
    "paths": {
      "@/components/*": ["./app/components/*"]
    }
  }
}
```

Restart your editor/dev server after changing `tsconfig.json`.

### Issue: Routes not generating

**Problem**: New route file not showing up

**Solution**:
- Make sure file is in `app/routes/`
- File must export a Route using `createFileRoute()`
- Restart dev server

## Summary

You now understand:

- The complete directory structure
- Where to put different types of files
- How entry points work
- How to organize code as your app grows
- Common patterns and best practices

## Next Steps

Continue to [Lesson 2: Configuration Files Explained](./lesson-02-configuration-files.md) to dive deeper into configuring your project.

## Further Reading

- [TanStack Start File Structure](https://tanstack.com/start/latest/docs/file-structure)
- [File-based Routing Guide](https://tanstack.com/router/latest/docs/routing)
- [TypeScript Path Mapping](https://www.typescriptlang.org/docs/handbook/module-resolution.html#path-mapping)
