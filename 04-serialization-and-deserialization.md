# Serialization and Deserialization

## The Communication Problem

We have a frontend **client** (e.g., JavaScript app) and a **server** (e.g., Rust backend). The client communicates with the server through HTTP, WebSocket, gRPC, etc.

When the client makes an HTTP request:
```javascript
GET /api/users
POST /api/users
Body: { "name": "John", "age": 30 }
```

The server responds with data that the client can parse and understand.

**The challenge:** JavaScript and Rust (or any two languages) have very different data types. How can they understand each other's data?

## The Solution: Serialization Standards

To solve this problem, we use a **common standard**—a set of rules that both client and server follow to convert information from their own format into a standard one.

This process is called **serialization** (converting to the standard format) and **deserialization** (converting from the standard format back to language-specific types).

### Text-Based Serialization Formats

- **JSON** (JavaScript Object Notation)
- **YAML** (YAML Ain't Markup Language)
- **XML** (eXtensible Markup Language)

### Binary Serialization Formats

- **Protobuf** (Protocol Buffers)
- **AVRO**

> **For this course:** We focus on **JSON over HTTP**—the most common combination.

## Understanding JSON

JSON is the most widely used data format for client-server communication.

**Example JSON object:**
```json
{
  "name": "John Doe",
  "age": 30,
  "isDeveloper": true,
  "skills": ["JavaScript", "React", "Node.js"]
}
```

### JSON Rules

1. **Keys must be strings** wrapped in double quotes (`"key"`)
2. **Values can be:**
   - String: `"text"`
   - Number: `42` or `3.14`
   - Boolean: `true` or `false`
   - Array: `[1, 2, 3]`
   - Object: `{ "nested": "value" }`
   - `null`

JSON is easy to read and write, making it the most popular choice for web APIs.

## The Complete Flow

**Client → Server:**
1. Client has data in its native format (e.g., JavaScript object)
2. **Serialize** the data into JSON
3. Send JSON over HTTP
4. Server receives JSON and **deserializes** it into its native format (e.g., Rust struct)

**Server → Client:**
1. Server has data in its native format (e.g., Rust struct)
2. **Serialize** the data into JSON
3. Send JSON over HTTP
4. Client receives JSON and **deserializes** it into its native format (e.g., JavaScript object)

## Key Takeaway

Both the client and server need to:
- Understand the serialization format (JSON)
- Be able to serialize their data to JSON
- Be able to deserialize JSON back to their native types

This standard format acts as a **universal language** between different systems, enabling seamless communication regardless of the programming languages used on either end.

---

> **Note:** There's an underlying layer (OSI Model) that handles converting data all the way to binary for network transmission, but that's outside the scope of this course.
