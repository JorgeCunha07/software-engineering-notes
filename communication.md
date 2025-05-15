# Communication

## RPC (Remote Procedure Call) Interfaces: The Fundamentals

At its core, **Remote Procedure Call (RPC)** is a communication paradigm where a program can cause a procedure (or a subroutine/function) to execute in another address space (commonly on another computer on a shared network) without the programmer explicitly coding the details for this remote interaction. The goal is to make a call to a remote service look and feel as much as possible like a local function call.

### Key Characteristics

- **Abstraction:** It hides the underlying network communication details (like socket management, data serialization/deserialization) from the developer.
- **Client-Server Model:** Typically involves a client making a request and a server processing that request and returning a response.
- **Stubs/Skeletons (or Proxies):**
  - **Client-Side Stub:** A piece of code on the client that looks like the remote procedure.\
     When the client calls it, the stub is responsible for the marshalling (serializing) the parameters, sending them to the server, waiting for the response, and unmarshalling
    the result.
  - **Server-Side Skeleton (or Stub):** A piece of code on the server that receives the request from the client stub, unmarshalls the parameters, calls the actual procedure implementation, marshals the result, and sends it back to the client.
- **Interface Definition:** The contract between the client and server (the procedures, their parameters, and return types) is usually defoined in a separate language-neutral **Interface Definition Language (IDL)**.

### RPC vs. REST

This is a common comparison, as both are used for inter-service communication.

| Feature            | RPC (e.g., gRPC, Thrift)                                                                                                         | REST                                                                                                                     |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| Paradigm           | Action/Verb-oriented (calling functions/methods)                                                                                 | Resource-oriented (nouns, operations on resources via HTTP methods)                                                      |
| Interface Contract | Defined upfront using an IDL (e.g., Protocol Buffers, Thrift IDL). Contract is usually strict and checked at compile time.       | Often defined via OpenAPI/Swagger (can be after-the-fact documentation). Contract can be looser.                         |
| HTTP Methods       | Can use any HTTP method (often POST for gRPC over HTTP/2) or custom TCP protocols. Not limited by HTTP verb semantics.           | Relies heavily on standard HTTP methods (GET, POST, PUT, DELETE, PATCH, etc.) with specific semantics.                   |
| Data Format        | Commonly binary formats (e.g., Protocol Buffers, Avro, Thrift) for efficiency and compactness.                                   | Commonly JSON (human-readable), XML. Binary formats are less common.                                                     |
| Operations         | Any function signature defined in the IDL.                                                                                       | Limited by HTTP methods; complex operations might need creative mapping (e.g., `/users/{id}/send-reset-password-email`). |
| Transport          | Can use various transports (HTTP/2 for gRPC, TCP).                                                                               | Primarily HTTP/1.1 or HTTP/2.                                                                                            |
| Performance        | Often higher due to binary formats and efficient protocols like HTTP/2 (multiplexing, header compression).                       | Can be less performant with JSON and HTTP/1.1, but HTTP/2 improves this.                                                 |
| Streaming          | Strong support for various streaming models (unary, server-streaming, client-streaming, bi-directional) in frameworks like gRPC. | Excelent browser support (JSON is native to JavaScript).                                                                 |
| Use Cases          | Internal microservice communication, high-performance APIs, streaming applications, situations requiring strong contracts.       | Public-facing APIs, simple CRUD operations, resource-centric services, when browser compatibility is key.                |

### Key Components of an RPC Framework

Most RPC frameworks involve these components:

#### 1. Interface Definition Language (IDL)

A language-agnostic way to define the service interface: the available procedures (methods), their input parameters, and their return types, including complex data structures.

Examples:

- **Protocol Buffers (Protobuf):** Used by gRPC.
- **Apache Thrift IDL:** Used by Apache Thrift.
- **Apache Avro IDL:** Used by Apache Avro.

#### 2. Code Generation

Compilers or tools that take the IDL file as input and generate:

- **Client stubs:** Code that client applications use to invoke remote procedures.
- **Server skeletons (or service base classes):** Code that developers implement on the server-side to provide the service logic.
- Data structures (message types) in the target programming language.

#### 3. Serialization/Deserialization (Marshalling/Unmarshalling)

The process of converting the structured data (parameters, return values) into a format suitable for network transmission (e.g., binary byte stream) and vice-versa.\
The IDL usually dictates the serialization format (e.g., Protobuf encoding).

#### 4. Transport Layer

The underlying protocol used to send messages between the client and server. This could be TCP, UDP, or higher-level protocols like HTTP/2 (as used by gRPC).

#### 5. Runtime Libraries

