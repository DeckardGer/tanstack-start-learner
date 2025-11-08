# Lesson 3: Entry Points and App Structure

## Introduction

Entry points are where your application starts executing. TanStack Start has separate entry points for the client and server, enabling server-side rendering while maintaining a great developer experience. Let's understand how it all works together.

## The Three Entry Points

TanStack Start has three main entry points:

1. **`app/ssr.tsx`** - Server-side rendering entry
2. **`app/client.tsx`** - Client-side hydration entry
3. **`app/router.tsx`** - Router configuration (shared)

```
        User Request
             |
             v
    +--------+--------+
    |   app/ssr.tsx   |  Server renders HTML
    +--------+--------+
             |
             | HTML sent to browser
             v
    +--------+--------+
    | app/client.tsx  |  Client hydrates & takes over
    +--------+--------+
             |
             | Both use...
             v
    +--------+--------+
    | app/router.tsx  |  Shared router config
    +-----------------+
```

## Server Entry Point: `app/ssr.tsx`

This is where server-side rendering happens.

### Basic Structure

```typescript
// app/ssr.tsx
import {
  createStartHandler,
  defaultStreamHandler,
} from '@tanstack/start/server'
import { getRouterManifest } from '@tanstack/start/router-manifest'

import { createRouter } from './router'

export default createStartHandler({
  createRouter,
  getRouterManifest,
})(defaultStreamHandler)
```

### What's Happening?

1. **`createStartHandler`**: Creates the server request handler
2. **`createRouter`**: Your router configuration
3. **`getRouterManifest`**: Metadata about your routes
4. **`defaultStreamHandler`**: Streams HTML to the browser

### Under the Hood

```typescript
// Simplified version of what happens
async function handleRequest(request: Request) {
  // 1. Create a router instance
  const router = createRouter()

  // 2. Load data for the current route
  await router.load()

  // 3. Render to HTML (streaming)
  const stream = renderToReadableStream(
    <StartServer router={router} />
  )

  // 4. Send to browser
  return new Response(stream, {
    headers: { 'Content-Type': 'text/html' },
  })
}
```

### Custom Server Handler

You can customize the server behavior:

```typescript
// app/ssr.tsx
import { createStartHandler } from '@tanstack/start/server'
import { getRouterManifest } from '@tanstack/start/router-manifest'
import { createRouter } from './router'

export default createStartHandler({
  createRouter,
  getRouterManifest,
})(async ({ request, router }) => {
  // Custom logic before rendering
  const userAgent = request.headers.get('User-Agent')
  console.log('Request from:', userAgent)

  // Add custom headers
  const response = await defaultStreamHandler({ request, router })

  // Modify response
  response.headers.set('X-Custom-Header', 'Hello')

  return response
})
```

### Adding Request Context

Pass data to your routes:

```typescript
// app/ssr.tsx
import { createStartHandler } from '@tanstack/start/server'
import { getRouterManifest } from '@tanstack/start/router-manifest'
import { createRouter } from './router'

export default createStartHandler({
  createRouter,
  getRouterManifest,
})(async ({ request, router }) => {
  // Parse session/auth from cookies
  const session = await getSession(request)

  // Pass to router context
  router.update({
    context: {
      session,
      req: request,
    },
  })

  return defaultStreamHandler({ request, router })
})
```

## Client Entry Point: `app/client.tsx`

This is where client-side JavaScript takes over.

### Basic Structure

```typescript
// app/client.tsx
import { hydrateRoot } from 'react-dom/client'
import { StartClient } from '@tanstack/start'
import { createRouter } from './router'

// Create router instance
const router = createRouter()

// Hydrate the server-rendered HTML
hydrateRoot(document, <StartClient router={router} />)
```

### What's Happening?

1. **`createRouter()`**: Creates a router instance (same as server)
2. **`hydrateRoot()`**: Attaches React to existing HTML
3. **`<StartClient>`**: TanStack Start's client wrapper

### Hydration Process

```
Server HTML           Client Hydration        Interactive App
+-----------+        +---------------+        +-------------+
|  <div>    |        | React attaches|        | Fully       |
|    Hello  |  --->  | event handlers| --->   | interactive |
|  </div>   |        | and state     |        | React app   |
+-----------+        +---------------+        +-------------+
```

### Adding Client-Side Services

```typescript
// app/client.tsx
import { hydrateRoot } from 'react-dom/client'
import { StartClient } from '@tanstack/start'
import { createRouter } from './router'

// Initialize analytics
import { initAnalytics } from './lib/analytics'
initAnalytics()

// Initialize error tracking
import { initErrorTracking } from './lib/errors'
initErrorTracking()

// Create router with client context
const router = createRouter()

// Start hydration
hydrateRoot(document, <StartClient router={router} />)

// Log hydration complete
console.log('App hydrated and ready!')
```

