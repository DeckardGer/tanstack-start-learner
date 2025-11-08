# Lesson 2: Configuration Files Explained

## Introduction

Configuration files control how your TanStack Start application is built, type-checked, and deployed. Understanding these files empowers you to customize your project for different environments and use cases.

## `app.config.ts` - TanStack Start Configuration

This is the main configuration file for TanStack Start. It controls server settings, build options, and deployment targets.

### Basic Configuration

```typescript
// app.config.ts
import { defineConfig } from '@tanstack/start/config'

export default defineConfig({
  // Server configuration
  server: {
    preset: 'node-server',
  },
})
```

### Server Presets

Choose where your app will be deployed:

```typescript
export default defineConfig({
  server: {
    // Node.js server (default)
    preset: 'node-server',

    // Or choose deployment platform:
    // preset: 'vercel',
    // preset: 'netlify',
    // preset: 'cloudflare-pages',
    // preset: 'aws-lambda',
  },
})
```

**Available Presets**:

1. **`node-server`** - Traditional Node.js server
   ```typescript
   server: {
     preset: 'node-server',
     port: 3000,
     host: 'localhost',
   }
   ```
   - Best for: VPS, Docker, traditional hosting
   - Pros: Full control, any Node.js features
   - Cons: You manage scaling and infrastructure

2. **`vercel`** - Vercel serverless
   ```typescript
   server: {
     preset: 'vercel',
   }
   ```
   - Best for: Quick deployment, automatic scaling
   - Pros: Zero config deployment, CDN, preview URLs
   - Cons: Vendor lock-in, cold starts

3. **`netlify`** - Netlify Functions
   ```typescript
   server: {
     preset: 'netlify',
   }
   ```
   - Best for: JAMstack apps, static sites with API
   - Pros: Great DX, form handling, split testing
   - Cons: Function timeout limits

4. **`cloudflare-pages`** - Cloudflare Workers
   ```typescript
   server: {
     preset: 'cloudflare-pages',
   }
   ```
   - Best for: Global edge deployment
   - Pros: Super fast, runs at the edge
   - Cons: Limited Node.js APIs

### Router Configuration

```typescript
export default defineConfig({
  routers: {
    // Server-side rendering router
    ssr: {
      entry: './app/ssr.tsx',
    },

    // Client-side router
    client: {
      entry: './app/client.tsx',
      base: '/',
    },

    // API router (optional)
    api: {
      entry: './app/api.ts',
      base: '/api',
    },
  },
})
```

### Vite Integration

Customize the underlying Vite configuration:

```typescript
import { defineConfig } from '@tanstack/start/config'
import react from '@vitejs/plugin-react'

export default defineConfig({
  vite: {
    plugins: [
      // Add Vite plugins
    ],
    resolve: {
      alias: {
        '@': '/app',
      },
    },
    server: {
      port: 3000,
    },
  },
})
```

### Complete Example

```typescript
// app.config.ts
import { defineConfig } from '@tanstack/start/config'

export default defineConfig({
  server: {
    preset: process.env.DEPLOY_TARGET || 'node-server',
    port: parseInt(process.env.PORT || '3000'),
    hostname: process.env.HOST || 'localhost',
  },

  routers: {
    ssr: {
      entry: './app/ssr.tsx',
    },
    client: {
      entry: './app/client.tsx',
      base: '/',
    },
  },

  vite: {
    resolve: {
      alias: {
        '~': '/app',
        '@': '/app',
      },
    },
    server: {
      port: 3000,
      strictPort: false,
    },
    build: {
      sourcemap: process.env.NODE_ENV === 'development',
      minify: process.env.NODE_ENV === 'production',
    },
  },
})
```

## `tsconfig.json` - TypeScript Configuration

Controls TypeScript compilation and type checking.

### Basic Configuration

```json
{
  "compilerOptions": {
    // Language & Environment
    "target": "ES2020",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "jsx": "react-jsx",

    // Module System
    "module": "ESNext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,

    // Type Checking
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,

    // Interop
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "isolatedModules": true,

    // Output
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,

    // Path Mapping
    "baseUrl": ".",
    "paths": {
      "~/*": ["./app/*"],
      "@/components/*": ["./app/components/*"],
      "@/features/*": ["./app/features/*"],
      "@/utils/*": ["./app/utils/*"],
      "@/types/*": ["./app/types/*"]
    }
  },
  "include": [
    "app/**/*",
    "vite.config.ts",
    "app.config.ts"
  ],
  "exclude": [
    "node_modules",
    ".vinxi",
    ".output"
  ]
}
```

### Key Options Explained

#### Language Options

```json
{
  "compilerOptions": {
    // What JS version to output
    "target": "ES2020",

    // What built-in types are available
    "lib": ["ES2020", "DOM", "DOM.Iterable"],

    // How to transform JSX
    "jsx": "react-jsx"  // Modern (no React import needed)
    // "jsx": "react"   // Legacy (requires React import)
  }
}
```

#### Module Resolution

