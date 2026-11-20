# 5. Errors, Logging, and Configuration

Good failure handling helps users understand what they can fix and helps developers investigate. Keep internal details on the server and return safe errors.

## Learning outcomes

You should be able to classify expected and unexpected failures, return safe responses, validate startup configuration, and choose useful log fields without recording secrets.

**Prerequisites:** Guides 1 and 3.  
**Key terms:** operational log (event record for diagnosis), secret (credential that grants access), graceful shutdown (stop new work and finish/close resources deliberately).

## Expected and unexpected errors

Expected conditions include invalid input, a missing record, or a duplicate value. Map them to suitable HTTP responses. Unexpected failures should be logged and returned as a generic 500.

~~~ts
import type { ErrorRequestHandler } from "express";

class AppError extends Error {
  constructor(
    public readonly statusCode: 400 | 401 | 403 | 404 | 409,
    message: string,
  ) {
    super(message);
  }
}

const errorHandler: ErrorRequestHandler = (error, req, res, next) => {
  if (res.headersSent) return next(error);
  if (error instanceof AppError) {
    return res.status(error.statusCode).json({
      error: { message: error.message },
    });
  }
  // In a real service, use a logger that redacts credentials and query values.
  console.error("Unhandled request error", {
    name: error instanceof Error ? error.name : "UnknownError",
  });
  return res.status(500).json({
    error: { message: "Internal server error" },
  });
};
app.use(errorHandler); // Register after routes.
~~~

This is a typed pattern; `app` must be the Express application created earlier in the file. Use the error flow supported by your Express version. Express 5 forwards rejected Promises from async handlers; Express 4 needs explicit forwarding. Do not send a response and then call `next()` for the same request.

In production code, use a typed application error with an allow-listed status and a deliberately safe public message. Do not trust arbitrary `error.statusCode` or `error.message` from third-party errors; map known failures and hide unknown ones. Send detailed diagnostics to a protected, redacting logger if needed.

## Logging and secrets

Logs should explain what happened, when, and which request was involved. Structured logs with request IDs and severity help in deployed services. Do not log passwords, tokens, cookies, payment details, or unnecessary personal data. Sanitize user-controlled strings to prevent forged log entries.

Keep environment-specific settings in environment variables. Validate required values at startup and provide a safe `.env.example` with placeholders. Keep `.env` and real credentials out of version control. If a secret leaks, rotate it; deleting it from the latest commit does not invalidate it.

~~~ts
const port = Number(process.env.PORT ?? 3000);
const databaseUrl = process.env.DATABASE_URL;
if (!databaseUrl) throw new Error("DATABASE_URL is required");
if (!Number.isInteger(port) || port < 1 || port > 65535) {
  throw new Error("PORT must be a valid TCP port");
}
~~~

Add a final not-found handler. During shutdown, stop accepting connections and close database clients cleanly.

## Practice

Add a request ID to logs, not-found handler, safe error response, and startup validation for PORT and DATABASE_URL. Confirm database failures do not expose the connection string.

**Check your work:** missing configuration prevents startup; a client validation error receives a 4xx; an unknown error receives a generic 500; logs have request context but no credentials or request-body secrets.

**Common mistake:** catching an error, logging it, and continuing as if the operation succeeded. Either recover explicitly or pass the error to the central handler.

## Further reading

- [Express 5: Error handling](https://expressjs.com/en/5x/guide/error-handling.html)
- [Node.js: Environment variables](https://nodejs.org/api/environment_variables.html)
- [OWASP: Logging Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html)
