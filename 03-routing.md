# Routing

Routing is the mapping of HTTP requests to server-side logic. While the HTTP method expresses the **intent** (what), routing expresses the **location** (where).

**Formula:** `HTTP Method + Route = Handler`

Example:
```
GET /users/123  →  Fetch user with ID 123
POST /users     →  Create a new user
```

## Static Routes

Static routes have no parameters:

```
GET  /api/books      – List all books
POST /api/books      – Create a new book
GET  /api/authors    – List all authors
```

These routes never clash because each combination of method and path is unique.

## Dynamic Routes with Path Parameters

Dynamic routes accept parameters as part of the path:

```
GET /api/users/123       – Fetch user with ID 123
GET /api/users/456       – Fetch user with ID 456
DELETE /api/products/:id – Delete product by ID
```

In your server code, you define placeholders for parameters:

```javascript
router.GET("/api/users/:id", getUserHandler)
router.DELETE("/api/products/:id", deleteProductHandler)
```

The `:id` is a placeholder that captures the value from the URL and passes it to the handler.

## Query Parameters

Query parameters are key-value pairs appended to the base URL using `?` and separated by `&`:

```
GET /api/search?query=javascript&limit=10&offset=0
```

**Path Params vs Query Params:**

| Aspect | Path Params | Query Params |
|--------|------------|--------------|
| **Purpose** | Identify specific resource | Filter, sort, paginate, configure |
| **Semantics** | RESTful identity | Metadata about the request |
| **Example** | `/users/123` | `/search?query=value` |
| **Use case** | Fetch user 123 | Search for "value" |

**Why not use path params for searching?**

```
GET /api/search/javascript   ← Possible but unclear semantics
GET /api/search?query=javascript  ← Clear intent
```

Path params have semantic meaning (identifying a specific resource), while query params provide additional context about the request.

## Nested Routes

Nested routes represent hierarchical relationships:

```
GET /api/users/123/posts/456
```

Breaking it down:
- `/api/users` – User collection
- `/api/users/123` – Specific user
- `/api/users/123/posts` – Posts by user 123
- `/api/users/123/posts/456` – Specific post by user 123

Each level is a separate resource, and each can return different responses. This makes the API more expressive and RESTful.

**When to use nesting:**

- The nested resource belongs to the parent (e.g., posts belong to users)
- The nesting depth stays shallow (typically 2-3 levels max)
- The relationship is logical and semantic

## Route Versioning

API versioning allows you to maintain multiple versions simultaneously:

```
GET /api/v1/products   – Version 1 of product endpoint
GET /api/v2/products   – Version 2 of product endpoint
```

**Reasons for versioning:**

- **Backward compatibility** – Old clients continue working
- **Gradual migration** – Give users time to upgrade
- **Experimentation** – Test new designs before deprecating old ones
- **Deprecation** – Sunset old versions on a schedule

**Alternative approaches:**

- Header-based versioning: `Accept: application/vnd.api+json;version=2`
- Content negotiation
- URL-based versioning (most common)
