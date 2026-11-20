# 3. Express Fundamentals

Express handles each request by running matching middleware and route handlers in registration order.

## Learning outcomes

You should be able to trace a request through middleware, explain where parsed input comes from, and implement route, not-found, and error paths.

**Prerequisites:** Guides 1–2.  
**Key terms:** middleware (function in the request pipeline), router (group of routes), error middleware (four-argument handler for forwarded errors).

## A small server

~~~js
import express from "express";
const app = express();
app.use(express.json());

app.get("/health", (req, res) => {
  res.status(200).json({ status: "ok" });
});
app.post("/messages", (req, res) => {
  res.status(201).json({ message: req.body.message });
});
app.listen(3000, () => console.log("Listening on port 3000"));
~~~

`express.json()` parses JSON bodies for later handlers. Parsing does not validate the fields or values.

## Request data and middleware

- `req.params`: path values such as `/users/:id`.
- `req.query`: URL query values such as `?page=2`.
- `req.body`: data parsed by body-parsing middleware.
- `req.headers`: metadata such as content type and authorization.

Client-provided values are untrusted. Validate them before business logic or database queries. Middleware must send a response or call `next()`; otherwise the request hangs. Middleware registered above a route runs before it.

## Routers and errors

An `express.Router()` groups related endpoints:

~~~js
import express from "express";

const app = express();
const taskRouter = express.Router();
app.use(express.json());

taskRouter.get("/", (req, res) => res.json({ data: [] }));
taskRouter.post("/", (req, res) => res.status(201).json({ data: req.body }));
app.use("/api/tasks", taskRouter);

app.listen(3000, () => console.log("Listening on port 3000"));
~~~

This is a runnable routing example once Express is installed and the project uses ECMAScript modules. The POST route is intentionally minimal; add validation before accepting real input. Add a not-found handler after routes and an error handler after that:

~~~js
app.use((req, res) => res.status(404).json({ message: "Route not found" }));
app.use((error, req, res, next) => {
  if (res.headersSent) return next(error);
  console.error("Unhandled request error", {
    name: error instanceof Error ? error.name : "UnknownError",
  });
  res.status(500).json({ message: "Internal server error" });
});
~~~

Error handlers have four parameters. Express 5 forwards rejected Promises from async handlers; Express 4 needs an async wrapper or explicit `next(error)`. Check the local `package.json` before relying on version-specific behavior. The example above is abbreviated: production error handling should distinguish expected client errors from unexpected failures and must delegate when `res.headersSent` is already true.

## Practice

Explore `03_express`, `04_routes`, and `05_middleware`. Add a health route, JSON POST route, not-found handler, and error handler. Confirm body parsing comes before the POST route.

**Check your work:** malformed JSON does not become a successful create; unknown paths return 404; thrown or rejected route errors reach one error handler; each request gets exactly one response.

**Common mistake:** calling `next()` and assuming code after it waits for the downstream handler. It continues immediately.

## Further reading

- [Express 5: Routing](https://expressjs.com/en/5x/guide/routing.html)
- [Express 5: Middleware](https://expressjs.com/en/5x/guide/using-middleware/)
- [Express 5: Error handling](https://expressjs.com/en/5x/guide/error-handling.html)