Provide the necessary infrastructure for managing connections, handling requests and responses, managing threads, timeouts, errors, etc.

## gRPC: A Prominent Modern RPC Framework

gRPC is an open-source, high performance RPC framework developed by Google. It's widely adopted for microservice communication.

### IDL

Uses **Protocol Buffers (Protobuf)** by default to define service interfaces and message structures in `.proto` files.

- Protobuf provides efficient binary serialization, strong typing, and schema evolution capabilities.

### Transport: Uses HTTP/2 as its transport protocol

HTTP/2 benefits:

- **Multiplexing:** Multiple requests/responses can be sent over a single TCP connection concurrently.
- **Server Push:** Server can send resources to the client proactively.
- **Header Compression (HPACK):** Reduces overhead.
- **Binary Framing:** More efficient and less error-prone than text-based HTTP/1.1.

### Key features of gRPC

- **Strongly-typed Contracts:** Defined in `.proto` files.
- **Efficient Binary Serialization:** With Protobuf.
- **Multi-language Support:** Excellent code generation for many languages (Java, C++, Python, Go, Node.js, C#, Ruby, etc.).
- **Streaming:**
  - **Unary RPC:** Classic request-response.
  - **Server Streaming RPC:** Client sends a request, server sends back a stream of responses.
  - **Client Streaming RPC:** Client sends a stream of requests, server sends back a single response.
  - **Bidirectional Streaming RPC:** Both client and server send a stream of messages to each other independently.
- **Deadlines/Timeouts:** Clients can specify how long they are willing to wait for an RPC to complete.
- **Cancellation:** Allows clients or servers to cancel an ongoing operation.
- **Pluggable Authentication & Authorization:** Supports various mechanisms like SSL/TLS, token-based auth.
- **Metadata:** Allows sending key-value pairs with calls, similar to HTTP headers.

### Basic gRPC Workflow

1. **Define Service:** Write a `.proto` file defining service methods and message types.
2. **Generate Code:** Use the `protoc` (Protocol Buffer compiler) with the gRPC plugin for your target language(s) to generate client stubs, server base classes, and message classes.
3. **Implement Server:** Write server-side logic by extending the generated service base class and implementing its methods. Start a gRPC server to listen for requests.
4. **Implement Client:** Use the generated client stub in your client application to make calls to the remote server methods.

### Advantages of Modern RPC (like gRPC)

- **Performance:** Binary serialization (Protobuf) and efficient transport (HTTP/2) often lead to lower latency and higher throughput compared to JSON/HTTP/1.1 based REST APIs.
- **Strongly-Typed Contracts & Schema Evolution:** IDL ensures client and server agree on the data structures and service methods, catching mismatches at compile time. Protobuf allows for backward and forward compatible schema changes if done carefully.
- **Code Genaration:** Reduces boilerplate code for client-server communication, allowing developers to focus on business logic.
- **Advanced Streaming Capabilities:** Built-in support for various streaming models is a significant advantage for use cases involving large data transfers or real-time communication.
- **Network Efficiency:** HTTP/2 features like multiplexing and header compression reduce network overhead.
- **Language Agnostic:** Designed for interoperability between service written in different languages.

### Disadvantages/Challenges of RPC

- **Browser Friendliness:** Direct browser consumption of binary RPC protocols like gRPC is not straightforward. Solutions like **gRPC-Web** (a proxy layer) are needed to translate to HTTP/1.1 + JavaScript friendly formats.
- **Discoverability & Introspection:** Unlike REST APIs (which can often be explored with a browser or `curl`), discovering and testing RPC services can be less intuitive without specific tooling (e.g., `grpcurl`, Postman for gRPC).
- **Potential for Tighter Coupling:** While the IDL defines a contract, changes to the service interface can require regeneration and recompilation of both client and server code. Careful API versioning strategies are needed.
- **Firewall Traversal:** While gRPC over HTTP/2 typically uses standard ports (80/443) and is generally firewall-friendly, older RPC mechanisms using custom TCP ports might face issues.
- **Readability:** Binary formats are not human-readable, making debugging network messages more challenging wihtout specialized tools.

### RPC with Spring / Spring Boot

Integrating RPC frameworks like gRPC into Spring Boot applications is well-supported:

- **Libraries/Starters:** Projects like `grpc-spring-boot-starter` (by LogNet or Yidongnan) simplify the setup.
- **Server-Side:** You can define your gRPC service implementation as Spring beans (annotated with `@GrpcService`) and leverage Spring's DI, AOP, transaction management, etc.
- **Client-Side:** You can inject gRPC client stubs (channels and stubs can be managed as Spring beans using `@GrpcClient`) into your Spring services to communicate with other gRPC services.
