# 1. Node.js Foundations

Node.js is a JavaScript runtime. It lets programs use server-side capabilities such as files, networking, and database clients. An Express server is a Node.js program.

## Learning outcomes

By the end, you should be able to run a small Node project, explain its module format, await a file operation, and identify where a rejected Promise is handled.

**Prerequisites:** basic JavaScript expressions, functions, arrays, and objects.  
**Key terms:** runtime (program that executes code), module (a file with imports/exports), dependency (external package), Promise (a value representing work that may finish later).

## Set up and run

~~~bash
node --version
npm --version
npm install
npm run
node index.js
~~~

`package.json` describes a project, its dependencies, and scripts. The lockfile records resolved dependency versions; keep it committed for repeatable installs. Use the entry point defined by the folder. This repository has JavaScript and TypeScript projects.

## Modules

Node projects commonly use ECMAScript modules (`import`/`export`) or CommonJS (`require`). Follow the project's format. Setting `"type": "module"` enables ECMAScript module syntax in its JavaScript files.

~~~js
// math.js
export function add(a, b) { return a + b; }
// index.js
import { add } from "./math.js";
console.log(add(2, 3));
~~~

## Asynchronous work

File, network, and database operations take time. Promises and `async`/`await` let code wait for I/O without blocking the JavaScript thread while that work is pending.

~~~js
import { readFile } from "node:fs/promises";
try {
  const contents = await readFile("notes.txt", "utf8");
  console.log(contents);
} catch (error) {
  console.error("Could not read notes:", error);
}
~~~

An `async` function returns a Promise. Await it or handle rejection; do not start asynchronous work and forget to handle it.

Node coordinates JavaScript callbacks and asynchronous I/O through its event loop. This is efficient for I/O-heavy APIs, but a long CPU-heavy calculation in a request handler still blocks other JavaScript work in that process. Measure first; then consider a worker thread, separate process, or background job for CPU-heavy work.

## Configuration and debugging

Environment variables hold environment-specific settings. Never commit real passwords, tokens, or connection strings. Validate required settings at startup. When debugging, read the stack trace, reproduce the smallest failing case, and avoid logging secrets or private data.

## Practice

Explore `01_starting` and `02_filehandler`. Identify entry points and module formats. Write a function that reads a file asynchronously and handles a missing-file error.

**Check your work:** run it from the project folder; show the success path and the missing-file path; explain why the function needs `await` or `return` for its caller to observe completion. Do not log file contents if they may contain secrets.

**Common mistake:** resolving a relative path from an assumed folder. Check `process.cwd()` and construct paths deliberately.

**Stretch task:** compare reading a large file with a streaming API and explain how streaming controls memory use. Do not optimize before measuring the actual workload.

## Further reading

- [Node.js introduction](https://nodejs.org/learn/getting-started/introduction-to-nodejs)
- [Node.js ECMAScript modules](https://nodejs.org/api/esm.html)
- [Blocking vs non-blocking I/O](https://nodejs.org/en/learn/asynchronous-work/overview-of-blocking-vs-non-blocking)
