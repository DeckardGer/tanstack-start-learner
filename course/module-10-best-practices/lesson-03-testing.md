# Lesson 3: Testing Strategies

## Unit Tests

```typescript
// features/posts/lib/posts.test.ts
import { describe, it, expect } from 'vitest'
import { formatPostTitle } from './posts'

describe('formatPostTitle', () => {
  it('capitalizes first letter', () => {
    expect(formatPostTitle('hello')).toBe('Hello')
  })
})
```

## Component Tests

```typescript
// features/posts/components/PostCard.test.tsx
import { render, screen } from '@testing-library/react'
import { PostCard } from './PostCard'

describe('PostCard', () => {
  it('renders post title', () => {
    const post = { id: '1', title: 'Test Post' }
    render(<PostCard post={post} />)
    expect(screen.getByText('Test Post')).toBeInTheDocument()
  })
})
```

## Integration Tests

```typescript
// Test routes
import { createMemoryRouter } from '@tanstack/react-router'

describe('Posts route', () => {
  it('loads and displays posts', async () => {
    const router = createMemoryRouter({
      routes: [postsRoute],
      initialEntries: ['/posts'],
    })
    
    render(<RouterProvider router={router} />)
    
    await waitFor(() => {
      expect(screen.getByText('Post 1')).toBeInTheDocument()
    })
  })
})
```

## Next Steps

Continue to [Lesson 4: SEO and Meta Tags](./lesson-04-seo.md)
