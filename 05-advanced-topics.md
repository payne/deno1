---
marp: true
theme: default
paginate: true
---

# Advanced Topics

## Security, Testing, and Deployment

---

## Security: The Deno Philosophy

**Secure by Default:**
- No file system access
- No network access
- No environment variable access
- No subprocess execution

**You must explicitly grant permissions**

This prevents malicious code from accessing sensitive resources.

---

## Permission Flags

```bash
--allow-read[=<path>]       # File system read
--allow-write[=<path>]      # File system write
--allow-net[=<host>]        # Network access
--allow-env[=<var>]         # Environment variables
--allow-run[=<program>]     # Run subprocesses
--allow-ffi                 # Foreign function interface
--allow-hrtime              # High-resolution time
--allow-all                 # Grant all permissions
```

---

## Granular Permissions

```bash
# Only read from specific directory
deno run --allow-read=/tmp myapp.ts

# Only access specific hosts
deno run --allow-net=api.github.com,deno.land myapp.ts

# Multiple permissions
deno run --allow-read --allow-write=/tmp --allow-net myapp.ts

# Read specific environment variables
deno run --allow-env=API_KEY,PORT myapp.ts
```

More secure than `--allow-all`!

---

## Permission Prompts

Without flags, Deno asks for permission:

```typescript
// This will prompt the user
const data = await Deno.readTextFile("data.txt");
```

Output:
```
┌ ⚠️  Deno requests read access to "data.txt".
├ Requested by `Deno.readTextFile()` API.
├ Run again with --allow-read to bypass this prompt.
└ Allow? [y/n/A] (y = yes, allow; n = no, deny; A = allow all read permissions)
```

---

## Testing with Deno

```typescript
// math_test.ts
import { assertEquals } from "@std/assert";

function add(a: number, b: number): number {
  return a + b;
}

Deno.test("add function", () => {
  assertEquals(add(2, 3), 5);
  assertEquals(add(-1, 1), 0);
  assertEquals(add(0, 0), 0);
});

Deno.test("add handles decimals", () => {
  assertEquals(add(1.5, 2.5), 4.0);
});
```

---

## Running Tests

```bash
# Run all tests
deno test

# Run specific test file
deno test math_test.ts

# Watch mode
deno test --watch

# Filter tests by name
deno test --filter "add"
```

No test runner installation needed!

---

## Async Tests

```typescript
import { assertEquals } from "@std/assert";

async function fetchUser(id: number) {
  const response = await fetch(`https://api.example.com/users/${id}`);
  return response.json();
}

Deno.test("fetchUser returns user data", async () => {
  const user = await fetchUser(1);
  assertEquals(typeof user.name, "string");
  assertEquals(typeof user.id, "number");
});
```

Tests with async/await just work!

---

## More Assertions

```typescript
import {
  assertEquals,
  assertNotEquals,
  assertExists,
  assertStringIncludes,
  assertThrows,
  assertRejects
} from "@std/assert";

Deno.test("various assertions", () => {
  assertEquals(1 + 1, 2);
  assertNotEquals("hello", "world");
  assertExists({ name: "Alice" });
  assertStringIncludes("Hello World", "World");

  assertThrows(() => {
    throw new Error("boom");
  });
});
```

---

## Mocking and Spies

```typescript
import { assertSpyCalls, spy } from "@std/testing/mock";

Deno.test("function is called", () => {
  const callback = spy();

  function doSomething(fn: () => void) {
    fn();
    fn();
  }

  doSomething(callback);

  assertSpyCalls(callback, 2);
});
```

---

## Benchmarking

```typescript
// bench.ts
Deno.bench("string concatenation", () => {
  let str = "";
  for (let i = 0; i < 1000; i++) {
    str += "x";
  }
});

Deno.bench("array join", () => {
  const arr = [];
  for (let i = 0; i < 1000; i++) {
    arr.push("x");
  }
  arr.join("");
});
```

Run: `deno bench`

---

## Code Coverage

```bash
# Run tests with coverage
deno test --coverage=cov_profile

# Generate coverage report
deno coverage cov_profile

# Generate HTML report
deno coverage cov_profile --html

# Lcov format
deno coverage cov_profile --lcov --output=cov.lcov
```

Built-in code coverage!

---

## Linting

```bash
# Lint all files
deno lint

# Lint specific files
deno lint src/**/*.ts

# Ignore rules
deno lint --rules-exclude=no-explicit-any
```

Configure in `deno.json`:
```json
{
  "lint": {
    "rules": {
      "tags": ["recommended"],
      "exclude": ["no-unused-vars"]
    }
  }
}
```

---

## Formatting

```bash
# Format all files
deno fmt

# Check formatting without changing files
deno fmt --check

