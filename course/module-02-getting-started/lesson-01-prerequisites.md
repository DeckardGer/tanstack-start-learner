# Lesson 1: Prerequisites & Requirements

## Introduction

Before diving into TanStack Start development, let's ensure you have all the necessary knowledge and tools installed.

## Knowledge Prerequisites

### 1. JavaScript & Modern ES6+

You should be comfortable with:

**ES6+ Syntax**:
```javascript
// Arrow functions
const add = (a, b) => a + b

// Destructuring
const { name, age } = user
const [first, ...rest] = array

// Spread operator
const newArray = [...oldArray, newItem]
const newObject = { ...oldObject, key: 'value' }

// Template literals
const message = `Hello, ${name}!`

// Async/await
async function fetchData() {
  const response = await fetch('/api/data')
  const data = await response.json()
  return data
}
```

**Modules**:
```javascript
// ES Modules
import { something } from './module'
export const myFunction = () => {}
export default MyComponent
```

### 2. React Fundamentals

You should understand:

**Function Components**:
```typescript
function Welcome({ name }: { name: string }) {
  return <h1>Hello, {name}</h1>
}
```

**Hooks**:
```typescript
import { useState, useEffect } from 'react'

function Counter() {
  const [count, setCount] = useState(0)

  useEffect(() => {
    document.title = `Count: ${count}`
  }, [count])

  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  )
}
```

**Props and State**:
```typescript
type Props = {
  initialCount: number
}

function Counter({ initialCount }: Props) {
  const [count, setCount] = useState(initialCount)
  return <div>{count}</div>
}
```

### 3. TypeScript (Recommended but Optional)

While you can use TanStack Start with JavaScript, TypeScript is highly recommended:

**Basic Types**:
```typescript
// Type annotations
const name: string = 'John'
const age: number = 30
const isActive: boolean = true

// Interfaces
interface User {
  id: string
  name: string
  email: string
}

// Type aliases
type Status = 'pending' | 'active' | 'inactive'

// Generics
function first<T>(array: T[]): T {
  return array[0]
}
```

**Don't worry**: TanStack Start provides excellent type inference, so you won't need to write types everywhere!

### 4. Basic Command Line

You should be able to:

```bash
# Navigate directories
cd my-project
cd ..

# List files
ls        # macOS/Linux
dir       # Windows

# Run commands
npm install
npm run dev
```

### 5. Git (Recommended)

Basic Git knowledge is helpful:

```bash
# Clone repositories
git clone <url>

# Check status
git status

# Commit changes
git add .
git commit -m "message"
```

## System Requirements

### Operating System

TanStack Start works on:
- **macOS**: 10.15 (Catalina) or later
- **Windows**: 10 or later
- **Linux**: Modern distributions (Ubuntu 20.04+, Fedora 34+, etc.)

### Node.js

**Required Version**: Node.js 18.0.0 or later

**Recommended**: Node.js 20.x LTS (Long Term Support)

**Check your version**:
```bash
node --version
# Should output: v20.x.x or higher
```

**Install Node.js**:

