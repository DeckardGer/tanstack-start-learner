# Lesson 5: Hydration and Client Transitions

## Hydration

Server HTML becomes interactive React:

```
1. Server renders HTML
2. Browser displays HTML
3. JavaScript loads
4. React "hydrates" HTML
5. App is fully interactive
```

## Hydration Process

```typescript
// app/client.tsx
import { hydrateRoot } from 'react-dom/client'
import { StartClient } from '@tanstack/start'
import { createRouter } from './router'

const router = createRouter()

// Hydrate server-rendered HTML
hydrateRoot(document, <StartClient router={router} />)
```

## Client Transitions

After hydration, navigation happens on client:

```typescript
// First page load: SSR
Browser -> Server -> HTML -> Browser

// Subsequent navigation: Client-side
Browser -> JavaScript -> Update UI
```

## Avoiding Hydration Mismatches

```typescript
// ❌ Bad - different on server/client
function Time() {
  return <div>{new Date().toString()}</div>
}

// ✅ Good - consistent
function Time() {
  const [time, setTime] = useState(null)
  
  useEffect(() => {
    setTime(new Date().toString())
  }, [])
  
  return <div>{time || 'Loading...'}</div>
}
```

## Summary

TanStack Start provides excellent SSR with seamless client hydration.

Module 8 Complete! Next: [Module 9: Full-Stack Patterns](../module-09-full-stack-patterns/README.md)