# Format specific files
deno fmt src/
```

Configure in `deno.json`:
```json
{
  "fmt": {
    "semiColons": false,
    "singleQuote": true,
    "lineWidth": 100
  }
}
```

---

## Working with Modules

```typescript
// Export from a module
// math.ts
export function add(a: number, b: number): number {
  return a + b;
}

export function multiply(a: number, b: number): number {
  return a * b;
}

export const PI = 3.14159;

// Import in another file
// main.ts
import { add, multiply, PI } from "./math.ts";
```

---

## Re-exporting

```typescript
// utils/index.ts
export { add, multiply } from "./math.ts";
export { formatDate } from "./date.ts";
export { capitalize } from "./string.ts";

// Now import from single entry point
// main.ts
import { add, formatDate, capitalize } from "./utils/index.ts";
```

---

## Dependency Management

```bash
# Cache all dependencies
deno cache main.ts

# Reload dependencies
deno cache --reload main.ts

# Show dependency tree
deno info main.ts

# Update dependencies
deno cache --reload --lock=deno.lock main.ts
```

---

## Lock Files

Create a lock file to ensure consistent dependencies:

```bash
# Generate lock file
deno cache --lock=deno.lock --lock-write main.ts

# Verify lock file
deno cache --lock=deno.lock main.ts
```

Commit `deno.lock` to version control!

---

## Using npm Packages

```typescript
// Import from npm
import express from "npm:express@4";
import lodash from "npm:lodash@4.17.21";

const app = express();

app.get("/", (req, res) => {
  res.send(lodash.capitalize("hello world"));
});

app.listen(3000);
```

Works with most npm packages!

---

## JSR: JavaScript Registry

Deno's modern package registry:

```typescript
// Import from JSR
import { parseArgs } from "jsr:@std/cli@^1.0.0";

// Or use import map
// deno.json
{
  "imports": {
    "@std/cli": "jsr:@std/cli@^1.0.0"
  }
}

// Then import
import { parseArgs } from "@std/cli";
```

---

## Publishing to JSR

```json
// deno.json
{
  "name": "@username/mypackage",
  "version": "1.0.0",
  "exports": "./mod.ts"
}
```

```bash
# Publish to JSR
deno publish
```

Automatic documentation generation!

---

## Web Workers

```typescript
// worker.ts
self.onmessage = (e) => {
  const result = e.data * 2;
  self.postMessage(result);
};

// main.ts
const worker = new Worker(new URL("./worker.ts", import.meta.url).href, {
  type: "module",
});

worker.postMessage(10);
worker.onmessage = (e) => {
  console.log("Result:", e.data); // 20
};
```

---

## Deployment Options

**Deno Deploy:**
- Serverless platform
- Edge deployment
- Zero configuration
- Free tier available

**Docker:**
```dockerfile
FROM denoland/deno:1.40.0
WORKDIR /app
COPY . .
RUN deno cache main.ts
CMD ["deno", "run", "--allow-net", "main.ts"]
```

---

## Deno Deploy Example

```typescript
// main.ts
Deno.serve((req) => {
  return new Response("Hello from Deno Deploy!");
});
```

Deploy:
```bash
# Link project
deployctl deploy --project=myapp main.ts
```

Automatically deployed to edge network!

---

## Environment-Specific Code

```typescript
// Check if running in Deno Deploy
const isDenoDeploy = Deno.env.get("DENO_DEPLOYMENT_ID") !== undefined;

if (isDenoDeploy) {
  // Production configuration
  console.log("Running in production");
} else {
  // Development configuration
  console.log("Running locally");
}
```

---

## Performance Tips

1. Use `Deno.bench()` to measure performance
2. Minimize dependencies
3. Use streaming for large files
4. Cache remote modules
5. Leverage Web APIs (they're optimized)
6. Use Workers for CPU-intensive tasks
7. Compile to binary for production

---

## Debugging

```bash
# Debug with Chrome DevTools
deno run --inspect-brk main.ts

# Connect to chrome://inspect

# VS Code debugging
# Add to .vscode/launch.json
{
  "type": "node",
  "request": "launch",
  "name": "Deno",
  "runtimeExecutable": "deno",
  "runtimeArgs": ["run", "--inspect-brk", "--allow-all", "main.ts"]
}
```

---

## Useful Deno Commands

```bash
deno init              # Initialize new project
deno upgrade           # Update Deno
deno doc               # Generate documentation
deno repl              # Interactive shell
deno vendor            # Vendor dependencies locally
deno bundle            # Bundle to single file (deprecated)
deno install           # Install script as command
```

---

## Community Resources

**Official:**
- deno.land - Main website
- docs.deno.com - Documentation
- jsr.io - Package registry

**Community:**
- discord.gg/deno - Discord server
- github.com/denoland - Source code
- deno.land/x - Third-party modules

---

## Summary

- Security through explicit permissions
- Built-in testing, linting, formatting
- Works with npm and JSR packages
- Easy deployment options
- Great developer experience
- Active community

You're ready to build with Deno!
