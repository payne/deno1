---
marp: true
theme: default
paginate: true
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

## Summary

- Deno makes CLI development straightforward
- Built-in argument parsing with @std/cli
- Easy user input with prompts
- Environment variable support
- Compile to standalone executables
- Cross-platform distribution

Ready for advanced topics!
