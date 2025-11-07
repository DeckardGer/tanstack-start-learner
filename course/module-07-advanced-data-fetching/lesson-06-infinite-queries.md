# Lesson 6: Infinite Queries and Pagination

## Infinite Query

Load more data on scroll:

```typescript
const {
  data,
  fetchNextPage,
  hasNextPage,
  isFetchingNextPage,
} = useInfiniteQuery({
  queryKey: ['posts'],
  queryFn: ({ pageParam = 1 }) => fetchPosts(pageParam),
  getNextPageParam: (lastPage, pages) => {
    return lastPage.hasMore ? pages.length + 1 : undefined
  },
  initialPageParam: 1,
})

// Render
data?.pages.map((page) => (
  page.posts.map(post => <PostCard key={post.id} post={post} />)
))

// Load more button
<button onClick={() => fetchNextPage()} disabled={!hasNextPage}>
  {isFetchingNextPage ? 'Loading...' : 'Load More'}
</button>
```

## Bidirectional Infinite Query

```typescript
const query = useInfiniteQuery({
  queryKey: ['posts'],
  queryFn: ({ pageParam }) => fetchPosts(pageParam),
  getNextPageParam: (lastPage) => lastPage.nextCursor,
  getPreviousPageParam: (firstPage) => firstPage.prevCursor,
  initialPageParam: null,
})

<button onClick={() => query.fetchPreviousPage()}>Load Older</button>
<button onClick={() => query.fetchNextPage()}>Load Newer</button>
```

## Pagination

```typescript
function Posts() {
  const [page, setPage] = useState(1)

  const { data, isLoading } = useQuery({
    queryKey: ['posts', page],
    queryFn: () => fetchPosts(page),
    placeholderData: (previousData) => previousData, // Keep old data while loading
  })

  return (
    <>
      <PostsList posts={data} />
      <button onClick={() => setPage(p => p - 1)} disabled={page === 1}>
        Previous
      </button>
      <button onClick={() => setPage(p => p + 1)}>
        Next
      </button>
    </>
  )
}
```

## Summary

Infinite queries and pagination make loading large datasets efficient.

Module 7 Complete! Next: [Module 8: Server Functions & SSR](../module-08-server-functions/README.md)
