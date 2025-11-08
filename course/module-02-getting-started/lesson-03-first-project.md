# Lesson 3: Creating Your First Project

## Introduction

Now that you have TanStack Start installed, let's build a complete first project to understand how everything works together.

## Project: Task Manager

We'll build a simple task manager that demonstrates:
- File-based routing
- Data fetching with TanStack Query
- Server functions
- Type-safe navigation
- State management

## Step 1: Create the Project

```bash
npm create @tanstack/start@latest task-manager
cd task-manager
npm install
```

## Step 2: Understanding the Initial Structure

Your project has this structure:

```
task-manager/
├── app/
│   ├── routes/
│   │   ├── __root.tsx      # Root layout
│   │   └── index.tsx       # Homepage
│   ├── router.tsx          # Router configuration
│   ├── client.tsx          # Client entry
│   └── server.tsx          # Server entry
├── public/                 # Static assets
├── package.json
└── vite.config.ts
```

## Step 3: Create the Root Layout

Edit `app/routes/__root.tsx`:

```typescript
import { createRootRoute, Link, Outlet } from '@tanstack/react-router'
import { TanStackRouterDevtools } from '@tanstack/router-devtools'

export const Route = createRootRoute({
  component: RootComponent,
})

function RootComponent() {
  return (
    <div style={{ padding: '20px', fontFamily: 'system-ui' }}>
      <nav style={{ marginBottom: '20px', borderBottom: '2px solid #ccc', paddingBottom: '10px' }}>
        <Link
          to="/"
          style={{ marginRight: '15px', textDecoration: 'none', color: '#0066cc' }}
          activeProps={{ style: { fontWeight: 'bold' } }}
        >
          Home
        </Link>
        <Link
          to="/tasks"
          style={{ marginRight: '15px', textDecoration: 'none', color: '#0066cc' }}
          activeProps={{ style: { fontWeight: 'bold' } }}
        >
          Tasks
        </Link>
        <Link
          to="/about"
          style={{ textDecoration: 'none', color: '#0066cc' }}
          activeProps={{ style: { fontWeight: 'bold' } }}
        >
          About
        </Link>
      </nav>
      <main>
        <Outlet />
      </main>
      <TanStackRouterDevtools />
    </div>
  )
}
```

**Key Concepts**:
- `createRootRoute`: Creates the root layout
- `<Outlet />`: Renders child routes
- `<Link>`: Type-safe navigation
- `TanStackRouterDevtools`: Development tools

## Step 4: Create the Homepage

Edit `app/routes/index.tsx`:

```typescript
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/')({
  component: HomePage,
})

function HomePage() {
  return (
    <div>
      <h1>Welcome to Task Manager</h1>
      <p>A simple app built with TanStack Start</p>
      <div style={{ marginTop: '20px' }}>
        <h2>Features:</h2>
        <ul>
          <li>Create and manage tasks</li>
          <li>Type-safe routing</li>
          <li>Server-side rendering</li>
          <li>Built with TanStack Start</li>
        </ul>
      </div>
    </div>
  )
}
```

## Step 5: Create Task Types

Create `app/types/task.ts`:

```typescript
export type Task = {
  id: string
  title: string
  completed: boolean
  createdAt: Date
}

export type CreateTaskInput = {
  title: string
}
```

## Step 6: Create Mock Data Store

Create `app/lib/tasks.ts`:

```typescript
import { Task, CreateTaskInput } from '../types/task'

// In-memory storage (use a database in production)
let tasks: Task[] = [
  {
    id: '1',
    title: 'Learn TanStack Start',
    completed: false,
    createdAt: new Date('2025-01-01'),
  },
  {
    id: '2',
    title: 'Build an awesome app',
    completed: false,
    createdAt: new Date('2025-01-02'),
  },
]

export async function getTasks(): Promise<Task[]> {
  // Simulate API delay
  await new Promise(resolve => setTimeout(resolve, 100))
  return tasks
}

export async function getTask(id: string): Promise<Task | undefined> {
  await new Promise(resolve => setTimeout(resolve, 100))
  return tasks.find(task => task.id === id)
}

export async function createTask(input: CreateTaskInput): Promise<Task> {
  await new Promise(resolve => setTimeout(resolve, 100))

  const newTask: Task = {
    id: Date.now().toString(),
    title: input.title,
    completed: false,
    createdAt: new Date(),
  }

  tasks.push(newTask)
  return newTask
}

export async function toggleTask(id: string): Promise<Task | undefined> {
  await new Promise(resolve => setTimeout(resolve, 100))

  const task = tasks.find(t => t.id === id)
  if (task) {
    task.completed = !task.completed
  }
  return task
}

export async function deleteTask(id: string): Promise<void> {
  await new Promise(resolve => setTimeout(resolve, 100))
  tasks = tasks.filter(task => task.id !== id)
}
```

