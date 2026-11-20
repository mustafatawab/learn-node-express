# 4. API Design and Validation

An API is a contract between a server and its clients. Clear routes, validation, and consistent responses make that contract easier to use and change.

## Learning outcomes

You should be able to describe an endpoint before implementing it, validate untrusted input, and explain how a breaking change affects API consumers.

**Prerequisites:** Guides 2–3.  
**Key terms:** contract (documented client/server agreement), schema (shape and constraints for data), compatibility (whether existing clients still work after a change).

## Design a resource

For a task, decide its fields, identity, ownership, and allowed operations:

~~~text
GET    /api/tasks?page=1&limit=20
POST   /api/tasks
GET    /api/tasks/:taskId
PATCH  /api/tasks/:taskId
DELETE /api/tasks/:taskId
~~~

Define accepted input, returned fields, and error cases. Do not expose database records blindly.

## Validate at the boundary

Validate route parameters, query values, and request bodies before business logic. Reject missing fields, wrong types, unreasonable lengths, and unexpected values. Validation is separate from authorization: a valid task ID does not mean the caller may access it.

The TypeScript projects use Zod. Here is a minimal runnable JavaScript demonstration; it keeps tasks in memory only, so data disappears when the process stops. In a real app, call a service/repository after validation. Express 5 forwards a rejected Promise; Express 4 projects must forward it explicitly.

~~~js
import express from "express";
import { randomUUID } from "node:crypto";
import { z } from "zod";

const app = express();
app.use(express.json());

const createTaskSchema = z.object({
  title: z.string().trim().min(1).max(120),
});

app.post("/api/v1/tasks", (req, res) => {
  const result = createTaskSchema.safeParse(req.body);
  if (!result.success) {
    return res.status(400).json({
      error: { code: "VALIDATION_ERROR", message: "Invalid request body" },
    });
  }
  const task = { id: randomUUID(), ...result.data };
  return res.status(201).json({ data: task });
});

app.listen(3000, () => console.log("Listening on port 3000"));
~~~

Install `express` and `zod` in a scratch project and set `"type": "module"` in `package.json` to run it. The example demonstrates validation; it is not a persistence layer or complete error-handling setup.

Choose consistent response shapes, for example `{ "data": [...] }` for results and `{ "error": { "code": "...", "message": "..." } }` for failures. Never return stack traces or raw database errors. Cap pagination size, use stable ordering, and decide how updates treat omitted or unknown fields.

Give errors stable machine-readable codes; clients should not have to parse a translated message. Keep API timestamps unambiguous and avoid assuming a client's language, timezone, address format, or currency. Store domain values in locale-neutral forms and format them for the user at the appropriate boundary.

Changing field names or response meaning can break clients. Prefer additive changes; handle breaking changes deliberately.

## Practice

Add `PATCH /api/tasks/:taskId`. Validate the ID and body, return 404 when missing, and ensure users cannot update another user's task.

**Check your work:** reject empty titles and oversized input; unknown fields follow a documented policy; valid input reaches the service as validated data; responses match the contract.

**Common mistake:** validating in the frontend only. A client can be bypassed, so the server validates every request.

For APIs used by another team or public clients, keep a machine-readable [OpenAPI description](https://spec.openapis.org/oas/) beside the code and review it for drift.

For example, an OpenAPI document starts with metadata and describes an operation's response:

~~~yaml
openapi: 3.1.0
info:
  title: Task API
  version: 1.0.0
paths:
  /health:
    get:
      operationId: healthCheck
      responses:
        "200":
          description: Service is healthy
          content:
            application/json:
              schema:
                type: object
                required: [status]
                properties:
                  status:
                    type: string
                    const: ok
~~~

This excerpt describes one endpoint; document request bodies, authentication, errors, and reusable schemas for the rest of the API. It uses OpenAPI 3.1.0 for broad tooling compatibility; select a specification version supported by your validator, code generator, and clients.

## Further reading

- [Express 5 API reference](https://expressjs.com/en/5x/api.html)
- [OWASP: REST Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html)
