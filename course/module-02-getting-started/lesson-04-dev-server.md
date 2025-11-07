# Lesson 4: Understanding the Development Server

## Introduction

The TanStack Start development server is powered by Vite and Vinxi, providing a fast, modern development experience. This lesson explores how it works and how to use it effectively.

## Starting the Development Server

### Basic Command

```bash
npm run dev
```

This command:
1. Starts the Vite development server
2. Compiles your TypeScript/JSX
3. Enables Hot Module Replacement (HMR)
4. Starts the SSR server
5. Opens the application (usually on port 3000)

### Output Explained

```bash
VITE v5.0.0  ready in 342 ms

➜  Local:   http://localhost:3000/
➜  Network: http://192.168.1.100:3000/
➜  press h + enter to show help
```

- **Local**: Access from your machine
- **Network**: Access from other devices on your network
- **Ready time**: How long it took to start

## Development Server Features

### 1. Hot Module Replacement (HMR)

HMR updates your app without full page reload:

**Example**: Edit a component:
```typescript
function Home() {
  return <div>Hello World!</div> // Change this
}
```

Save the file → Browser updates instantly without losing state!

**How it works**:
1. Vite detects file change
2. Sends update to browser via WebSocket
3. React reconciles the changes
4. Component updates in place

**What triggers HMR**:
- Component changes
- Style changes
- Route changes
- Most code changes

**What causes full reload**:
- Configuration changes
- Router structure changes
- Sometimes server function changes

### 2. Fast Refresh

React Fast Refresh preserves component state during updates:

```typescript
function Counter() {
  const [count, setCount] = useState(0)

  return (
    <div>
      <p>Count: {count}</p> {/* Edit this - count persists! */}
      <button onClick={() => setCount(c => c + 1)}>
        Increment
      </button>
    </div>
  )
}
```

Edit the JSX → Count value stays the same!

### 3. Error Overlay

When errors occur, you see a helpful overlay:

```typescript
function Broken() {
  throw new Error('Something went wrong!')
}
```

The overlay shows:
- Error message
- Stack trace with source maps
- Exact file and line number
- Click to open in editor

### 4. TypeScript Checking

TypeScript errors appear in:
- Terminal
- Browser overlay
- IDE

```typescript
// TypeScript catches this immediately
const name: string = 123 // Error: Type 'number' is not assignable to type 'string'
```

## Server Configuration

### Port Configuration

**Change the port**:

```bash
# Command line
npm run dev -- --port 3001

# Or in package.json
{
  "scripts": {
    "dev": "vinxi dev --port 3001"
  }
}
```

**Environment variable**:
```bash
PORT=3001 npm run dev
```

### Host Configuration

**Expose to network**:
```bash
npm run dev -- --host
```

Now accessible from other devices:
```
➜  Network: http://192.168.1.100:3000/
```

### HTTPS in Development

**Why HTTPS**:
- Test secure features (Service Workers, etc.)
- Match production environment
- Test third-party integrations

**Setup**:
```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import basicSsl from '@vitejs/plugin-basic-ssl'

export default defineConfig({
  plugins: [
    basicSsl(), // Generates self-signed certificate
  ],
})
```

```bash
npm install -D @vitejs/plugin-basic-ssl
npm run dev
# Now runs on https://localhost:3000
```

## Development Tools

### 1. TanStack Router DevTools

Automatically included in development:

```typescript
import { TanStackRouterDevtools } from '@tanstack/router-devtools'

function RootComponent() {
  return (
    <>
      <Outlet />
      <TanStackRouterDevtools /> {/* Only in development */}
    </>
  )
}
```

**Features**:
- View route tree
- Inspect current route
- See route params and search params
- Navigate to any route
- View route loaders

**Toggle**: Click the TanStack icon in the bottom corner

### 2. TanStack Query DevTools

Included with TanStack Query:

```typescript
import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import { ReactQueryDevtools } from '@tanstack/react-query-devtools'

const queryClient = new QueryClient()

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      {/* Your app */}
      <ReactQueryDevtools initialIsOpen={false} />
    </QueryClientProvider>
  )
}
```

**Features**:
- View all queries
- See cache contents
- Trigger refetches
- Inspect query states
- View mutations
- Clear cache

### 3. React DevTools

Browser extension:
- Inspect component tree
- View props and state
- Profile performance
- Track re-renders

