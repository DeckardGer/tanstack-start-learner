# Lesson 6: Real-Time Features

## WebSocket Integration

```typescript
// app/lib/websocket.ts
export function useWebSocket(url: string) {
  const [messages, setMessages] = useState([])
  
  useEffect(() => {
    const ws = new WebSocket(url)
    
    ws.onmessage = (event) => {
      setMessages(prev => [...prev, event.data])
    }
    
    return () => ws.close()
  }, [url])
  
  return messages
}
```

## Polling

```typescript
useQuery({
  queryKey: ['messages'],
  queryFn: fetchMessages,
  refetchInterval: 3000, // Poll every 3 seconds
})
```

## Server-Sent Events

```typescript
useEffect(() => {
  const eventSource = new EventSource('/api/events')
  
  eventSource.onmessage = (event) => {
    console.log('New message:', event.data)
  }
  
  return () => eventSource.close()
}, [])
```

## Summary

Full-stack patterns enable building complete applications with TanStack Start.

Module 9 Complete! Next: [Module 10: Best Practices & Production](../module-10-best-practices/README.md)
