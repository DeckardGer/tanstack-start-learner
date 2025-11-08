# Lesson 4: Build and Development Modes

## Introduction

TanStack Start operates differently in development and production. Understanding these modes helps you develop efficiently and deploy optimized applications. Let's explore the differences and how to leverage each mode effectively.

## Development Mode

Development mode prioritizes developer experience with fast refresh, detailed errors, and helpful warnings.

### Starting Development Mode

```bash
# Start development server
npm run dev

# Or with custom port
PORT=3001 npm run dev

# With debugging
NODE_OPTIONS='--inspect' npm run dev
```

### What Happens in Dev Mode?

```
1. Vinxi starts development server
         ↓
2. Vite transforms files on-demand
         ↓
3. File watcher monitors changes
         ↓
4. Hot Module Replacement (HMR) active
         ↓
5. Source maps enabled
         ↓
6. App ready at http://localhost:3000
```

### Development Features

#### 1. Hot Module Replacement (HMR)

Changes reflect instantly without full page reload:

```typescript
// app/routes/index.tsx
export const Route = createFileRoute('/')({
  component: Home,
})

function Home() {
  return (
    <div>
      <h1>Hello World</h1>
      {/* Change this text and see it update instantly! */}
    </div>
  )
}
```

**What gets HMR?**
- React components
- CSS files
- Most TypeScript files

**What requires full reload?**
- Route configuration changes
- App config changes
- New dependencies added

#### 2. Detailed Error Messages

Development mode shows helpful errors:

```typescript
// This will show a detailed error overlay
function BrokenComponent() {
  const data = undefined
  return <div>{data.name}</div> // Error: Cannot read property 'name' of undefined
}
```

**Error overlay shows:**
- Stack trace
- Source code location
- Suggestions to fix

#### 3. React DevTools Integration

React DevTools work automatically in development:

```typescript
// Components have readable names
function UserProfile({ userId }) {
  return <div>User: {userId}</div>
}
// Shows as "UserProfile" in DevTools
```

#### 4. TanStack Query DevTools

Automatically enabled in development:

```typescript
// app/routes/__root.tsx
import { TanStackRouterDevtools } from '@tanstack/router-devtools'
import { ReactQueryDevtools } from '@tanstack/react-query-devtools'

export const Route = createRootRoute({
  component: RootComponent,
})

function RootComponent() {
  return (
    <html>
      <body>
        <Outlet />

        {/* Only show in development */}
        {import.meta.env.DEV && (
          <>
            <TanStackRouterDevtools />
            <ReactQueryDevtools />
          </>
        )}
      </body>
    </html>
  )
}
```

#### 5. Unoptimized Bundles

Development bundles are larger but faster to build:

```
Development Bundle:
- Readable code
- Source maps included
- No minification
- No tree shaking
- Development warnings included

Size: ~2MB
Build time: 500ms
```

### Development Configuration

```typescript
// app.config.ts
import { defineConfig } from '@tanstack/start/config'

export default defineConfig({
  vite: {
    server: {
      // Development server options
      port: 3000,
      strictPort: false,
      open: true, // Open browser automatically
      cors: true,

      // HMR options
      hmr: {
        overlay: true, // Show error overlay
      },

      // Proxy API requests
      proxy: {
        '/api': {
          target: 'http://localhost:3001',
          changeOrigin: true,
          rewrite: (path) => path.replace(/^\/api/, ''),
        },
      },
    },

    // Development optimizations
    optimizeDeps: {
      include: ['react', 'react-dom'],
    },
  },
})
```

### Development Workflow

#### 1. Start Server

```bash
npm run dev
```

```
  VITE v5.0.0  ready in 234 ms

  ➜  Local:   http://localhost:3000/
  ➜  Network: http://192.168.1.100:3000/
  ➜  press h to show help
```

#### 2. Make Changes

```typescript
// Edit any file
// Changes apply instantly via HMR
```

#### 3. Check Console

```typescript
// Development logs are visible
console.log('Debug info:', data)
console.warn('This might be an issue')
console.error('Error:', error)
```

#### 4. Use DevTools

- React DevTools: Inspect component tree
- Network tab: Check requests
- Console: View logs and errors
- TanStack DevTools: Query cache, router state

## Production Mode

Production mode creates optimized builds for deployment.

### Building for Production

```bash
# Build the application
npm run build

# Output in .output/ directory
```

### What Happens During Build?

```
1. Type checking (TypeScript)
         ↓
2. Bundle application code
         ↓
3. Minify JavaScript & CSS
         ↓
4. Generate source maps
         ↓
5. Optimize assets
         ↓
6. Extract CSS
         ↓
7. Tree shake unused code
         ↓
8. Create .output/ directory
```

### Production Optimizations

#### 1. Code Minification