### Development Mode Enhancements

```typescript
// app/client.tsx
import { hydrateRoot } from 'react-dom/client'
import { StartClient } from '@tanstack/start'
import { createRouter } from './router'

const router = createRouter()

// Development-only code
if (import.meta.env.DEV) {
  // Show helpful logs
  console.log('🚀 App starting in development mode')

  // Add debugging helpers
  window.__router = router

  // Enable React DevTools
  if (typeof window !== 'undefined') {
    window.__REACT_DEVTOOLS_GLOBAL_HOOK__ = window.__REACT_DEVTOOLS_GLOBAL_HOOK__ || {}
  }
}

hydrateRoot(document, <StartClient router={router} />)
```

## Router Configuration: `app/router.tsx`

The router configuration is shared between client and server.

### Basic Configuration

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

// Type augmentation for type safety
declare module '@tanstack/react-router' {
  interface Register {
    router: ReturnType<typeof createRouter>
  }
}
```

### Router Options

```typescript
// app/router.tsx
import { createRouter as createTanStackRouter } from '@tanstack/react-router'
import { QueryClient } from '@tanstack/react-query'
import { routeTree } from './routeTree.gen'

export function createRouter() {
  const queryClient = new QueryClient({
    defaultOptions: {
      queries: {
        staleTime: 1000 * 60, // 1 minute
      },
    },
  })

  return createTanStackRouter({
    routeTree,

    // Context passed to all routes
    context: {
      queryClient,
    },

    // Preload strategy
    defaultPreload: 'intent', // Preload on hover/focus

    // Pending component
    defaultPendingComponent: () => (
      <div className="p-4">Loading...</div>
    ),

    // Error component
    defaultErrorComponent: ({ error }) => (
      <div className="p-4 text-red-500">
        Error: {error.message}
      </div>
    ),

    // Not found component
    defaultNotFoundComponent: () => (
      <div className="p-4">404 - Page Not Found</div>
    ),

    // Preloading
    defaultPreloadDelay: 100, // ms
    defaultPreloadStaleTime: 30000, // 30 seconds

    // Caching
    defaultPendingMs: 1000,
    defaultPendingMinMs: 500,
  })
}

// Type augmentation
declare module '@tanstack/react-router' {
  interface Register {
    router: ReturnType<typeof createRouter>
  }
}
```

### Adding Global Context

Make data available to all routes:

```typescript
// app/router.tsx
import { createRouter as createTanStackRouter } from '@tanstack/react-router'
import { QueryClient } from '@tanstack/react-query'
import { routeTree } from './routeTree.gen'

// Define your context type
export interface RouterContext {
  queryClient: QueryClient
  userId?: string
  theme: 'light' | 'dark'
}

export function createRouter() {
  const queryClient = new QueryClient()

  return createTanStackRouter({
    routeTree,
    context: {
      queryClient,
      theme: 'light',
    },
  })
}

// Type augmentation
declare module '@tanstack/react-router' {
  interface Register {
    router: ReturnType<typeof createRouter>
  }

  interface RouterContext extends RouterContext {}
}
```

**Using context in routes**:

```typescript
// app/routes/profile.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/profile')({
  component: ProfilePage,
})

function ProfilePage() {
  // Access context (fully typed!)
  const { queryClient, userId, theme } = Route.useRouteContext()

  return (
    <div data-theme={theme}>
      User ID: {userId}
    </div>
  )
}
```

## The `routeTree.gen.ts` File

This file is auto-generated and should never be edited manually.

```typescript
// app/routeTree.gen.ts
// This file is auto-generated by TanStack Router

import { Route as rootRoute } from './routes/__root'
import { Route as IndexRoute } from './routes/index'
import { Route as AboutRoute } from './routes/about'

const rootRouteChildren = [IndexRoute, AboutRoute]

export const routeTree = rootRoute.addChildren(rootRouteChildren)
```

**When is it generated?**

- On dev server start
- On file changes in `app/routes/`
- On build

**Note**: Commit this file to git for better performance.

## Full App Flow

### Initial Page Load

```
1. Browser requests /about
         ↓
2. Server receives request (app/ssr.tsx)
         ↓
3. Router created (app/router.tsx)
         ↓
4. About route matched
         ↓
5. Route loader runs (if any)
         ↓
6. Component renders to HTML
         ↓
7. HTML + JS sent to browser
         ↓
8. Client hydrates (app/client.tsx)
         ↓
9. App now interactive!
```

### Subsequent Navigation

```
1. User clicks link to /contact
         ↓