**Install**:
- [Chrome Extension](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
- [Firefox Extension](https://addons.mozilla.org/en-US/firefox/addon/react-devtools/)

## Environment Variables

### Development Variables

Create `.env.development`:
```bash
# Public (available in client)
VITE_API_URL=http://localhost:3000/api
VITE_APP_NAME=My App Dev

# Private (server only)
DATABASE_URL=postgresql://localhost/myapp_dev
API_SECRET_KEY=dev-secret-key
```

**Access in code**:
```typescript
// Client (VITE_ prefix required)
const apiUrl = import.meta.env.VITE_API_URL

// Server
const dbUrl = process.env.DATABASE_URL
```

### Environment Files

```
.env                 # Loaded in all cases
.env.local           # Loaded in all cases, ignored by git
.env.development     # Only in development
.env.production      # Only in production
```

**Priority**: `.env.local` > `.env.development` > `.env`

## Debugging

### Browser Debugging

**Console Logging**:
```typescript
function MyComponent() {
  console.log('Component rendering')

  useEffect(() => {
    console.log('Effect running')
  }, [])

  return <div>Hello</div>
}
```

**Debugger Statement**:
```typescript
function handleClick() {
  debugger // Execution pauses here
  console.log('After debugger')
}
```

**Browser DevTools**:
1. Open DevTools (F12)
2. Go to Sources tab
3. Set breakpoints
4. Step through code

### Server Debugging

**Node.js Inspector**:
```json
{
  "scripts": {
    "dev:debug": "node --inspect node_modules/.bin/vinxi dev"
  }
}
```

```bash
npm run dev:debug
# Open chrome://inspect in Chrome
# Click "inspect" to debug server code
```

**Logging**:
```typescript
export const myServerFn = createServerFn('GET', async () => {
  console.log('This logs in terminal')
  return { data: 'something' }
})
```

## Performance Monitoring

### Build Time

Watch for slow builds:
```bash
VITE v5.0.0  ready in 2847 ms  # Slow!
```

**Improve**:
- Remove unused dependencies
- Optimize barrel exports
- Use dynamic imports

### HMR Performance

If HMR is slow:
```typescript
// Instead of barrel exports
import { Button, Input, Modal } from '@/components' // Slow

// Use direct imports
import { Button } from '@/components/Button' // Fast
```

### Memory Usage

Monitor with:
```bash
# macOS/Linux
ps aux | grep node

# Windows
tasklist | findstr node
```

## Common Development Tasks

### Clear Cache

**Vite cache**:
```bash
rm -rf .vite node_modules/.vite
```

**npm cache**:
```bash
npm cache clean --force
```

**Full reset**:
```bash
rm -rf node_modules .vite package-lock.json
npm install
```

### Restart Server

Sometimes you need to restart:
- After installing dependencies
- After config changes
- If HMR stops working

**Quick restart**: Ctrl+C then `npm run dev`

### Check for Updates

```bash
npm outdated
npm update
```

## Network Debugging

### View Network Requests

Browser DevTools → Network tab shows:
- Page loads
- API calls
- Asset loads
- WebSocket connections (HMR)

### Proxy Configuration

Proxy API requests in development:

```typescript
// vite.config.ts
export default defineConfig({
  server: {
    proxy: {
      '/api': {
        target: 'http://api.example.com',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, '')
      }
    }
  }
})
```

Now `/api/users` → `http://api.example.com/users`

## Troubleshooting

### Server Won't Start

**Port in use**:
```bash
# Find process
lsof -ti:3000  # macOS/Linux
netstat -ano | findstr :3000  # Windows

# Kill it
kill -9 <PID>  # macOS/Linux
taskkill /PID <PID> /F  # Windows
```

**Dependencies**:
```bash
rm -rf node_modules package-lock.json
npm install
```

### HMR Not Working

1. Check browser console for errors
2. Look for WebSocket connection errors
3. Restart dev server
4. Clear browser cache

### TypeScript Errors

```bash
# Check TypeScript
npx tsc --noEmit

# Restart TypeScript server in VS Code
Cmd/Ctrl + Shift + P → "TypeScript: Restart TS Server"
```

## Summary

The development server provides:

✅ **Fast startup** with Vite
✅ **Hot Module Replacement** for instant updates
✅ **React Fast Refresh** to preserve state
✅ **Built-in DevTools** for Router and Query
✅ **TypeScript checking** in real-time
✅ **Error overlays** with source maps
✅ **Environment variables** support
✅ **Network access** for device testing

## Next Steps

Continue to [Module 3: Project Structure](../module-03-project-structure/README.md) to understand how TanStack Start projects are organized.

## Further Reading

- [Vite Features](https://vitejs.dev/guide/features.html)
- [Vite Config Reference](https://vitejs.dev/config/)
- [React Fast Refresh](https://react.dev/learn/fast-refresh)
- [Environment Variables](https://vitejs.dev/guide/env-and-mode.html)