```javascript
// Before minification (development)
function calculateTotal(items) {
  return items.reduce((sum, item) => {
    return sum + item.price * item.quantity
  }, 0)
}

// After minification (production)
function c(t){return t.reduce((s,i)=>s+i.price*i.quantity,0)}
```

#### 2. Tree Shaking

Removes unused code:

```typescript
// You import:
import { Button } from '@/components/ui'

// Only Button code is included
// Other components in ui/ are removed
```

#### 3. Code Splitting

Automatically splits code into chunks:

```
.output/public/assets/
├── index-abc123.js        # Main bundle
├── route-about-def456.js  # /about route
├── route-blog-ghi789.js   # /blog route
└── vendor-jkl012.js       # node_modules
```

#### 4. Asset Optimization

```
Images:  Optimized and cached
CSS:     Minified and extracted
Fonts:   Subset and compressed
```

#### 5. Production Bundle

```
Production Bundle:
- Minified code
- Source maps (optional)
- Tree shaken
- Code split
- Development code removed

Size: ~200KB (10x smaller!)
Build time: 15s
```

### Production Configuration

```typescript
// app.config.ts
import { defineConfig } from '@tanstack/start/config'

export default defineConfig({
  vite: {
    build: {
      // Source maps for debugging (optional)
      sourcemap: true,

      // Minification
      minify: 'esbuild', // or 'terser'

      // Chunk size warnings
      chunkSizeWarningLimit: 1000,

      // Rollup options
      rollupOptions: {
        output: {
          // Manual chunks for better caching
          manualChunks: {
            'react-vendor': ['react', 'react-dom'],
            'router-vendor': ['@tanstack/react-router'],
            'query-vendor': ['@tanstack/react-query'],
          },
        },
      },

      // Target browsers
      target: 'es2020',
    },
  },
})
```

### Running Production Build

```bash
# Build first
npm run build

# Then start production server
npm run start
```

```
Production server running at:
➜ http://localhost:3000
```

### Production Features

#### 1. No DevTools

Development tools are stripped:

```typescript
// This code is removed in production
if (import.meta.env.DEV) {
  console.log('Development log')  // Removed
  window.__DEBUG__ = router        // Removed
}

// This stays
if (import.meta.env.PROD) {
  // Production analytics
  initAnalytics()
}
```

#### 2. Error Handling

Less detailed errors (for security):

```typescript
// Development: Full stack trace
Error: Cannot read property 'name' of undefined
  at UserProfile (UserProfile.tsx:10:25)
  at div
  at App (App.tsx:15:3)
  ...

// Production: Generic message
Error: Application error occurred
```

Implement proper error tracking:

```typescript
// app/client.tsx
import * as Sentry from '@sentry/react'

if (import.meta.env.PROD) {
  Sentry.init({
    dsn: import.meta.env.VITE_SENTRY_DSN,
    environment: 'production',
  })
}
```

#### 3. Caching Headers

Set appropriate cache headers:

```typescript
// app/ssr.tsx
export default createStartHandler({
  createRouter,
  getRouterManifest,
})(async ({ request, router }) => {
  const response = await defaultStreamHandler({ request, router })

  if (import.meta.env.PROD) {
    // Cache static assets for 1 year
    if (request.url.includes('/assets/')) {
      response.headers.set('Cache-Control', 'public, max-age=31536000, immutable')
    }
    // Cache pages for 1 hour
    else {
      response.headers.set('Cache-Control', 'public, max-age=3600')
    }
  }

  return response
})
```

#### 4. Performance Monitoring

```typescript
// app/client.tsx
import { onCLS, onFID, onLCP } from 'web-vitals'

if (import.meta.env.PROD) {
  // Track Core Web Vitals
  onCLS(console.log)
  onFID(console.log)
  onLCP(console.log)
}
```

## Environment Variables

### Using Environment Variables

```bash
# .env
VITE_API_URL=http://localhost:3001
VITE_APP_NAME=My App

# .env.production
VITE_API_URL=https://api.production.com
```

**Access in code**:

```typescript
const apiUrl = import.meta.env.VITE_API_URL
const appName = import.meta.env.VITE_APP_NAME
const isDev = import.meta.env.DEV
const isProd = import.meta.env.PROD
```

**Important**: Prefix with `VITE_` for client-side access!

### Server-Only Variables

```bash
# .env
DATABASE_URL=postgresql://...  # Server only
SECRET_KEY=abc123              # Server only
VITE_PUBLIC_KEY=xyz789         # Client accessible
```

```typescript
// Server code (app/ssr.tsx)
const dbUrl = process.env.DATABASE_URL  // ✅ Works

// Client code (app/client.tsx)
const dbUrl = process.env.DATABASE_URL  // ❌ Undefined
const publicKey = import.meta.env.VITE_PUBLIC_KEY  // ✅ Works
```

## Preview Mode

Test production build locally:

```bash
# Build
npm run build

# Start in production mode
npm run start

# Or use preview
npm run preview
```