```json
{
  "compilerOptions": {
    // How modules are loaded
    "module": "ESNext",

    // How to find modules
    "moduleResolution": "bundler",  // Recommended for Vite
    // "moduleResolution": "node"   // Traditional Node.js

    // Allow importing .json files
    "resolveJsonModule": true
  }
}
```

#### Strict Type Checking

```json
{
  "compilerOptions": {
    // Enable ALL strict checks (recommended)
    "strict": true,

    // What "strict" includes:
    // "noImplicitAny": true,
    // "strictNullChecks": true,
    // "strictFunctionTypes": true,
    // "strictBindCallApply": true,
    // "strictPropertyInitialization": true,
    // "noImplicitThis": true,
    // "alwaysStrict": true,

    // Extra safety
    "noUncheckedIndexedAccess": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true
  }
}
```

**Example of strict checking**:

```typescript
// With strict: true
function greet(name: string) {
  return `Hello, ${name}!`
}

greet('Alice')     // ✅ OK
greet(undefined)   // ❌ Error: undefined not assignable to string

// Array access safety
const arr = [1, 2, 3]
const item = arr[10]  // Type: number | undefined (safe!)

// With noUncheckedIndexedAccess: true
if (item !== undefined) {
  // Now we know item is a number
  const doubled = item * 2
}
```

#### Path Mapping

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      // Map ~/* to app/*
      "~/*": ["./app/*"],

      // More specific mappings
      "@/components/*": ["./app/components/*"],
      "@/features/*": ["./app/features/*"]
    }
  }
}
```

**Usage**:

```typescript
// Without path mapping:
import { Button } from '../../../components/ui/Button'

// With path mapping:
import { Button } from '@/components/ui/Button'
// or
import { Button } from '~/components/ui/Button'
```

### Project References

For monorepos or large projects:

```json
{
  "compilerOptions": {
    "composite": true,
    "declaration": true,
    "declarationMap": true
  },
  "references": [
    { "path": "./packages/shared" },
    { "path": "./packages/ui" }
  ]
}
```

## `package.json` - Project Metadata

Defines your project's metadata, dependencies, and scripts.

### Essential Fields

```json
{
  "name": "my-tanstack-start-app",
  "version": "0.1.0",
  "type": "module",
  "private": true,

  "scripts": {
    "dev": "vinxi dev",
    "build": "vinxi build",
    "start": "vinxi start",
    "typecheck": "tsc --noEmit",
    "lint": "eslint app",
    "format": "prettier --write app"
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

### Important: `"type": "module"`

This enables ES modules in Node.js:

```javascript
// With "type": "module"
import { something } from './file.js'  // ✅ Works

// Without it (CommonJS)
const something = require('./file')    // Old way
```

### Custom Scripts

Add useful scripts:

```json
{
  "scripts": {
    // Development
    "dev": "vinxi dev",
    "dev:debug": "NODE_OPTIONS='--inspect' vinxi dev",

    // Building
    "build": "vinxi build",
    "build:analyze": "ANALYZE=true vinxi build",

    // Production
    "start": "NODE_ENV=production vinxi start",
    "preview": "vinxi build && vinxi start",

    // Type checking
    "typecheck": "tsc --noEmit",
    "typecheck:watch": "tsc --noEmit --watch",

    // Linting & Formatting
    "lint": "eslint app --ext .ts,.tsx",
    "lint:fix": "eslint app --ext .ts,.tsx --fix",
    "format": "prettier --write \"app/**/*.{ts,tsx,json,css}\"",
    "format:check": "prettier --check \"app/**/*.{ts,tsx,json,css}\"",

    // Testing
    "test": "vitest",
    "test:ui": "vitest --ui",
    "test:coverage": "vitest --coverage",

    // Validation
    "validate": "npm run typecheck && npm run lint && npm run test",

    // Clean
    "clean": "rm -rf .vinxi .output node_modules"
  }
}
```

### Dependency Management

**dependencies** - Required at runtime:
```json
{
  "dependencies": {
    "@tanstack/react-router": "^1.0.0",
    "@tanstack/react-query": "^5.0.0",
    "react": "^18.2.0",
    "zod": "^3.22.0"
  }
}
```

**devDependencies** - Only needed for development:
```json
{
  "devDependencies": {
    "@types/react": "^18.2.0",
    "typescript": "^5.0.0",
    "vite": "^5.0.0",
    "vitest": "^1.0.0",
    "prettier": "^3.0.0",
    "eslint": "^8.50.0"
  }
}
```

## `.gitignore` - Git Ignore Rules

Tell Git which files to ignore:

```gitignore
# Dependencies
node_modules/

# Build outputs
.vinxi/
.output/
dist/
build/

# Environment files
.env
.env.local
.env.*.local

# OS files
.DS_Store
Thumbs.db

# Editor files
.vscode/
.idea/
*.swp
*.swo

# Logs
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Testing
coverage/

# Temporary files
*.tmp
.cache/
```

## `.env` Files - Environment Variables

Store configuration and secrets:

### `.env` - Default values

```bash
# .env
NODE_ENV=development
PORT=3000
API_URL=http://localhost:3001
```

### `.env.local` - Local overrides (gitignored)

```bash
# .env.local
DATABASE_URL=postgresql://user:pass@localhost:5432/mydb
API_KEY=secret-key-here
SESSION_SECRET=super-secret-session-key
```

### Environment-specific files

```bash
# .env.development
API_URL=http://localhost:3001
DEBUG=true

# .env.production
API_URL=https://api.production.com
DEBUG=false

# .env.test
API_URL=http://localhost:3002
```

### Using Environment Variables

```typescript
// In server code
const apiUrl = process.env.API_URL || 'http://localhost:3000'
const apiKey = process.env.API_KEY

// Type-safe env variables
declare global {
  namespace NodeJS {
    interface ProcessEnv {
      API_URL: string
      API_KEY: string
      DATABASE_URL: string
      NODE_ENV: 'development' | 'production' | 'test'
    }
  }
}

// Now you get autocomplete and type checking!
const url: string = process.env.API_URL
```

### Validation with Zod

```typescript
// app/lib/env.ts
import { z } from 'zod'

const envSchema = z.object({
  NODE_ENV: z.enum(['development', 'production', 'test']),
  PORT: z.coerce.number().default(3000),
  API_URL: z.string().url(),
  DATABASE_URL: z.string().min(1),
  API_KEY: z.string().min(1),
})

export const env = envSchema.parse(process.env)

// Usage: env.API_URL is now type-safe and validated!
```

## `vite.config.ts` - Vite Configuration

Advanced Vite configuration (usually not needed, as `app.config.ts` covers most cases):

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],

  resolve: {
    alias: {
      '@': '/app',
    },
  },

  server: {
    port: 3000,
    strictPort: true,
    open: true,
    proxy: {
      '/api': {
        target: 'http://localhost:3001',
        changeOrigin: true,
      },
    },
  },

  build: {
    sourcemap: true,
    rollupOptions: {
      output: {
        manualChunks: {
          'react-vendor': ['react', 'react-dom'],
          'router-vendor': ['@tanstack/react-router'],
        },
      },
    },
  },
})
```

## ESLint Configuration

Code quality and consistency:

```javascript
// eslint.config.js
import js from '@eslint/js'
import typescript from '@typescript-eslint/eslint-plugin'
import tsParser from '@typescript-eslint/parser'
import react from 'eslint-plugin-react'
import reactHooks from 'eslint-plugin-react-hooks'

