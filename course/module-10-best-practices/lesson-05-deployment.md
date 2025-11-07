# Lesson 5: Deployment and Production Builds

## Building for Production

```bash
npm run build
```

## Environment Variables

```bash
# .env.production
NODE_ENV=production
DATABASE_URL=postgresql://...
API_URL=https://api.production.com
```

## Vercel Deployment

```bash
# Install Vercel CLI
npm i -g vercel

# Deploy
vercel --prod
```

## Docker Deployment

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

EXPOSE 3000

CMD ["npm", "start"]
```

## Cloudflare Pages

```typescript
// app.config.ts
export default defineConfig({
  server: {
    preset: 'cloudflare-pages',
  },
})
```

## Health Checks

```typescript
export const healthCheck = createServerFn('GET', async () => {
  return { status: 'ok', timestamp: Date.now() }
})
```

## Next Steps

Continue to [Lesson 6: Monitoring and Analytics](./lesson-06-monitoring.md)