1. **Using Official Installer**:
   - Visit [nodejs.org](https://nodejs.org)
   - Download the LTS version
   - Run the installer

2. **Using nvm (Recommended for macOS/Linux)**:
   ```bash
   # Install nvm
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

   # Install Node.js LTS
   nvm install --lts

   # Use the LTS version
   nvm use --lts
   ```

3. **Using nvm-windows**:
   - Download from [github.com/coreybutler/nvm-windows](https://github.com/coreybutler/nvm-windows)
   - Install and run:
   ```bash
   nvm install lts
   nvm use lts
   ```

### Package Manager

You'll need one of:

**npm** (comes with Node.js):
```bash
npm --version
# Should output: 9.x.x or higher
```

**pnpm** (recommended for speed):
```bash
# Install pnpm
npm install -g pnpm

# Check version
pnpm --version
```

**yarn**:
```bash
# Install yarn
npm install -g yarn

# Check version
yarn --version
```

## Development Tools

### Code Editor

**Recommended: Visual Studio Code**

Why VS Code?
- Excellent TypeScript support
- Great React/JSX support
- Integrated terminal
- Extensions for TanStack tools

**Download**: [code.visualstudio.com](https://code.visualstudio.com)

**Essential Extensions**:

1. **ES7+ React/Redux/React-Native snippets**
   - Quick React snippets
   - Install: `dsznajder.es7-react-js-snippets`

2. **TypeScript + React Snippets**
   - TypeScript-specific snippets
   - Install: `devsense.typescript-react-snippets`

3. **Prettier - Code Formatter**
   - Automatic code formatting
   - Install: `esbenp.prettier-vscode`

4. **ESLint**
   - JavaScript/TypeScript linting
   - Install: `dbaeumer.vscode-eslint`

5. **TanStack Query DevTools** (browser extension)
   - Visualize queries and cache
   - Chrome/Firefox extension

**VS Code Settings** (`.vscode/settings.json`):
```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "typescript.preferences.importModuleSpecifier": "relative",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```

### Alternative Editors

**WebStorm**:
- Excellent for large projects
- Built-in TypeScript support
- Requires license

**Cursor**:
- AI-powered editor
- Based on VS Code
- Great for learning

**Zed**:
- Super fast
- Modern alternative
- Growing ecosystem

## Browser Requirements

### Development Browser

**Recommended**: Chrome or Edge (Chromium-based)
- Best DevTools
- React DevTools extension
- TanStack Query DevTools extension

**Also Supported**:
- Firefox
- Safari
- Brave

### Browser Extensions

**React DevTools**:
- Inspect React component tree
- Debug props and state
- [Chrome](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
- [Firefox](https://addons.mozilla.org/en-US/firefox/addon/react-devtools/)

**TanStack Query DevTools**:
- Already included in development mode!
- Appears automatically in your app

## Optional but Useful

### Database Tools

If you'll be working with databases:

**PostgreSQL**:
- [pgAdmin](https://www.pgadmin.org/)
- [TablePlus](https://tableplus.com/)
- [DBeaver](https://dbeaver.io/)

**SQLite**:
- [DB Browser for SQLite](https://sqlitebrowser.org/)

**MongoDB**:
- [MongoDB Compass](https://www.mongodb.com/products/compass)

### API Testing Tools

**Postman**:
- Test API endpoints
- [Download](https://www.postman.com/downloads/)

**Insomnia**:
- Lightweight alternative
- [Download](https://insomnia.rest/download)

**Thunder Client**:
- VS Code extension
- Integrated testing

## Verification Checklist

Before proceeding, verify you have:

- [ ] Node.js 18+ installed
- [ ] npm/pnpm/yarn working
- [ ] Code editor set up (VS Code recommended)
- [ ] Basic React knowledge
- [ ] Basic TypeScript familiarity (recommended)
- [ ] Command line basics
- [ ] Modern browser with DevTools

## Quick Verification Commands

Run these to verify your setup:

```bash
# Check Node.js
node --version
# Expected: v18.0.0 or higher

# Check npm
npm --version
# Expected: 9.0.0 or higher

# Check git (optional but recommended)
git --version
# Expected: 2.x.x or higher

# Verify npm can install packages
npm install -g npm-check
npm-check --version
```

## Troubleshooting

### Node.js Version Issues

**Problem**: Version too old

**Solution**:
```bash
# Using nvm
nvm install 20
nvm use 20

# Or download from nodejs.org
```

### Permission Errors (macOS/Linux)

**Problem**: EACCES errors when installing global packages

**Solution**:
```bash
# Use nvm (recommended)
# OR change npm's default directory
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
# Add to ~/.profile or ~/.zshrc:
export PATH=~/.npm-global/bin:$PATH
```

### Windows PATH Issues

**Problem**: Commands not found

**Solution**:
- Reinstall Node.js with "Add to PATH" checked
- Or manually add Node.js to PATH in System Environment Variables

## Summary

You now have:

✅ Understanding of required knowledge
✅ System requirements verified
✅ Development tools installed
✅ Browser extensions ready
✅ Environment verified

You're ready to install TanStack Start!

## Next Steps

Continue to [Lesson 2: Installation & Setup](./lesson-02-installation-setup.md) to install TanStack Start and create your first project.

## Further Reading

- [Node.js Official Docs](https://nodejs.org/docs)
- [React Documentation](https://react.dev)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [VS Code Documentation](https://code.visualstudio.com/docs)
