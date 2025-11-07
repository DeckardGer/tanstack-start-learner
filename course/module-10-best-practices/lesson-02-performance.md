# Lesson 2: Performance Optimization

## Code Splitting

```typescript
// Lazy load routes
export const Route = createFileRoute('/admin')({
  component: () => import('./AdminDashboard').then(m => m.AdminDashboard),
})
```

## Preloading

```typescript
<Link 
  to="/posts/$postId" 
  params={{ postId }}
  preload="intent"  // Preload on hover
>
  View Post
</Link>
```

## Memoization

```typescript
const expensiveValue = useMemo(() => {
  return computeExpensive(data)
}, [data])

const handleClick = useCallback(() => {
  doSomething()
}, [dependency])
```

## Image Optimization

```typescript
<img 
  src="/images/hero.jpg" 
  loading="lazy"
  width={800}
  height={600}
/>
```

## Bundle Analysis

```bash
npm run build -- --analyze
```

## Next Steps

Continue to [Lesson 3: Testing Strategies](./lesson-03-testing.md)
