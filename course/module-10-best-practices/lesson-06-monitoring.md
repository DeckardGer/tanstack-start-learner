# Lesson 6: Monitoring and Analytics

## Error Tracking with Sentry

```typescript
// app/client.tsx
import * as Sentry from '@sentry/react'

if (import.meta.env.PROD) {
  Sentry.init({
    dsn: import.meta.env.VITE_SENTRY_DSN,
    environment: 'production',
    tracesSampleRate: 1.0,
  })
}
```

## Analytics

```typescript
// app/lib/analytics.ts
export function trackPageView(url: string) {
  if (typeof window !== 'undefined' && window.gtag) {
    window.gtag('config', 'GA_MEASUREMENT_ID', {
      page_path: url,
    })
  }
}

// Use in routes
export const Route = createFileRoute('/posts/$postId')({
  component: PostPage,
  onEnter: ({ location }) => {
    trackPageView(location.pathname)
  },
})
```

## Performance Monitoring

```typescript
// Track Core Web Vitals
import { onCLS, onFID, onLCP } from 'web-vitals'

onCLS(console.log)
onFID(console.log)
onLCP(console.log)
```

## Logging

```typescript
export const logger = {
  info: (message: string, data?: unknown) => {
    if (import.meta.env.PROD) {
      // Send to logging service
      sendLog('info', message, data)
    } else {
      console.log(message, data)
    }
  },
  error: (message: string, error?: Error) => {
    if (import.meta.env.PROD) {
      Sentry.captureException(error)
    } else {
      console.error(message, error)
    }
  },
}
```

## Summary

Comprehensive monitoring ensures your application stays healthy in production.

## Congratulations!

You've completed all 10 modules of the TanStack Start course! You now have:

- Deep understanding of TanStack Start
- Mastery of routing and data fetching
- Server-side rendering expertise
- Full-stack development skills
- Production-ready knowledge

Continue building amazing applications with TanStack Start!
