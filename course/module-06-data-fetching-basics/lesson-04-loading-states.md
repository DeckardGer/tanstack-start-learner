# Lesson 4: Loading, Error, and Success States

## Query States

```typescript
const { 
  data,
  isLoading,      // Initial load (no cached data)
  isFetching,     // Any fetch (including background)
  isError,        // Error state
  isSuccess,      // Success state
  error,          // Error object
  status,         // 'pending' | 'error' | 'success'
} = useQuery({...})
```

## Loading States

```typescript
function Posts() {
  const { data, isLoading, isFetching } = useQuery({
    queryKey: ['posts'],
    queryFn: fetchPosts,
  })

  if (isLoading) {
    return <div>Loading initial data...</div>
  }

  return (
    <div>
      {isFetching && <div>Updating...</div>}
      <PostsList posts={data} />
    </div>
  )
}
```

## Error Handling

```typescript
function Posts() {
  const { data, error, isError } = useQuery({
    queryKey: ['posts'],
    queryFn: fetchPosts,
  })

  if (isError) {
    return (
      <div className="error">
        <h2>Error loading posts</h2>
        <p>{error.message}</p>
        <button onClick={() => refetch()}>Try Again</button>
      </div>
    )
  }

  return <PostsList posts={data} />
}
```

## Success State

```typescript
const { data, isSuccess } = useQuery({
  queryKey: ['posts'],
  queryFn: fetchPosts,
})

if (isSuccess) {
  // Data is guaranteed to exist
  return <div>{data.length} posts</div>
}
```

## Status-Based Rendering

```typescript
const { data, status } = useQuery({
  queryKey: ['posts'],
  queryFn: fetchPosts,
})

switch (status) {
  case 'pending':
    return <Spinner />
  case 'error':
    return <ErrorMessage />
  case 'success':
    return <PostsList posts={data} />
}
```

## Suspense Integration

```typescript
function Posts() {
  const { data } = useSuspenseQuery({
    queryKey: ['posts'],
    queryFn: fetchPosts,
  })

  // No loading check needed!
  return <PostsList posts={data} />
}

// Wrap with Suspense
<Suspense fallback={<Loading />}>
  <Posts />
</Suspense>
```

## Next Steps

Continue to [Lesson 5: Refetching and Background Updates](./lesson-05-refetching.md)
