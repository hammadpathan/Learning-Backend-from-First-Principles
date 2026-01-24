# 4. Serialization and Deserialization

We’ve setup our understanding as a frontend “client” and we have a server.

The client communicates with the server through HTTP, or web socket, GRPC, etc.

Lets assume our client is a javascript app. it has to make an http request to the server

something like:

```jsx
GET - url/path
POST - url/path

along with the   { body }
```

The server responds in a similar manner that the client can parse and make sense of

The point of this lesson is to understand how the client and the server can understand the data being sent and received.

Like if the client is in JS, the backend (server) is in RUST. The client sends a request to the server. How can the server make sense of the data in its own data types. Similar vice versa.

JS and Rust have very different data types.

### OSI Models (out of scope for course)

We have different layers that work together to send and receive data.

*This is the underlying layer that converts things to be sent over the network (all the way to binary)*

On top we have the:

**Application Layer**

at the bottom we have the:

**Physical layer**

To solve the problem: you come up with a common standard. A set of rules that the client and server converts the information from their own language/format into a standard one.

This is called a **Serialization Standard.**

- JSON
- YAML
- XML

These 3 are called **text-based serialization**

We also have **binary format serialization**:

- Protobuf
- AVRO

We will be focusing on JSON with HTTP.

### JSON

This is an example of what JSON object looks like

```jsx
{
	"name": "John Doe",
	"age": 30,
	"isDeveloper": true,
	"skills": ["JavaScript", "React", "Node.js"]
}
```

Its used widely and is very easy to read.

- Keys must be inside “ “ double quotes.
- Keys must be strings.
- The values however, can be a string, number, array, another object, etc.

This data format is most commonly used.

Both the client and the server needs to understand JSON.

**Overall,** all you need to understand is that there is a layer that translates client/server specific language into a standard format which in our (and most common) cases would be JSON over HTTP.

The whole flow is called **serialization** and **deserialization.**