## Step 7: Create Server Functions

Create `app/lib/server-functions.ts`:

```typescript
import { createServerFn } from '@tanstack/start'
import { getTasks, getTask, createTask, toggleTask, deleteTask } from './tasks'
import type { CreateTaskInput } from '../types/task'

export const getTasksFn = createServerFn('GET', async () => {
  return await getTasks()
})

export const getTaskFn = createServerFn('GET', async (id: string) => {
  return await getTask(id)
})

export const createTaskFn = createServerFn('POST', async (input: CreateTaskInput) => {
  return await createTask(input)
})

export const toggleTaskFn = createServerFn('POST', async (id: string) => {
  return await toggleTask(id)
})

export const deleteTaskFn = createServerFn('POST', async (id: string) => {
  await deleteTask(id)
  return { success: true }
})
```

## Step 8: Create Tasks List Page

Create `app/routes/tasks.index.tsx`:

```typescript
import { createFileRoute, Link } from '@tanstack/react-router'
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query'
import { getTasksFn, createTaskFn, toggleTaskFn, deleteTaskFn } from '../lib/server-functions'
import { useState } from 'react'

export const Route = createFileRoute('/tasks/')({
  component: TasksPage,
})

function TasksPage() {
  const [newTaskTitle, setNewTaskTitle] = useState('')
  const queryClient = useQueryClient()

  // Fetch tasks
  const { data: tasks, isLoading } = useQuery({
    queryKey: ['tasks'],
    queryFn: () => getTasksFn(),
  })

  // Create task mutation
  const createMutation = useMutation({
    mutationFn: createTaskFn,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['tasks'] })
      setNewTaskTitle('')
    },
  })

  // Toggle task mutation
  const toggleMutation = useMutation({
    mutationFn: toggleTaskFn,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['tasks'] })
    },
  })

  // Delete task mutation
  const deleteMutation = useMutation({
    mutationFn: deleteTaskFn,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['tasks'] })
    },
  })

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault()
    if (newTaskTitle.trim()) {
      createMutation.mutate({ title: newTaskTitle })
    }
  }

  if (isLoading) {
    return <div>Loading tasks...</div>
  }

  return (
    <div>
      <h1>Tasks</h1>

      {/* Create Task Form */}
      <form onSubmit={handleSubmit} style={{ marginBottom: '20px' }}>
        <input
          type="text"
          value={newTaskTitle}
          onChange={(e) => setNewTaskTitle(e.target.value)}
          placeholder="Enter new task..."
          style={{ padding: '8px', marginRight: '10px', width: '300px' }}
        />
        <button
          type="submit"
          disabled={createMutation.isPending}
          style={{ padding: '8px 16px', cursor: 'pointer' }}
        >
          {createMutation.isPending ? 'Adding...' : 'Add Task'}
        </button>
      </form>

      {/* Tasks List */}
      {tasks && tasks.length > 0 ? (
        <ul style={{ listStyle: 'none', padding: 0 }}>
          {tasks.map((task) => (
            <li
              key={task.id}
              style={{
                padding: '10px',
                marginBottom: '10px',
                border: '1px solid #ddd',
                borderRadius: '4px',
                display: 'flex',
                alignItems: 'center',
                justifyContent: 'space-between'
              }}
            >
              <div style={{ display: 'flex', alignItems: 'center', gap: '10px' }}>
                <input
                  type="checkbox"
                  checked={task.completed}
                  onChange={() => toggleMutation.mutate(task.id)}
                  style={{ cursor: 'pointer' }}
                />
                <Link
                  to="/tasks/$taskId"
                  params={{ taskId: task.id }}
                  style={{
                    textDecoration: task.completed ? 'line-through' : 'none',
                    color: task.completed ? '#999' : '#000'
                  }}
                >
                  {task.title}
                </Link>
              </div>
              <button
                onClick={() => deleteMutation.mutate(task.id)}
                disabled={deleteMutation.isPending}
                style={{
                  padding: '4px 8px',
                  backgroundColor: '#dc3545',
                  color: 'white',
                  border: 'none',
                  borderRadius: '4px',
                  cursor: 'pointer'
                }}
              >
                Delete
              </button>
            </li>
          ))}
        </ul>
      ) : (
        <p>No tasks yet. Create one above!</p>
      )}
    </div>
  )
}
```

