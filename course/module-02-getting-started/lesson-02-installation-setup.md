# Lesson 2: Installation & Setup

## Introduction

In this lesson, you'll learn how to install TanStack Start and set up your development environment.

## Installation Methods

There are three main ways to get started with TanStack Start:

1. **CLI (Recommended)** - Fastest way to get started
2. **Clone Example** - Start with a working example
3. **Manual Setup** - Build from scratch for learning

## Method 1: Using the CLI (Recommended)

The CLI is the fastest and easiest way to create a new project.

### Step 1: Run the Create Command

```bash
npm create @tanstack/start@latest
```

Or with your preferred package manager:

```bash
# Using pnpm
pnpm create @tanstack/start@latest

# Using yarn
yarn create @tanstack/start@latest
```

### Step 2: Answer the Prompts

The CLI will ask you several questions:

```bash
? Project name: › my-tanstack-app
? Select a template: › Basic (recommended)
? Install dependencies? › Yes
? Initialize git repository? › Yes
? TypeScript or JavaScript? › TypeScript (recommended)
```

**Template Options**:
- **Basic**: Minimal setup - best for learning
- **With Auth**: Includes authentication example
- **Full-Stack**: Complete example with database
- **Blog**: Pre-configured blog template

### Step 3: Navigate and Start

```bash
cd my-tanstack-app
npm run dev
```

Your app is now running at `http://localhost:3000`!

## Method 2: Clone an Example

Start with an official example:

```bash
# Clone the basic example
npx gitpick TanStack/router/tree/main/examples/react/start-basic my-app

# Navigate into the project
cd my-app

# Install dependencies
npm install

# Start development server
npm run dev
```

**Available Examples**:
- `start-basic` - Minimal setup
- `start-trpc` - With tRPC integration
- `start-clerk-basic` - With Clerk authentication
- `start-convex` - With Convex backend

## Method 3: Manual Installation

For understanding how it all works, you can set up manually.

### Step 1: Create Project Directory

```bash
mkdir my-tanstack-app
cd my-tanstack-app
npm init -y
```

### Step 2: Install Dependencies

```bash
# Core dependencies
npm install @tanstack/start @tanstack/react-router @tanstack/react-query

# React dependencies
npm install react react-dom

# Development dependencies
npm install -D @vitejs/plugin-react vite typescript @types/react @types/react-dom
```

### Step 3: Create Configuration Files

**`tsconfig.json`**:
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true
  },
  "include": ["app"]
}
```

**`vite.config.ts`**:
```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import { TanStackStartVite } from '@tanstack/start/vite'

export default defineConfig({
  plugins: [
    react(),
    TanStackStartVite(),
  ],
})
```

**`package.json`** - Add scripts:
```json
{
  "scripts": {
    "dev": "vinxi dev",
    "build": "vinxi build",
    "start": "vinxi start"
  }
}
```

### Step 4: Create App Structure

```bash
mkdir -p app/routes
```

**`app/routes/__root.tsx`**:
```typescript
import { createRootRoute, Outlet } from '@tanstack/react-router'

export const Route = createRootRoute({
  component: () => (
    <div>
      <h1>My TanStack Start App</h1>
      <Outlet />
    </div>
  ),
})
```

**`app/routes/index.tsx`**:
```typescript
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/')({
  component: Home,
})

function Home() {
  return (
    <div>
      <h2>Welcome to TanStack Start!</h2>
      <p>Your app is running.</p>
    </div>
  )
}
```

**`app/router.tsx`**:
```typescript
import { createRouter } from '@tanstack/react-router'
import { routeTree } from './routeTree.gen'

export const router = createRouter({ routeTree })

declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router
  }
}
```

**`app/client.tsx`**:
```typescript
import { StartClient } from '@tanstack/start'
import { createRouter } from './router'

const router = createRouter()

function App() {
  return <StartClient router={router} />
}

App.mount(document.getElementById('root')!)
```

**`app/server.tsx`**:
```typescript
import { createStartHandler, defaultStreamHandler } from '@tanstack/start/server'
import { getRouterManifest } from '@tanstack/start/router-manifest'
import { createRouter } from './router'

