# Lesson 4: SEO and Meta Tags

## Meta Tags

```typescript
// app/routes/posts/$postId.tsx
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    const post = await fetchPost(params.postId)
    return { post }
  },
  component: PostPage,
  head: ({ loaderData }) => ({
    meta: [
      {
        title: loaderData.post.title,
      },
      {
        name: 'description',
        content: loaderData.post.excerpt,
      },
      {
        property: 'og:title',
        content: loaderData.post.title,
      },
      {
        property: 'og:description',
        content: loaderData.post.excerpt,
      },
      {
        property: 'og:image',
        content: loaderData.post.image,
      },
    ],
  }),
})
```

## Dynamic Title

```typescript
head: ({ loaderData }) => ({
  meta: [
    {
      title: `${loaderData.post.title} | My Blog`,
    },
  ],
})
```

## Structured Data

```typescript
function PostPage() {
  const { post } = Route.useLoaderData()
  
  const structuredData = {
    '@context': 'https://schema.org',
    '@type': 'BlogPosting',
    headline: post.title,
    author: post.author,
    datePublished: post.publishedAt,
  }
  
  return (
    <>
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={{ __html: JSON.stringify(structuredData) }}
      />
      <article>{/* content */}</article>
    </>
  )
}
```

## Next Steps

Continue to [Lesson 5: Deployment](./lesson-05-deployment.md)