export default [
  js.configs.recommended,
  {
    files: ['**/*.{ts,tsx}'],
    languageOptions: {
      parser: tsParser,
      parserOptions: {
        ecmaVersion: 2020,
        sourceType: 'module',
        ecmaFeatures: {
          jsx: true,
        },
      },
    },
    plugins: {
      '@typescript-eslint': typescript,
      react,
      'react-hooks': reactHooks,
    },
    rules: {
      'react/react-in-jsx-scope': 'off',
      'react-hooks/rules-of-hooks': 'error',
      'react-hooks/exhaustive-deps': 'warn',
      '@typescript-eslint/no-unused-vars': 'warn',
    },
  },
]
```

## Prettier Configuration

Code formatting:

```javascript
// prettier.config.js
export default {
  semi: false,
  singleQuote: true,
  trailingComma: 'es5',
  tabWidth: 2,
  printWidth: 80,
  arrowParens: 'avoid',
}
```

Or use `.prettierrc`:

```json
{
  "semi": false,
  "singleQuote": true,
  "trailingComma": "es5",
  "tabWidth": 2,
  "printWidth": 80,
  "arrowParens": "avoid"
}
```

## Exercises

### Exercise 1: Configure for Vercel

1. Change `app.config.ts` to use Vercel preset
2. Add environment variables
3. Test locally

### Exercise 2: Add Path Aliases

1. Add custom path aliases to `tsconfig.json`
2. Update imports to use aliases
3. Verify TypeScript recognizes them

### Exercise 3: Environment Variables

1. Create `.env.local`
2. Add API URL and keys
3. Create type-safe env validation
4. Use in your app

### Exercise 4: Custom Scripts

1. Add lint and format scripts
2. Add a validation script
3. Run all scripts

## Summary

You now understand:

- How to configure TanStack Start for different deployments
- TypeScript configuration options
- Package.json scripts and dependencies
- Environment variables and security
- Linting and formatting setup

## Next Steps

Continue to [Lesson 3: Entry Points and App Structure](./lesson-03-entry-points.md) to understand how your app initializes.

## Further Reading

- [TanStack Start Configuration](https://tanstack.com/start/latest/docs/configuration)
- [TypeScript tsconfig Reference](https://www.typescriptlang.org/tsconfig)
- [Vite Configuration](https://vitejs.dev/config/)
- [Environment Variables Best Practices](https://12factor.net/config)
