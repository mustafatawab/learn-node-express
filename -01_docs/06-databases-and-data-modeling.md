# 6. Databases and Data Modeling

Databases persist application state. A useful model captures domain rules, protects data integrity, and supports the queries an app needs.

## Learning outcomes

You should be able to model a one-to-many relationship, explain constraints and transactions, read a migration, and recognize an inefficient query pattern.

**Prerequisites:** Guides 1 and 4.  
**Key terms:** constraint (database-enforced rule), transaction (all-or-nothing unit of work), migration (versioned schema change), index (data structure that speeds selected lookups at a cost).

## Relational and document databases

PostgreSQL stores rows in tables with defined columns and relationships. Constraints and transactions help enforce rules. MongoDB stores documents in collections and can fit document-shaped data. Neither removes the need to model data and validate requests.

This repo demonstrates Postgres with Neon and Prisma in `07_todo_with_neon`, and MongoDB with Mongoose in `08_mongodb`.

## Integrity, queries, and migrations

Use stable identifiers and database constraints for invariants such as unique email addresses and required relationships. Application checks improve error messages, but constraints also protect against races and other write paths. For SQL, use parameterized queries or an ORM; never concatenate user input into SQL. Use transactions when several changes must succeed or fail together.

Schema changes need a versioned migration history. Review migrations, especially those that drop columns or alter data. Plan changes so deployed application and schema versions can safely overlap.

Example SQL: a task belongs to one user; a unique constraint enforces email uniqueness even if simultaneous requests race:

~~~sql
CREATE TABLE app_user (
  id uuid PRIMARY KEY,
  email text NOT NULL UNIQUE
);
CREATE TABLE task (
  id uuid PRIMARY KEY,
  owner_id uuid NOT NULL REFERENCES app_user(id),
  title text NOT NULL CHECK (length(trim(title)) BETWEEN 1 AND 120),
  created_at timestamptz NOT NULL DEFAULT now()
);
CREATE INDEX task_owner_created_idx ON task (owner_id, created_at DESC);
~~~

The index is only useful if it matches real query patterns; confirm with query plans and production-like data. Keep database constraints even when the API validates the same rule.

An ownership-filtered, parameterized query can combine access control with lookup:

~~~sql
SELECT id, title, created_at
FROM task
WHERE owner_id = $1
ORDER BY created_at DESC, id DESC
LIMIT $2;
~~~

The owner ID comes from the authenticated server-side identity, not a trusted client-supplied owner field. The second sort column makes ties deterministic. Use your database library's parameter binding rather than interpolating values into SQL.

Transactions protect a group of writes that must succeed together. For example, creating a task and an audit row should either persist both or neither:

~~~text
BEGIN
  INSERT task ...
  INSERT audit_event ...
COMMIT
~~~

If either insert fails, roll back. In application code, use the database driver's or ORM's transaction API and use its transaction-scoped client for every operation in the unit.

Prisma configuration and schema are in `07_todo_with_neon/prisma.config.ts` and `07_todo_with_neon/prisma/schema.prisma`. That project uses Prisma ORM 7; the older Neon guide used direct SQL. Use the versioned Prisma 7 docs below when following its CLI workflow; the general concepts apply across versions.

Fetch only needed fields and rows. Index measured query patterns, considering write and storage costs. Avoid one query per row when batching can do the work. Reuse a database client or pool; do not open one per request or close a shared pool after each query.

## Practice

In the todo project, find the user-to-todo relationship, unique email constraint, and migration history. Add a field through a migration and consider existing rows. In MongoDB, inspect the Mongoose schema, validation, and indexes.

**Check your work:** explain what happens when a task's user is deleted; demonstrate an atomic multi-row operation with a transaction; review a migration before applying it; show which query an index supports.

**Common mistake:** treating a successful application-level “does this email exist?” check as uniqueness protection. Two concurrent requests can both pass that check; the database constraint is authoritative.

Also decide how email normalization and uniqueness work for your product. Case folding and internationalized email handling have product and identity implications; do not invent normalization rules without documenting them.

## Further reading

- [PostgreSQL: Constraints](https://www.postgresql.org/docs/current/ddl-constraints.html)
- [PostgreSQL: Transactions](https://www.postgresql.org/docs/current/tutorial-transactions.html)
- [Prisma ORM 7: Development and production migrations](https://www.prisma.io/docs/orm/v7/prisma-migrate/workflows/development-and-production)
- [Prisma ORM 7: Database connections](https://www.prisma.io/docs/orm/v7/prisma-client/setup-and-configuration/databases-connections)
- [Mongoose: Schemas](https://mongoosejs.com/docs/guide.html)
- [Mongoose: Validation](https://mongoosejs.com/docs/validation.html)