This simulates production environment locally.

## Comparing Modes

| Feature | Development | Production |
|---------|-------------|------------|
| Build Time | Fast (500ms) | Slower (15s) |
| Bundle Size | Large (2MB) | Small (200KB) |
| Source Maps | Yes | Optional |
| Minification | No | Yes |
| HMR | Yes | No |
| Error Details | Full | Limited |
| DevTools | Enabled | Disabled |
| Performance | Slower | Optimized |

## Conditional Code

### Development Only

```typescript
if (import.meta.env.DEV) {
  // Only runs in development
  console.log('Debug:', data)
  window.__DEBUG__ = { router, queryClient }
}
```

### Production Only

```typescript
if (import.meta.env.PROD) {
  // Only runs in production
  initAnalytics()
  initErrorTracking()
}
```

### By Environment Variable

```typescript
if (import.meta.env.VITE_ENABLE_FEATURE === 'true') {
  // Enable feature based on env var
}
```

## Build Optimization Tips

### 1. Analyze Bundle Size

```bash
# Install analyzer
npm install -D rollup-plugin-visualizer

# Build with analysis
npm run build -- --analyze
```

```typescript
// vite.config.ts
import { visualizer } from 'rollup-plugin-visualizer'

export default defineConfig({
  plugins: [
    visualizer({
      open: true,
      gzipSize: true,
    }),
  ],
})
```

### 2. Lazy Load Routes

```typescript
// app/routes/admin.tsx
export const Route = createFileRoute('/admin')({
  // Lazy load component
  component: () => import('./admin-component').then(m => m.AdminComponent),
})
```

### 3. Split Vendor Chunks

```typescript
// app.config.ts
export default defineConfig({
  vite: {
    build: {
      rollupOptions: {
        output: {
          manualChunks: {
            'react-vendor': ['react', 'react-dom'],
            'tanstack-vendor': [
              '@tanstack/react-router',
              '@tanstack/react-query',
            ],
          },
        },
      },
    },
  },
})
```

### 4. Remove Unused Dependencies

```bash
# Find unused dependencies
npx depcheck

# Remove them
npm uninstall unused-package
```

## Debugging Production Issues

### Enable Source Maps

```typescript
// app.config.ts
export default defineConfig({
  vite: {
    build: {
      sourcemap: true, // Enable in production
    },
  },
})
```

### Add Error Tracking

```typescript
// app/client.tsx
import * as Sentry from '@sentry/react'

Sentry.init({
  dsn: import.meta.env.VITE_SENTRY_DSN,
  environment: import.meta.env.MODE,
  tracesSampleRate: 1.0,
})
```

### Logging

```typescript
// app/utils/logger.ts
export const logger = {
  info: (message: string, data?: unknown) => {
    if (import.meta.env.DEV) {
      console.log(message, data)
    } else {
      // Send to logging service
      sendToLoggingService('info', message, data)
    }
  },
  error: (message: string, error?: Error) => {
    if (import.meta.env.DEV) {
      console.error(message, error)
    } else {
      // Send to error tracking
      Sentry.captureException(error)
    }
  },
}
```

## Deployment Checklist

Before deploying to production:

- [ ] Run `npm run build` successfully
- [ ] Test production build locally with `npm run start`
- [ ] Check bundle sizes
- [ ] Verify environment variables
- [ ] Test error boundaries
- [ ] Check performance metrics
- [ ] Enable error tracking
- [ ] Set up monitoring
- [ ] Configure caching headers
- [ ] Test in target browsers
- [ ] Security audit: `npm audit`

## Exercises

### Exercise 1: Development Setup

1. Start dev server
2. Make a component change
3. Observe HMR in action
4. Check DevTools

### Exercise 2: Production Build

1. Build for production
2. Compare bundle sizes
3. Test production server
4. Check performance

### Exercise 3: Environment Variables

1. Create `.env.local`
2. Add variables
3. Use in component
4. Test in dev and prod

### Exercise 4: Bundle Analysis

1. Install visualizer
2. Build with analysis
3. Identify large dependencies
4. Optimize bundle

## Summary

You now understand:

- Development vs production modes
- Hot Module Replacement (HMR)
- Build process and optimizations
- Environment variables
- Bundle optimization
- Production deployment checklist

## Congratulations!

You've completed Module 3! You now have a deep understanding of:
- Project structure
- Configuration files
- Entry points
- Build modes

## Next Steps

Continue to [Module 4: Routing Fundamentals](../module-04-routing-fundamentals/README.md) to master TanStack Router!

## Further Reading

- [Vite Build Optimizations](https://vitejs.dev/guide/build.html)
- [Production Best Practices](https://react.dev/learn/production-builds)
- [Web Vitals](https://web.dev/vitals/)
- [Bundle Analysis](https://github.com/btd/rollup-plugin-visualizer)
