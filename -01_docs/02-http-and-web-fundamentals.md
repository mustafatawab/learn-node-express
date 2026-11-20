# 2. HTTP and Web Fundamentals

HTTP is the protocol browsers and APIs use to exchange requests and responses. Understanding it makes Express routes easier to design and debug.

## Learning outcomes

You should be able to read a raw request/response, select a method and status for a basic API operation, and explain when retrying a request can be unsafe.

**Prerequisites:** Guide 1.  
**Key terms:** header (metadata), representation (the resource data sent in a response), safe method (intended not to change server state), idempotent (repeating a request has the same intended effect).

## Requests and responses

A request has a method, URL, headers, and sometimes a body. A response has a status code, headers, and sometimes a body.

~~~http
POST /api/tasks HTTP/1.1
Content-Type: application/json

{"title":"Read about HTTP"}
~~~

`Content-Type` says what a body contains; `Accept` describes formats the client can receive. A body is not automatically JSON: the server must parse and validate it.

## Methods and status codes

- `GET` reads a resource and should not change server state.
- `POST` commonly creates a resource or starts an operation.
- `PUT` commonly replaces a resource; `PATCH` changes part of one.
- `DELETE` removes a resource.

Common codes: `200 OK`, `201 Created`, `204 No Content`, `400 Bad Request`, `401 Unauthorized` (authentication missing or invalid), `403 Forbidden` (caller not allowed), `404 Not Found`, `409 Conflict`, `422 Unprocessable Content`, and `500 Internal Server Error`.

## Resource URLs and retries

~~~text
GET    /api/tasks       list tasks
POST   /api/tasks       create a task
GET    /api/tasks/42    read task 42
PATCH  /api/tasks/42    update task 42
DELETE /api/tasks/42    delete task 42
~~~

An operation is idempotent when repeating it has the same intended effect as doing it once. HTTP defines safe methods such as GET as idempotent; PUT and DELETE are also idempotent. POST is not generally idempotent. An idempotent operation may still create logs or return a different response on a retry. This matters when clients retry after timeouts. Never put secrets in URLs; URLs can appear in history and logs. See [RFC 9110 `9.2](https://www.rfc-editor.org/rfc/rfc9110.html#section-9.2).

## Cookies, CORS, and TLS

Cookies let browsers send stored values with later requests. CORS controls which web origins may read cross-origin responses; it is not authentication and does not stop non-browser clients. TLS (HTTPS) protects data in transit.

## Practice

Use curl or an API client to send GET and POST requests. Record method, URL, headers, status, and body. Try invalid JSON and observe the response.

**Check your work:** explain why a POST retry can create duplicate records, why a 401 differs from 403, and why CORS does not block a command-line client.

**Common mistake:** assuming the status code alone tells the client everything. Define a stable response body and document it too.

## Further reading

- [MDN: HTTP overview](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview)
- [MDN: HTTP methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)
- [MDN: Status codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
