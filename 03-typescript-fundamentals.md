---
marp: true
theme: default
paginate: true
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

## Summary

- TypeScript works out of the box
- Zero configuration needed
- Full type safety with Deno APIs
- Modern TypeScript features supported
- Optional custom configuration
- Type checking is fast and reliable

Now let's build something real!
