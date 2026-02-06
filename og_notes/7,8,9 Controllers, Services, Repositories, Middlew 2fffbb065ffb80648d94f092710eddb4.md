# 7,8,9. Controllers, Services, Repositories, Middlewares & Request Context

# Handlers, Services, Repositories

A **Request Lifecycle** is what we call the entire operation when a client sends a request over HTTP to a server, and the server sends a response back.

We’ve covered how it looks from the outside (like http) but we will now dive into what actually happens in the server.

1. The request reaches the server

This is called our ENTRY POINT, where the request reaches our servers PORT.

1. Our server is **listening** on a port.
2. From here we use **routes** (/users, /stuff, etc) to redirect requests to a **handler/controller**

A **handler/controller** is a predefined function that handles the request of that route.

Both terms **handler** and **controller** are used for the same thing.

```jsx
Client sends request over HTTP
-> Request received on server PORT
-> Server listening on PORT
-> ROUTES redirect request to a HANDLER/CONTROLLER

These handlers/controllers are unique to each route.
```

We use multiple controllers as a design pattern. This just keeps things simple, easy to extend and navigate rather than a single controller handling everything.

```jsx
REMEMBER: different routes do different things
```

### Controller/Handler

Receives **two** variables/objects:

- The request object
- The response object

The controller can take values from the request object, and send values through the response object. This is handled by the library you are using (req, res).

also responsible for response codes

**note: not all programming languages work well with json directly which is why we deserialize into things like struct, class, etc.**

Here we can do external operations with the data, do processing, literally a bunch of stuff. This is the typical structure:

1. Take out data from the request object
    1. Deserialization (extract data from the JSON object into your native data format) (also called **binding**)
    2. if this failed, return 400 error code - bad request
2. Validate and Transform data
    1. params, body, etc etc
    2. make sure all data is in the correct
    3. optionally transform data into specific structures for our logics
    
    ```jsx
    /books?sort=name
    /books?sort=date
    
    we transform data accordingly
    these fields can also be set defaults incase not specific like:
    /books
    
    we can do this conditionally before we even reach the DB
    ```
    
3. We call the **service layer** with our transformed data

### Service Layer

Think of this as just a function that has nothing to do with frontend/backend. All it does is process data and return it without worrying about response code, validation, what data etc etc.

It excepts everything to be already good.

This **service** can call the **repository/database layer**

```jsx
one example: pull data

another example: send email
```

This layers needs to **orchestrate** data as needed.

ALL PROCESSING HAPPENS IN SERVICE LAYERS

### Repository/Database Layer

Single responsibility of building and executing a database query based on the passed data.

There shouldn’t be extra options here, it should be simple one thing.

- Fetch, Insert, Delete

### Overall Now

```jsx
user sends request -> controller -> service -> db

db -> service -> controller -> back to user
```

# Middlewares

The moment a request reaches our ENTRY POINT for our server:

- we can have different functions

```jsx
incoming request -> |func| -> |func| -> |routing| -> 
-> |func| - > |controller,service,db| -> |func| -> back to user
```

Middlewares sit in between our core api lifecycle points. A middleware is a type of a handler.

**MIDDLEWARES ARE OPTIONAL FUNCTIONS DEPENDING ON YOUR REQUIREMENTS**

Middlewares take 3 objects:

- req, res, next
- next: passes the execution to the next context (func → func or func → routing etc.)

Middleware can take values from req, AND send responses. We can cut off the complete API lifecycle flow at ANY POINT and send a response back.

**Why use middlewares?**

So we don’t write the same set of execution lines in different places. Place it in one package and reuse it in different places. This does “some logic” in between our steps of the API lifecycle.

Your app receives thousands/millions of requests. Because of that you have auth, logging, parsing operations and have hundreds/thousands of API endpoints.

If we don’t have middleware, we have to duplicate these “extra operations” for each handler/endpoint.

We delegate responsibilites to a different component (middlewares).

Some examples of middleware:

- Security (CORS, security headers, auth - the token stuff, rate limit)
- Logging and monitoring
- Parsing
- Compression
- Global error handling

The order/sequence also matters for the middlewares. Done through the “next” object in the middleware.

### Request Context

*some kind of storage*

Can be an abstract concept, but can also be concrete.

This is some sort of “storage/state” that is **scoped** for a specific request. The context is accessed across our middleware, and boundaries.

For a great example lets recall back to our stateful/stateless http requests.

```jsx
uuid, role, METADATA -> STORED IN THE CONTEXT
```

One middleware can modify values in the context, and another middleware can access that changed value through the context **without close coupling**.

Lets say we are inserting a book into a database.

Rather than placing the userid of the user inserting the book in the payload, we can pull it from the REQUEST CONTEXT, pulled from the AUTH MIDDLEWARE as its sent down the API Lifecycle path.

**A SHARED STATE ACCESSIBLE TO ALL MIDDLEWARES, LIMITED TO 1 REQUEST**

One more thing, request contexts can also be used for:

- tracing requests (what services were called etc.)
- send cancel and abort signals and deadlines to our downstream external services so our service doesn’t hang up perpetually