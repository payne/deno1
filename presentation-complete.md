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

## Summary: Getting Started

- Installing Deno is quick and easy
- Single executable with all tools
- TypeScript works immediately
- Import from URLs or npm
- Configuration via `deno.json`
- Excellent IDE support

Ready to write TypeScript!

---

# TypeScript with Deno

## First-Class TypeScript Support

---

## Why TypeScript + Deno?

**Zero Configuration:**
- No `tsconfig.json` needed (though you can use one)
- No compilation step in development
- Instant feedback from type checking
- Built-in type checker

**Modern Defaults:**
- Strict mode enabled
- Latest ECMAScript features
- Module resolution just works

---

## Hello TypeScript

```typescript
// greet.ts
function greet(name: string): string {
  return `Hello, ${name}!`;
}

const result = greet("Deno");
console.log(result);

// Type error caught automatically
// greet(42); // Error: Argument of type 'number' is not assignable
```

Run it:
```bash
deno run greet.ts
```

Types are checked, then stripped at runtime.

---

## Type Annotations

```typescript
// Basic types
const name: string = "Alice";
const age: number = 30;
const isActive: boolean = true;

// Arrays
const numbers: number[] = [1, 2, 3];
const items: Array<string> = ["a", "b", "c"];

// Objects
const user: { name: string; age: number } = {
  name: "Bob",
  age: 25
};
```

---

## Interfaces and Types

```typescript
// Interface
interface User {
  id: number;
  name: string;
  email: string;
  age?: number; // Optional property
}

// Type alias
type Status = "active" | "inactive" | "pending";

// Using them
const user: User = {
  id: 1,
  name: "Alice",
  email: "alice@example.com"
};

const status: Status = "active";
```

---

## Functions

```typescript
// Function with types
function add(a: number, b: number): number {
  return a + b;
}

// Arrow function
const multiply = (a: number, b: number): number => a * b;

// Optional parameters
function greet(name: string, greeting?: string): string {
  return `${greeting ?? "Hello"}, ${name}!`;
}

// Default parameters
function makeUser(name: string, role: string = "user") {
  return { name, role };
}
```

---

## Async/Await

```typescript
// Async function
async function fetchUser(id: number): Promise<User> {
  const response = await fetch(`https://api.example.com/users/${id}`);
  const data = await response.json();
  return data as User;
}

// Using it
const user = await fetchUser(1);
console.log(user.name);
```

Top-level `await` works in Deno!

---

## Generics

```typescript
// Generic function
function first<T>(arr: T[]): T | undefined {
  return arr[0];
}

const num = first([1, 2, 3]); // number | undefined
const str = first(["a", "b"]); // string | undefined

// Generic interface
interface Response<T> {
  data: T;
  status: number;
  message: string;
}

const userResponse: Response<User> = {
  data: { id: 1, name: "Alice", email: "alice@example.com" },
  status: 200,
  message: "Success"
};
```

---

## Deno Namespace Types

Deno provides built-in types:

```typescript
// File operations
const file: Deno.FsFile = await Deno.open("data.txt");

// Process info
const os: string = Deno.build.os;
const arch: string = Deno.build.arch;

// Environment
const home: string | undefined = Deno.env.get("HOME");

// Command execution
const command: Deno.Command = new Deno.Command("echo", {
  args: ["hello"]
});
```

---

## Type Imports

```typescript
// Import just the type
import type { User } from "./types.ts";

// Import value and type
import { type Config, loadConfig } from "./config.ts";

// Use type in JSDoc (for JavaScript files)
/** @type {string} */
const name = "Alice";
```

---

## Working with JSON

```typescript
// Define the shape
interface Config {
  port: number;
  host: string;
  debug: boolean;
}

// Read and parse
const text = await Deno.readTextFile("config.json");
const config: Config = JSON.parse(text);

// Type-safe access
console.log(`Server on ${config.host}:${config.port}`);
```

---

## Enums

```typescript
// Numeric enum
enum Direction {
  Up,
  Down,
  Left,
  Right
}