2. Client-side navigation
         ↓
3. Route loader runs (if any)
         ↓
4. Component renders
         ↓
5. No server request needed!
```

## Advanced Patterns

### Lazy Loading Routes

For better performance, lazy load route components:

```typescript
// app/routes/dashboard.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/dashboard')({
  // Lazy load the component
  component: () => import('./dashboard-component').then(m => m.DashboardComponent),
})
```

### Route-Level Code Splitting

```typescript
// app/routes/admin.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/admin')({
  // This will create a separate chunk
  component: () => import('../features/admin/AdminPage').then(m => m.AdminPage),
})
```

### Preloading Data

```typescript
// app/router.tsx
export function createRouter() {
  const router = createTanStackRouter({
    routeTree,
    defaultPreload: 'intent', // Preload on hover

    // Preload immediately for critical routes
    defaultPreloadDelay: 0,
  })

  // Preload critical routes
  router.preloadRoute({ to: '/dashboard' })

  return router
}
```

## Development vs Production

### Development Mode

```typescript
// app/client.tsx
if (import.meta.env.DEV) {
  // Development-only code
  console.log('Running in development mode')

  // Helpful debugging
  window.__DEBUG__ = {
    router,
    queryClient,
  }
}
```

### Production Mode

```typescript
// app/ssr.tsx
export default createStartHandler({
  createRouter,
  getRouterManifest,
})(async ({ request, router }) => {
  // Production optimizations
  if (import.meta.env.PROD) {
    // Enable caching headers
    const response = await defaultStreamHandler({ request, router })
    response.headers.set('Cache-Control', 'public, max-age=3600')
    return response
  }

  return defaultStreamHandler({ request, router })
})
```

## Common Patterns

### Authentication Context

```typescript
// app/ssr.tsx
export default createStartHandler({
  createRouter,
  getRouterManifest,
})(async ({ request, router }) => {
  // Get user from session
  const user = await getUserFromSession(request)

  // Pass to router
  router.update({
    context: {
      user,
      isAuthenticated: !!user,
    },
  })

  return defaultStreamHandler({ request, router })
})
```

### Request Logging

```typescript
// app/ssr.tsx
export default createStartHandler({
  createRouter,
  getRouterManifest,
})(async ({ request, router }) => {
  const start = Date.now()

  const response = await defaultStreamHandler({ request, router })

  const duration = Date.now() - start
  console.log(`${request.method} ${request.url} - ${duration}ms`)

  return response
})
```

### Error Tracking

```typescript
// app/client.tsx
import { initErrorTracking } from './lib/errorTracking'

// Initialize error tracking
const errorTracker = initErrorTracking({
  dsn: import.meta.env.VITE_SENTRY_DSN,
})

// Create router with error handling
const router = createRouter()

// Track navigation errors
router.subscribe('onError', (error) => {
  errorTracker.captureException(error)
})

hydrateRoot(document, <StartClient router={router} />)
```

## Exercises

### Exercise 1: Custom Server Context

1. Add a request timestamp to server context
2. Pass it through the router
3. Display it in a route component

### Exercise 2: Client-Side Analytics

1. Initialize analytics in `client.tsx`
2. Track route changes
3. Send page view events

### Exercise 3: Global Loading State

1. Add a global loading indicator
2. Show it during navigation
3. Style it nicely

### Exercise 4: Error Boundary

1. Create a global error boundary
2. Catch errors in routes
3. Display user-friendly messages

## Debugging Tips

### Inspecting Router State

```typescript
// In browser console
window.__router = router

// Then you can inspect:
__router.state.location.pathname
__router.state.matches
__router.state.isLoading
```

### Logging SSR Output

```typescript
// app/ssr.tsx
export default createStartHandler({
  createRouter,
  getRouterManifest,
})(async ({ request, router }) => {
  console.log('SSR Request:', {
    url: request.url,
    method: request.method,
    headers: Object.fromEntries(request.headers),
  })

  return defaultStreamHandler({ request, router })
})
```

### React DevTools

The React DevTools browser extension works great with TanStack Start. Install it to inspect component trees and props.

## Summary

You now understand:

- The three entry points (SSR, client, router)
- How server-side rendering works
- How client-side hydration happens
- How to configure the router
- How to add context and services
- Development vs production considerations

## Next Steps

Continue to [Lesson 4: Build and Development Modes](./lesson-04-build-modes.md) to understand the build process.

## Further Reading

- [TanStack Start SSR Guide](https://tanstack.com/start/latest/docs/ssr)
- [React Hydration](https://react.dev/reference/react-dom/client/hydrateRoot)
- [TanStack Router API](https://tanstack.com/router/latest/docs/api)
