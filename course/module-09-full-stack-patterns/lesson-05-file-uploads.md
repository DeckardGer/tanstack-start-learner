# Lesson 5: File Uploads and Asset Management

## File Upload

```typescript
export const uploadFile = createServerFn('POST', async (formData: FormData) => {
  const file = formData.get('file') as File
  
  // Validate file
  if (file.size > 5 * 1024 * 1024) {
    throw new Error('File too large')
  }
  
  // Save file
  const buffer = await file.arrayBuffer()
  const path = `/uploads/${Date.now()}-${file.name}`
  
  await writeFile(path, Buffer.from(buffer))
  
  return { path }
})
```

## Upload Component

```typescript
function FileUpload() {
  const mutation = useMutation({
    mutationFn: uploadFile,
  })

  const handleUpload = async (e) => {
    const file = e.target.files[0]
    const formData = new FormData()
    formData.append('file', file)
    
    await mutation.mutateAsync(formData)
  }

  return (
    <div>
      <input type="file" onChange={handleUpload} />
      {mutation.isPending && <div>Uploading...</div>}
      {mutation.isSuccess && <div>Uploaded!</div>}
    </div>
  )
}
```

## Next Steps

Continue to [Lesson 6: Real-Time Features](./lesson-06-realtime.md)
