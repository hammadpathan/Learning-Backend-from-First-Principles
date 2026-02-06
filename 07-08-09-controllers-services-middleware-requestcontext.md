# 07–08–09. Controllers, Services, Repositories, Middleware, Request Context

## Request lifecycle (high level)
A **request lifecycle** is the full operation from the moment a client sends an HTTP request to a server until the server sends a response back.

At a high level:
1. The request reaches the server (**entry point**). This is where the server receives traffic on its **port**.
2. The server is **listening** on that port.
3. **Routes** (`/users`, `/stuff`, etc.) redirect the request to a **handler/controller**.

A **handler/controller** is a predefined function that handles the request for a specific route. The terms **handler** and **controller** are used interchangeably.

```
Client sends request over HTTP
-> Request received on server PORT
-> Server listening on PORT
-> ROUTES redirect request to a HANDLER/CONTROLLER

These handlers/controllers are unique to each route.
```

We use multiple controllers as a design pattern. It keeps things simple, easy to extend, and easy to navigate rather than having one controller handle everything.

```
REMEMBER: different routes do different things
```

## Controller/Handler
A controller receives **two** objects:
- The **request** object
- The **response** object

The controller can read values from the request object and send values through the response object (e.g., `req`, `res`, depending on the library). It is also responsible for response codes.

> Note: not all programming languages work well with JSON directly, which is why we deserialize into native types (structs, classes, etc.).

Typical controller structure:
1. **Extract data** from the request object
   1. **Deserialization** (extract data from JSON into your native data format), also called **binding**
   2. If this fails, return **400 Bad Request**
2. **Validate and transform** data
   1. Validate params, body, etc.
   2. Ensure all data is in the correct shape/type
   3. Optionally transform data into specific structures for business logic

   Example:
   ```
   /books?sort=name
   /books?sort=date

   we transform data accordingly
   these fields can also be set defaults in case not specified like:
   /books

   we can do this conditionally before we even reach the DB
   ```
3. Call the **service layer** with transformed data

## Service layer
Think of this as a function that has nothing to do with frontend/backend concerns. It only processes data and returns it without worrying about response codes, validation, or request/response objects.

It **expects** everything to already be valid and well-shaped.

A service can call the **repository/database layer**.

Examples:
```
one example: pull data

another example: send email
```

This layer needs to **orchestrate** data as needed.

**All processing happens in service layers.**

## Repository/Database layer
Single responsibility: build and execute database queries based on the passed data.

It should stay simple and focused on one job at a time.

- Fetch
- Insert
- Delete

## End-to-end flow
```
user sends request -> controller -> service -> db

db -> service -> controller -> back to user
```

---

## Middlewares
As soon as a request reaches the server entry point, you can run functions **before** it reaches routing, **between** routing and controllers, and even **after** controllers.

```
incoming request -> |func| -> |func| -> |routing| ->
-> |func| -> |controller,service,db| -> |func| -> back to user
```

Middlewares sit between core lifecycle points. A middleware is a type of handler.

**Middlewares are optional and depend on your requirements.**

Middlewares take **three** objects:
- `req`
- `res`
- `next`

`next` passes execution to the next context (function → function or function → routing, etc.).

A middleware can read from `req` and can also send a response directly. It can **cut off** the lifecycle at any point and respond immediately.

### Why use middlewares?
So we don’t repeat the same logic in different endpoints. Put common logic in a single place and reuse it across routes. This adds “some logic” between steps of the API lifecycle.

Because your app receives thousands/millions of requests, you often need auth, logging, parsing, and other operations across many endpoints. Without middleware, you must duplicate these extra operations for every handler/endpoint.

We delegate responsibilities to a separate component (middlewares).

Common middleware examples:
- Security (CORS, security headers, auth, rate limiting)
- Logging and monitoring
- Parsing
- Compression
- Global error handling

**Order matters** in middleware chains. The sequence is controlled via `next`.

---

## Request context
A request context is some kind of **storage/state** that is **scoped** to a specific request. It can be an abstract concept or a concrete structure.

It is accessible across middleware boundaries and can be modified along the lifecycle.

Example:
```
uuid, role, METADATA -> STORED IN THE CONTEXT
```

One middleware can set values in the context, and another middleware can read them later **without close coupling**.

Example use-case: inserting a book into a database.
Instead of placing the user ID in the payload, you can pull it from the **request context**, which was populated by the **auth middleware** as the request moved through the lifecycle.

**A shared state accessible to all middlewares, limited to one request.**

Request contexts can also be used for:
- Tracing requests (which services were called, etc.)
- Sending cancel/abort signals and deadlines to downstream services so your service doesn’t hang indefinitely
