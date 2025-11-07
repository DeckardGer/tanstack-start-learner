# Lesson 5: Refetching and Background Updates

## Automatic Refetching

TanStack Query refetches automatically in these scenarios:

1. **Window Focus** - User returns to tab
2. **Network Reconnect** - Internet reconnects
3. **Interval** - Polling

## Stale Time

Configure how long data stays fresh:

```typescript
useQuery({
  queryKey: ['posts'],
  queryFn: fetchPosts,
  staleTime: 1000 * 60 * 5, // 5 minutes
})
```

## Cache Time

How long unused data stays in cache:

```typescript
useQuery({
  queryKey: ['posts'],
  queryFn: fetchPosts,
  gcTime: 1000 * 60 * 10, // 10 minutes (previously cacheTime)
})
```

## Refetch Intervals

Poll for new data:

```typescript
useQuery({
  queryKey: ['posts'],
  queryFn: fetchPosts,
  refetchInterval: 1000 * 30, // Every 30 seconds
})
```

## Manual Refetch

```typescript
const { data, refetch } = useQuery({
  queryKey: ['posts'],
  queryFn: fetchPosts,
})

<button onClick={() => refetch()}>
  Refresh
</button>
```

## Refetch Options

```typescript
refetch({
  cancelRefetch: true,  // Cancel ongoing request
  throwOnError: false,  // Don't throw errors
})
```

## Background Refetching

```typescript
useQuery({
  queryKey: ['posts'],
  queryFn: fetchPosts,
  refetchOnWindowFocus: true,  // Refetch on tab focus
  refetchOnReconnect: true,    // Refetch on reconnect
  refetchOnMount: true,        // Refetch on mount
})
```

## Conditional Refetching

```typescript
useQuery({
  queryKey: ['posts'],
  queryFn: fetchPosts,
  refetchInterval: isImportant ? 1000 : false,
})
```

## Summary

TanStack Query keeps your data fresh automatically with configurable refetching strategies.

Module 6 Complete! Next: [Module 7: Advanced Data Fetching](../module-07-advanced-data-fetching/README.md)