## Step 9: Create Task Detail Page

Create `app/routes/tasks.$taskId.tsx`:

```typescript
import { createFileRoute } from '@tanstack/react-router'
import { useQuery } from '@tanstack/react-query'
import { getTaskFn } from '../lib/server-functions'

export const Route = createFileRoute('/tasks/$taskId')({
  component: TaskDetailPage,
})

function TaskDetailPage() {
  const { taskId } = Route.useParams()

  const { data: task, isLoading } = useQuery({
    queryKey: ['tasks', taskId],
    queryFn: () => getTaskFn(taskId),
  })

  if (isLoading) {
    return <div>Loading task...</div>
  }

  if (!task) {
    return <div>Task not found</div>
  }

  return (
    <div>
      <h1>Task Details</h1>
      <div style={{ marginTop: '20px' }}>
        <p><strong>ID:</strong> {task.id}</p>
        <p><strong>Title:</strong> {task.title}</p>
        <p><strong>Status:</strong> {task.completed ? '✅ Completed' : '⏳ Pending'}</p>
        <p><strong>Created:</strong> {new Date(task.createdAt).toLocaleDateString()}</p>
      </div>
    </div>
  )
}
```

## Step 10: Create About Page

Create `app/routes/about.tsx`:

```typescript
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/about')({
  component: AboutPage,
})

function AboutPage() {
  return (
    <div>
      <h1>About Task Manager</h1>
      <p>This is a simple task management app built with TanStack Start.</p>
      <h2>Technologies Used:</h2>
      <ul>
        <li>TanStack Start - Full-stack framework</li>
        <li>TanStack Router - Type-safe routing</li>
        <li>TanStack Query - Data fetching and caching</li>
        <li>React - UI library</li>
        <li>TypeScript - Type safety</li>
      </ul>
    </div>
  )
}
```

## Step 11: Run Your App

```bash
npm run dev
```

Visit `http://localhost:3000` and test:

1. Navigate between pages
2. Create new tasks
3. Mark tasks as complete
4. View task details
5. Delete tasks

## What You've Learned

### File-Based Routing
```
routes/
├── __root.tsx           → Layout for all pages
├── index.tsx            → / (homepage)
├── about.tsx            → /about
├── tasks.index.tsx      → /tasks
└── tasks.$taskId.tsx    → /tasks/:taskId
```

### Type-Safe Navigation
```typescript
// Params are typed automatically
<Link to="/tasks/$taskId" params={{ taskId: '1' }} />

// TypeScript knows the shape
const { taskId } = Route.useParams() // taskId: string
```

### Server Functions
```typescript
// Server-only code
export const getTasksFn = createServerFn('GET', async () => {
  // This runs ONLY on the server
  return await getTasks()
})
```

### Data Fetching
```typescript
// Automatic caching and refetching
const { data, isLoading } = useQuery({
  queryKey: ['tasks'],
  queryFn: () => getTasksFn(),
})
```

### Mutations
```typescript
// Update data with automatic cache invalidation
const mutation = useMutation({
  mutationFn: createTaskFn,
  onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: ['tasks'] })
  },
})
```

## Summary

You've built a complete TanStack Start application with:

✅ File-based routing
✅ Type-safe navigation
✅ Server functions
✅ Data fetching with caching
✅ Mutations with optimistic updates
✅ Multiple routes and layouts

## Next Steps

Continue to [Lesson 4: Understanding the Development Server](./lesson-04-dev-server.md)

## Further Reading

- [TanStack Router File-Based Routing](https://tanstack.com/router/latest/docs/framework/react/guide/file-based-routing)
- [TanStack Query Queries](https://tanstack.com/query/latest/docs/framework/react/guides/queries)
- [Server Functions](https://tanstack.com/start/latest/docs/framework/react/server-functions)