const move: Direction = Direction.Up;

// String enum
enum LogLevel {
  Error = "ERROR",
  Warn = "WARN",
  Info = "INFO",
  Debug = "DEBUG"
}

function log(level: LogLevel, message: string) {
  console.log(`[${level}] ${message}`);
}
```

---

## Union Types

```typescript
// Union type
type Result = string | number | boolean;

function format(value: Result): string {
  if (typeof value === "string") {
    return value.toUpperCase();
  } else if (typeof value === "number") {
    return value.toFixed(2);
  } else {
    return value ? "YES" : "NO";
  }
}

// Discriminated unions
type Response =
  | { status: "success"; data: User }
  | { status: "error"; message: string };
```

---

## Type Guards

```typescript
interface Dog {
  bark: () => void;
}

interface Cat {
  meow: () => void;
}

// Type guard function
function isDog(pet: Dog | Cat): pet is Dog {
  return (pet as Dog).bark !== undefined;
}

function makeSound(pet: Dog | Cat) {
  if (isDog(pet)) {
    pet.bark(); // TypeScript knows it's a Dog
  } else {
    pet.meow(); // TypeScript knows it's a Cat
  }
}
```

---

## Module Types

```typescript
// types.ts - Export types
export interface User {
  id: number;
  name: string;
}

export type UserRole = "admin" | "user" | "guest";

// main.ts - Import and use
import type { User, UserRole } from "./types.ts";

const user: User = { id: 1, name: "Alice" };
const role: UserRole = "admin";
```

---

## Custom tsconfig.json

You can override Deno's defaults:

```json
{
  "compilerOptions": {
    "strict": true,
    "lib": ["deno.window"],
    "allowJs": true,
    "checkJs": true,
    "noImplicitAny": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true
  }
}
```

Reference it: `deno run --config tsconfig.json main.ts`

---

## Type Checking

```bash
# Check types without running
deno check main.ts

