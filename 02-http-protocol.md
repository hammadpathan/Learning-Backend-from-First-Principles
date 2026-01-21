# HTTP Protocol

> Note: The original lesson notes for this topic were incomplete in the source material. This file will be expanded with comprehensive HTTP protocol concepts.

HTTP (HyperText Transfer Protocol) is the foundation of communication between clients and servers on the web. It is a stateless, request-response protocol that defines how messages are formatted and transmitted.

## HTTP Methods

HTTP methods express the **intent** of an operation:

- **GET** – Retrieve data (safe, idempotent)
- **POST** – Create new data
- **PUT** – Replace entire resource
- **PATCH** – Partially update resource
- **DELETE** – Remove resource
- **HEAD** – Like GET, but without response body
- **OPTIONS** – Describe communication options

## Request Structure

An HTTP request consists of:

1. **Request line** – Method, URL, HTTP version (e.g., `GET /api/users HTTP/1.1`)
2. **Headers** – Metadata about the request (e.g., `Content-Type`, `Authorization`)
3. **Body** (optional) – Data payload (typically for POST, PUT, PATCH)

## Response Structure

An HTTP response consists of:

1. **Status line** – HTTP version and status code (e.g., `HTTP/1.1 200 OK`)
2. **Headers** – Metadata about the response
3. **Body** – Response payload (usually JSON, HTML, etc.)

## Status Codes

Responses are categorized by status code:

- **1xx** – Informational (request received, processing continues)
- **2xx** – Success (request succeeded)
  - `200 OK` – Request successful
  - `201 Created` – Resource created
  - `204 No Content` – Success with no response body
- **3xx** – Redirection (further action needed)
  - `301 Moved Permanently`
  - `304 Not Modified` – Resource hasn't changed
- **4xx** – Client errors (client sent bad request)
  - `400 Bad Request`
  - `401 Unauthorized`
  - `403 Forbidden`
  - `404 Not Found`
- **5xx** – Server errors (server failed to handle valid request)
  - `500 Internal Server Error`
  - `503 Service Unavailable`

## Stateless Protocol

HTTP is **stateless**: each request is independent. The server doesn't inherently remember previous requests. Solutions for maintaining state:

- **Cookies** – Small data stored on the client
- **Sessions** – Server-side storage with a session ID
- **Tokens** – JWT or similar (common in modern APIs)

## Versioning

- **HTTP/1.1** – The long-standing standard, uses persistent connections
- **HTTP/2** – Multiplexing, server push, better compression
- **HTTP/3** – Built on QUIC, faster and more reliable
