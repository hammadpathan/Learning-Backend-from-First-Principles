# 3. Routing

HTTP requests method expresses the “what”

For routing, you are describing “where”

**ex:**

GET (intent) → /users/ (where/resource/route)

the combination of the action and route, is mapped to a handler and returns the data

**overall:** routing is the mapping of URL params to a server side logic

### Static Routes

```jsx
GET - /api/books (pulls data)

POST - /api/books (pushes data)
```

these are two different routes which will never clash

These two can be called “static” routes because there are no parameters in the URL / route.

### Dynamic Routes with Path Params

A “dynamic” route would look something like this:

```jsx
GET - /api/users/123
```

This route still pulls data, but it takes in a path param to get the dat for a specific user “123”.

In the server this would look something like:

```jsx
R.GET(”/api/users/:id”)
```

This defines the param “id” for the route.

### Query Params

A **query** param looks a bit different in the route:

```jsx
GET - /api/search?query=some+value
```

We follow the base route up till /seach. But we start the query with the “?”.

Think if we want to send values in a GET request. There is no body so we can’t add the data in there.

***Why not just use path params?***

Path params serve a different purpose. They serve as a semantic expression. Such as fetch details of user with id “123”.

Doing a search with this format would look like:

```jsx
GET - /api/search/some+value
```

It’s *possible* **BUT** maintaining this would be hard and defeats the purpose of REST APIS.

With query params we can send a set of KEY:VALUE pairs to the server to send some kind of metadeta about the request.

### Nested Routes

Nested routes look like this:

```jsx
GET - /api/users/123/posts/456

You can build the route like this:
- /api/users
-> /api/users/123
-> /api/users/123/posts
-> /api/users/123/posts/456

These all return different responses.
```

We are saying we want to GET on a user with id of “123”

- fetch information / data of a specific user

Then we further pull the specific post “456”

### Route versioning and deprecation

```jsx
GET - /api/v1/products
GET - /api/v2/products
```

A common practice is route versioning.

These two routes return different responses.

It can be useful when you upgrade your software/format with new requirements

You can either change the entire route or simply add another versioning.

**Eventually you can deprecate v1 and shift completely to v2.**

This is a stable and complete workflow which allows you to add breaking changes, giving the client the opportunity to migrate.

### Catch-all Route

This route catches a route that doesn’t exist:

```jsx
GET - /api/v3/products

In the server at the bottom it would catch:
/api/*
and sent a user friendly message that it doesn't exists
```