# Check all TypeScript files
deno check **/*.ts

# Include remote dependencies
deno cache --reload main.ts
```

Errors are caught before runtime!

---

## Summary: TypeScript

- TypeScript works out of the box
- Zero configuration needed
- Full type safety with Deno APIs
- Modern TypeScript features supported
- Optional custom configuration
- Type checking is fast and reliable

Now let's build something real!

---

# Building CLI Applications

## Practical Deno Development

---

## Why CLI Apps with Deno?

**Perfect fit:**
- Single executable compilation
- No runtime dependencies needed
- Built-in permissions system
- Easy distribution
- Cross-platform support
- Fast startup time

---

## Simple CLI: Hello World

```typescript
// cli.ts
console.log("Welcome to my CLI!");
console.log(`You're running on ${Deno.build.os}`);
console.log(`Current directory: ${Deno.cwd()}`);
```

Run it:
```bash
deno run cli.ts
```

That's it!

---

## Reading Command Line Arguments

```typescript
// args.ts
console.log("Arguments:", Deno.args);

// Run with: deno run args.ts hello world 123
// Output: Arguments: [ "hello", "world", "123" ]
```

`Deno.args` gives you raw arguments as an array.

---

## Parsing Arguments with @std/cli

```typescript
// add.ts
import { parseArgs } from "@std/cli/parse-args";

const args = parseArgs(Deno.args);

const num1 = parseFloat(args._[0] as string);
const num2 = parseFloat(args._[1] as string);

if (isNaN(num1) || isNaN(num2)) {
  console.error("Please provide two numbers");
  Deno.exit(1);
}

console.log(`${num1} + ${num2} = ${num1 + num2}`);
```

---

## Advanced Argument Parsing

```typescript
// sum.ts
import { parseArgs } from "@std/cli/parse-args";

const args = parseArgs(Deno.args, {
  boolean: ["help", "version"],
  alias: { h: "help", v: "version" },
  default: { help: false, version: false }
});

if (args.help) {
  console.log("Usage: sum [numbers...]");
  console.log("Calculate the sum of numbers");
  Deno.exit(0);
}

if (args.version) {
  console.log("sum v1.0.0");
  Deno.exit(0);
}
```

---

## Complete Sum Example

```typescript
const numbers = args._.map(n => parseFloat(n as string));

if (numbers.length === 0) {
  console.error("Error: Please provide numbers to sum");
  Deno.exit(1);
}

if (numbers.some(isNaN)) {
  console.error("Error: All arguments must be valid numbers");
  Deno.exit(1);
}

const sum = numbers.reduce((acc, n) => acc + n, 0);
console.log(`Sum: ${sum}`);
```

---

## User Input with Prompts

```typescript
// prompt.ts
const name = prompt("What's your name?");

if (name) {
  console.log(`Hello, ${name}!`);
} else {
  console.log("No name provided");
}

const age = prompt("What's your age?");
const ageNum = age ? parseInt(age) : 0;

console.log(`You are ${ageNum} years old`);
```

Run: `deno run prompt.ts`

---

## Secure Password Input

```typescript
// secret.ts
import { promptSecret } from "@std/cli/prompt-secret";

console.log("Enter your password:");
const password = promptSecret();

if (password) {
  console.log(`Password length: ${password.length}`);
  // Don't log the actual password!
} else {
  console.log("No password entered");
}
```

Text won't be visible while typing!

---

## Environment Variables

```typescript
// env.ts
const apiKey = Deno.env.get("API_KEY");
const port = Deno.env.get("PORT") ?? "3000";

if (!apiKey) {
  console.error("API_KEY not set!");
  Deno.exit(1);
}

console.log(`Using port: ${port}`);

// Set environment variable
Deno.env.set("MY_VAR", "value");
```

Run: `API_KEY=secret deno run --allow-env env.ts`

---

## Using .env Files

Create `.env`:
```
API_KEY=your-secret-key
DATABASE_URL=postgres://localhost/mydb
PORT=8000
DEBUG=true
```

Load it:
```typescript
import { load } from "@std/dotenv";

const env = await load();

console.log("API Key:", env.API_KEY);
console.log("Port:", env.PORT);
```

---

## Complete CLI Example: Updater

```typescript
// updater.ts
import { load } from "@std/dotenv";
import { promptSecret } from "@std/cli/prompt-secret";

// Load environment
await load({ export: true });

const apiKey = Deno.env.get("API_KEY");
if (!apiKey) {
  console.error("API_KEY not found in environment");
  Deno.exit(1);
}
```

---

## Updater Example (continued)

```typescript
// Get user input
const username = prompt("Enter username:");
if (!username) {
  console.error("Username required");
  Deno.exit(1);
}

console.log("Enter password:");
const password = promptSecret();
if (!password) {
  console.error("Password required");
  Deno.exit(1);
}

console.log(`Updating user: ${username}`);
console.log("(Using API key from environment)");
```

---

## File Operations

```typescript
// Read a file
const text = await Deno.readTextFile("data.txt");
console.log(text);

// Write a file
await Deno.writeTextFile("output.txt", "Hello, Deno!");

// Read JSON
const config = JSON.parse(await Deno.readTextFile("config.json"));

// Write JSON
await Deno.writeTextFile(
  "data.json",
  JSON.stringify({ name: "Alice" }, null, 2)
);
```

Requires `--allow-read` and `--allow-write` permissions.

---

## Working with Paths

```typescript
import { join, dirname, basename } from "@std/path";

const filePath = "/home/user/documents/file.txt";

console.log(dirname(filePath));   // /home/user/documents
console.log(basename(filePath));  // file.txt

const newPath = join("/home", "user", "file.txt");
console.log(newPath); // /home/user/file.txt

// Current directory
const cwd = Deno.cwd();
const fullPath = join(cwd, "data", "file.txt");
```

---

## Listing Files

```typescript
// List files in directory
for await (const entry of Deno.readDir(".")) {
  console.log(entry.name, entry.isFile ? "(file)" : "(dir)");
}

// Check if file exists
try {
  await Deno.stat("file.txt");
  console.log("File exists");
} catch {
  console.log("File not found");
}
```

---

## Making HTTP Requests

```typescript
// GET request
const response = await fetch("https://api.github.com/repos/denoland/deno");
const data = await response.json();
console.log(`Stars: ${data.stargazers_count}`);

// POST request
const postResponse = await fetch("https://api.example.com/data", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ name: "Alice" })
});

const result = await postResponse.json();
```

Requires `--allow-net` permission.

---

## Styling Output with Chalk

```typescript
import chalk from "npm:chalk@5";

console.log(chalk.blue("Hello world!"));
console.log(chalk.red.bold("Error: Something went wrong"));
console.log(chalk.green("✓ Success!"));
console.log(chalk.yellow.underline("Warning"));

// Combine styles
console.log(
  chalk.bgBlue.white(" INFO ") + " " +
  chalk.blue("This is an info message")
);
```

---

## Building a Real CLI: Structure

```
my-cli/
├── deno.json
├── main.ts
├── src/
│   ├── commands/
│   │   ├── add.ts
│   │   └── list.ts
│   └── utils/
│       ├── db.ts
│       └── logger.ts
└── .env
```

---

## CLI with Commands

```typescript
// main.ts
import { parseArgs } from "@std/cli/parse-args";

const args = parseArgs(Deno.args);
const command = args._[0] as string;

switch (command) {
  case "add":
    await import("./src/commands/add.ts");
    break;
  case "list":
    await import("./src/commands/list.ts");
    break;
  default:
    console.log("Usage: mycli <add|list> [options]");
    Deno.exit(1);
}
```

---

## Deno Tasks for Development

```json
// deno.json
{
  "tasks": {
    "dev": "deno run --allow-read --allow-write --allow-env main.ts",
    "start": "deno run --allow-all main.ts",
    "compile": "deno compile --allow-all --output mycli main.ts"
  },
  "imports": {
    "@std/cli": "jsr:@std/cli@^1.0.0",
    "@std/dotenv": "jsr:@std/dotenv@^0.225.0"
  }
}
```

Run: `deno task dev`

---

## Compiling to Executable

```bash
# Compile for current platform
deno compile --allow-all --output mycli main.ts

# Cross-compile for other platforms
deno compile --allow-all --target x86_64-unknown-linux-gnu --output mycli-linux main.ts
deno compile --allow-all --target x86_64-pc-windows-msvc --output mycli.exe main.ts
deno compile --allow-all --target x86_64-apple-darwin --output mycli-mac main.ts
```

Now distribute the binary!

---

## Error Handling

```typescript
try {
  const data = await Deno.readTextFile("config.json");
  const config = JSON.parse(data);
  // Use config...
} catch (error) {
  if (error instanceof Deno.errors.NotFound) {
    console.error("Config file not found");
  } else if (error instanceof SyntaxError) {
    console.error("Invalid JSON in config file");
  } else {
    console.error("Unexpected error:", error.message);
  }
  Deno.exit(1);
}
```

---

## Best Practices

1. Always validate user input
2. Provide helpful error messages
3. Use exit codes (0 for success, non-zero for errors)
4. Include `--help` and `--version` flags
5. Handle Ctrl+C gracefully
6. Use environment variables for sensitive data
7. Test with different permissions
8. Document required permissions

---

## Summary: CLI Applications

- Deno makes CLI development straightforward
- Built-in argument parsing with @std/cli
- Easy user input with prompts
- Environment variable support
- Compile to standalone executables
- Cross-platform distribution

Ready for advanced topics!

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

## Final Summary

- Security through explicit permissions
- Built-in testing, linting, formatting
- Works with npm and JSR packages
- Easy deployment options
- Great developer experience
- Active community

**You're ready to build with Deno!**

---

## Thank You!

Questions?

**Resources:**
- Slides: (your repo)
- Reference: https://reverentgeek.com/build-a-command-line-application-with-deno-2/
- Docs: https://docs.deno.com
- Community: https://discord.gg/deno
