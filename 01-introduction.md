---
marp: true
theme: default
paginate: true
---

# Writing TypeScript with Deno

## A Modern Approach to Server-Side Development

---

## About This Presentation

- **Duration**: 30-50 minutes
- **Topics**: Deno fundamentals, TypeScript integration, CLI applications
- **Target Audience**: Developers familiar with JavaScript/TypeScript
- **Goal**: Get you building with Deno confidently

---

## What is Deno?

- Modern JavaScript/TypeScript runtime built on V8
- Created by Ryan Dahl (Node.js creator)
- First-class TypeScript support
- Secure by default
- Built-in tooling (formatter, linter, test runner)
- Web-standard APIs

---

## Why Deno?

**Key Advantages:**

- No `package.json` or `node_modules`
- TypeScript works out of the box
- Security-first approach with permissions
- Modern ES modules (no CommonJS)
- Standard library maintained by core team
- Single executable with all tools included

---

## Deno vs Node.js

| Feature | Deno | Node.js |
|---------|------|---------|
| TypeScript | Built-in | Requires setup |
| Security | Explicit permissions | Full access |
| Modules | ES modules, URLs | CommonJS/ESM |
| Package Manager | Built-in | npm/yarn/pnpm |
| Tooling | Included | Separate packages |

---

## When to Use Deno?

**Great for:**
- New projects with TypeScript
- CLI tools and scripts
- Edge functions and serverless
- Learning modern JavaScript

**Consider Node.js for:**
- Large existing codebases
- Heavy npm dependency requirements
- Team familiarity constraints

---

## Today's Journey

1. **Getting Started** - Installation and first steps
2. **TypeScript Fundamentals** - How Deno handles TS
3. **Building CLI Applications** - Practical examples
4. **Advanced Topics** - Permissions, deployment, best practices

Let's dive in!
