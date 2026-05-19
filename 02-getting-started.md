---
marp: true
theme: default
paginate: true
---

# Getting Started with Deno

## Installation and First Steps

---

## Installing Deno

**macOS/Linux:**
```bash
curl -fsSL https://deno.land/install.sh | sh
```

**Windows (PowerShell):**
```powershell
irm https://deno.land/install.ps1 | iex
```

**Alternative methods:**
- Homebrew: `brew install deno`
- Cargo: `cargo install deno --locked`
- Docker: `docker pull denoland/deno`

---

## Verify Installation

```bash
deno --version
```

Output:
```
deno 2.x.x (release, x86_64-unknown-linux-gnu)
v8 12.x.xxx.x
typescript 5.x.x
```

---

## Your First Deno Program

Create `hello.ts`:

```typescript
console.log("Hello from Deno!");
console.log(`Running on ${Deno.build.os}`);
console.log(`Current time: ${new Date().toISOString()}`);
```

Run it:
```bash
deno run hello.ts
```

No compilation step needed!

---

## Deno CLI Commands

**Essential commands:**

```bash
deno run <file>        # Run a program
deno test              # Run tests
deno fmt               # Format code
deno lint              # Lint code
deno compile           # Create executable
deno task <name>       # Run a task
deno info              # Show info about dependencies
```

---

## Using External Modules

**Import from URLs:**

```typescript
import { serve } from "https://deno.land/std@0.224.0/http/server.ts";

serve((req) => new Response("Hello World!"));
```

**Import from npm:**

```typescript
import chalk from "npm:chalk@5";

console.log(chalk.blue("Hello from npm!"));
```

---

## Import Maps

Create `deno.json`:

```json
{
  "imports": {
    "@std/cli": "jsr:@std/cli@^1.0.0",
    "chalk": "npm:chalk@5"
  }
}
```

Now import with clean aliases:

```typescript
import { parseArgs } from "@std/cli";
import chalk from "chalk";
```

---

## Standard Library

Deno's standard library is hosted on JSR (JavaScript Registry):

```typescript
import { parseArgs } from "@std/cli/parse-args";
import { load } from "@std/dotenv";
import { assertEquals } from "@std/assert";
```

**Benefits:**
- Well-tested, maintained by core team
- Semantic versioning
- No `node_modules` directory

---

## Configuration: deno.json

```json
{
  "imports": {
    "@std/cli": "jsr:@std/cli@^1.0.0"
  },
  "tasks": {
    "dev": "deno run --watch main.ts",
    "start": "deno run --allow-net main.ts"
  },
  "fmt": {
    "semiColons": false
  },
  "lint": {
    "rules": {
      "tags": ["recommended"]
    }
  }
}
```

---

## IDE Setup

**VS Code:**
1. Install "Deno" extension
2. Enable Deno in workspace settings:

```json
{
  "deno.enable": true,
  "deno.lint": true,
  "deno.unstable": false
}
```

**Other IDEs:**
- JetBrains: Built-in support
- Vim/Neovim: Use `deno lsp`
- Sublime Text: LSP package

---

## Quick Experiment: REPL

Deno has a built-in REPL:

```bash
deno repl
```

Try it:
```typescript
> const name = "Deno"
> `Hello ${name}!`
> await fetch("https://api.github.com/repos/denoland/deno")
>   .then(r => r.json())
```

Top-level `await` works!

---

## Summary

- Installing Deno is quick and easy
- Single executable with all tools
- TypeScript works immediately
- Import from URLs or npm
- Configuration via `deno.json`
- Excellent IDE support

Ready to write TypeScript!
