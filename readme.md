# Learn Node.js and Express

This repository is a hands-on path from basic Node.js concepts to building APIs with Express, authentication, and databases. Work through the folders in order; each folder is a small learning project.

For the step-by-step backend engineering curriculum, start with the [documentation guide index](-01_docs/README.md).

## Before you start

You should be comfortable with JavaScript basics, including functions, objects, promises, and `async`/`await`, and know how to use a terminal. Install a current Node.js LTS release and npm.

To run a folder's project, move into it, install its dependencies, and follow that folder's README or entry point. For example:

```bash
cd 03_express
npm install
node index.js
```

The projects may use different entry points and scripts, so check each folder's `package.json` before running it.

## Learning path

| Folder | Topics |
| --- | --- |
| `01_starting` | Running JavaScript with Node.js and starting a project |
| `02_filehandler` | Working with files, paths, and promises |
| `03_express` | Creating a basic Express server and serving static files |
| `04_routes` | Organizing routes and serving a public page |
| `05_middleware` | Middleware and request handling |
| `06_simple-crud` | A first create, read, update, and delete API |
| `07_todo_with_neon` | A todo API with authentication and Prisma-backed Neon data |
| `08_mongodb` | An API organized into modules and backed by MongoDB |

The later projects build on earlier ideas. Read their local notes and inspect the code as you go; the table is a guide, not a promise that every production topic is covered in each project.

## Node.js and Express in a nutshell

Node.js runs JavaScript outside the browser. It gives your program access to server-side capabilities such as files, networking, and database clients. Express is a Node.js framework for handling HTTP requests and building web servers and APIs.

```js
import express from "express";

const app = express();
const port = 3000;

app.use(express.json()); // Parse JSON request bodies before routes run.

app.get("/", (req, res) => {
  res.json({ message: "Hello from Express" });
});

app.listen(port, () => {
  console.log(`Server listening on http://localhost:${port}`);
});
```

This example uses ES modules. In a project's `package.json`, set `"type": "module"` to use `import` in `.js` files, or use the module format already chosen by that project.

## Core Express concepts

### Requests and responses

- `req.method` and `req.path` describe the HTTP method and URL path.
- `req.params` contains values from route patterns such as `/users/:id`.
- `req.query` contains URL query values such as `?page=2`.
- `req.body` contains parsed request data when the matching body-parsing middleware is installed. Use `express.json()` for JSON and `express.urlencoded({ extended: true })` for form submissions.
- `req.headers` contains the request headers.
- `res.status(code)`, `res.json(data)`, `res.send(data)`, and `res.redirect(path)` help create a response.

```js
app.get("/users/:id", (req, res) => {
  res.json({ userId: req.params.id, page: req.query.page ?? "1" });
});

app.post("/users", (req, res) => {
  res.status(201).json({ user: req.body });
});
```

For a real API, validate `req.body` and other user input before using it. Do not treat client-provided data as trusted.

### Middleware and order

Middleware runs in the order it is registered. A middleware function can inspect or change the request, send a response, pass control onward with `next()`, or pass an error with `next(error)`.

```js
app.use((req, res, next) => {
  console.log(`${req.method} ${req.path}`);
  next();
});

app.get("/", (req, res) => {
  res.send("Home");
});
```

Calling `next()` starts the next matching middleware or route. Code immediately after `next()` runs right away; it does not wait for downstream handlers to finish. Put middleware that should run before a route above that route. Put error-handling middleware after routes.

Express has built-in middleware such as `express.json()`, `express.urlencoded()`, and `express.static()`. Use only the middleware your application needs, and understand what data it accepts or exposes.

### Errors

An Express error handler has four parameters and is registered after routes:

```js
app.use((err, req, res, next) => {
  console.error(err);
  res.status(500).json({ message: "Internal server error" });
});
```

In a real application, return useful client errors for expected cases, keep internal details out of public responses, and log enough context on the server to investigate failures. Follow the async error-handling approach supported by the Express version used in your project.

## What to learn as you build APIs

Use the later projects to practice these backend engineering skills:

1. **Design routes and responses:** choose clear resource URLs, HTTP methods, and status codes; keep response shapes consistent.
2. **Validate input:** check request data at the boundary and return clear `400` responses for invalid input.
3. **Structure the code:** keep routing, request handling, business logic, and data access understandable as the app grows.
4. **Work with data safely:** model relationships and constraints, use database operations carefully, and handle missing or conflicting records.
5. **Handle identity and access:** hash passwords with a suitable password-hashing library, authenticate users, and check authorization for every protected action.
6. **Configure the app:** read secrets and environment-specific settings from environment variables; never commit real credentials.
7. **Improve reliability:** add useful logs, automated tests for important behavior, and clear setup instructions.
8. **Prepare for deployment:** use secure settings, handle process shutdown and database connections, and deploy with environment-specific configuration.

These are topics to learn and verify, not guarantees that every example in this repository is production-ready. Review each project before using its patterns in a deployed service.

## Useful commands

Run these from the relevant project folder:

```bash
npm install          # Install dependencies listed by the project
node index.js        # Run this entry point if the project uses it
npm run              # List scripts defined in package.json
```

The entry point and available scripts vary by folder. Check `package.json` and the folder's notes.
