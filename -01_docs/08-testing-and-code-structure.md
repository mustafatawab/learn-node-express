# 8. Testing and Code Structure

Tests make behavior repeatable and help prevent regressions. Structure should clarify responsibilities without adding layers a project does not need.

## Learning outcomes

You should be able to select a test level for a behavior, isolate test data, and trace a request across code boundaries.

**Prerequisites:** Guides 3–7.  
**Key terms:** unit test (small isolated behavior), integration test (real boundary between components), fixture (known test data), regression (previously working behavior that breaks).

## A practical test mix

- **Unit tests** check a small function or business rule in isolation.
- **Integration tests** check boundaries such as a route with middleware or service with database.
- **End-to-end tests** exercise the app through its public interface.

Test valid and invalid input, missing records, unauthenticated requests, forbidden access, and important failures. Keep tests deterministic; isolate data and clean up afterward. Node.js has a built-in test runner, and other frameworks are options. Check local scripts and the supported Node version before copying commands.

Here is a complete unit-test example using Node's built-in runner and assertion library:

~~~js
// add.js
export function add(a, b) {
  if (!Number.isFinite(a) || !Number.isFinite(b)) {
    throw new TypeError("Inputs must be finite numbers");
  }
  return a + b;
}
~~~

~~~js
// add.test.js
import test from "node:test";
import assert from "node:assert/strict";
import { add } from "./add.js";

test("adds two finite numbers", () => {
  assert.equal(add(2, 3), 5);
});
test("rejects non-finite input", () => {
  assert.throws(() => add(Infinity, 1), TypeError);
});
~~~

Run it with `node --test` on a supported Node version. This example assumes ECMAScript modules, such as `"type": "module"` in `package.json`. For route-level tests, use an HTTP test client and a test database; do not point destructive test setup at production.

## Organize by responsibility

~~~text
route       maps HTTP endpoints and middleware
controller  translates HTTP input and output
service     applies business rules
repository  reads and writes persistent data
model       describes stored data when the data library uses one
~~~

This is an option, not a mandatory template. A small project may need fewer layers. Keep HTTP details out of database code and avoid duplicating business rules across routes. Trace a request through `07_todo_with_neon` and `08_mongodb` before adding abstractions.

Keep side effects at clear boundaries. A service that depends on a repository or small interface is easier to test than one that reaches into global state everywhere. Mock only where useful; test real boundaries when practical.

## Practice

Test a task validation schema and business rule. Add an API-level check that an unauthenticated user cannot read protected data and one user's task is not visible to another.

**Check your work:** the test fails if the implementation is removed; it does not depend on test order; failure output identifies the behavior; a clean run can be repeated.

**Common mistake:** testing only the happy path or asserting implementation details that are free to change. Test observable behavior and important failure cases.

## Further reading

- [Node.js: Test runner](https://nodejs.org/api/test.html)
- [Express: Performance and reliability](https://expressjs.com/en/advanced/best-practice-performance.html)