export default createStartHandler({
  createRouter,
  getRouterManifest,
})(defaultStreamHandler)
```

### Step 5: Generate Route Tree

```bash
npx tsx scripts/generate-route-tree.ts
```

### Step 6: Start Development

```bash
npm run dev
```

## Understanding the Installation

### Package Breakdown

**`@tanstack/start`**:
- Main framework package
- Server-side rendering
- Bundling and build tools
- Server function support

**`@tanstack/react-router`**:
- File-based routing
- Type-safe navigation
- Route loaders
- Search param validation

**`@tanstack/react-query`**:
- Data fetching
- Caching
- Background updates
- Mutations

### Development Dependencies

**Vite**:
- Fast development server
- Hot Module Replacement (HMR)
- Production builds

**TypeScript**:
- Type checking
- Better IDE support
- Catch errors early

**Vinxi**:
- TanStack Start's build tool
- Built on top of Vite
- Handles SSR and bundling

## Project Structure After Installation

```
my-tanstack-app/
├── node_modules/
├── app/
│   ├── routes/
│   │   ├── __root.tsx
│   │   └── index.tsx
│   ├── router.tsx
│   ├── client.tsx
│   └── server.tsx
├── public/
│   └── favicon.ico
├── package.json
├── tsconfig.json
├── vite.config.ts
└── .gitignore
```

## Verification Steps

### 1. Check Installation

```bash
# Verify all packages installed
npm list @tanstack/start @tanstack/react-router @tanstack/react-query
```

### 2. Start Development Server

```bash
npm run dev
```

You should see:
```
VITE v5.x.x  ready in XXX ms

➜  Local:   http://localhost:3000/
➜  Network: use --host to expose
```

### 3. Open in Browser

Navigate to `http://localhost:3000`

You should see your app running!

### 4. Test Hot Reload

Edit `app/routes/index.tsx`:
```typescript
function Home() {
  return (
    <div>
      <h2>Hello from TanStack Start!</h2>
      <p>Hot reload is working! ✨</p>
    </div>
  )
}
```

Save the file - the browser should update automatically.

## Common Issues and Solutions

### Issue: Port Already in Use

**Error**:
```
Port 3000 is already in use
```

**Solution**:
```bash
# Use a different port
npm run dev -- --port 3001

# Or kill the process using port 3000
# macOS/Linux:
lsof -ti:3000 | xargs kill -9
# Windows:
netstat -ano | findstr :3000
taskkill /PID <PID> /F
```

### Issue: Module Not Found

**Error**:
```
Cannot find module '@tanstack/start'
```

**Solution**:
```bash
# Delete node_modules and reinstall
rm -rf node_modules package-lock.json
npm install
```

### Issue: TypeScript Errors

**Error**:
```
Cannot find name 'Route'
```

**Solution**:
```bash
# Generate route tree
npm run generate-routes

# Or if script doesn't exist
npx tsx scripts/generate-route-tree.ts
```

### Issue: Vite Build Errors

**Error**:
```
Failed to resolve entry for package
```

**Solution**:
```bash
# Clear Vite cache
rm -rf .vite node_modules/.vite

# Reinstall
npm install
```

## IDE Setup

### VS Code

**Recommended Extensions**:
1. Install TanStack Router extension (if available)
2. Enable TypeScript takeover mode

**`.vscode/settings.json`**:
```json
{
  "typescript.tsdk": "node_modules/typescript/lib",
  "typescript.enablePromptUseWorkspaceTsdk": true
}
```

### TypeScript Config

Ensure `tsconfig.json` includes:
```json
{
  "compilerOptions": {
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "strict": true
  }
}
```

## Environment Variables

Create `.env` file:
```bash
# Public variables (available in client)
VITE_API_URL=http://localhost:3000/api

# Server-only variables
DATABASE_URL=postgresql://...
API_SECRET=your-secret
```

Access in code:
```typescript
// Client-side (VITE_ prefix required)
const apiUrl = import.meta.env.VITE_API_URL

// Server-side
const secret = process.env.API_SECRET
```

## Summary

You now know how to:

✅ Install TanStack Start using the CLI
✅ Clone and use official examples
✅ Manually set up a project from scratch
✅ Understand the package structure
✅ Verify your installation
✅ Troubleshoot common issues
✅ Configure your IDE

## Next Steps

Continue to [Lesson 3: Creating Your First Project](./lesson-03-first-project.md) to build your first TanStack Start application.

## Further Reading

- [TanStack Start Installation Guide](https://tanstack.com/start/latest/docs/framework/react/getting-started)
- [Vite Configuration](https://vitejs.dev/config/)
- [TypeScript Configuration](https://www.typescriptlang.org/tsconfig)
