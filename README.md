# jaemy-wiki
Repository for personal wiki

# spring
## Handling HTTP Requests
Servlet Container to Before the Execution of Controller
DispatcherServlet -> HandlerMapping -> ArgumentResolver(RequestBodyMethodArgumentResolver)
### DispatcherServlet
When a request is received by the servlet container, it's the responsibility of the DispatcherServlet to handle the request, determine which controller method to invoke, and map the request data to method parameters.
### HandlerMapping
The HandlerMapping component is responsible for mapping incoming requests to specific controller methods. It determines which controller and method should handle the request based on the URL, HTTP method, and other factors.
### ArgumentResolver
Argument resolvers are responsible for converting request data (such as request parameters, headers, and request body) into method parameters. For the @RequestBody annotation, a specific argument resolver called RequestBodyMethodArgumentResolver is responsible for reading the request body and converting it into a Java object.

# Pending wiki
Yes, you're correct in your description of how HTTP requests are handled in a servlet container and then passed to Spring Web MVC.

Here's the typical sequence of events when an HTTP request is made to a servlet-based web application like Spring MVC:

1. **Request Reception by Servlet Container**:
   When an HTTP request arrives at the servlet container (e.g., Tomcat, Jetty), the container's thread (often known as an HTTP connector thread) is responsible for receiving and parsing the request.

2. **Thread Handover to Application**:
   After the request is parsed, the servlet container passes the request to a thread from its thread pool. This thread will handle the actual processing of the request. The container's thread pool is often based on the NIO (Non-blocking I/O) model, and it efficiently manages the handling of many concurrent requests with fewer threads.

3. **Spring's DispatcherServlet**:
   Within a Spring MVC application, the main entry point for handling requests is the `DispatcherServlet`. It's configured in your `web.xml` or through Spring Boot's configuration.

4. **DispatcherServlet Handling**:
   When a request reaches the `DispatcherServlet`, it determines the appropriate controller method to handle the request based on URL mapping, HTTP method, etc.

5. **Controller Method Execution**:
   The controller method is invoked to handle the request. This method may have parameters annotated with `@RequestBody` or other annotations that describe how to extract data from the request.

6. **Argument Resolvers and Request Processing**:
   Argument resolvers, including the `RequestResponseBodyMethodProcessor`, work to extract data from the request and convert it into method parameters. This includes handling `@RequestBody` annotated parameters.

7. **Business Logic and Response Preparation**:
   Your controller method's business logic is executed, which may involve accessing databases, services, or other resources. The method prepares the response data.

8. **Response Rendering**:
   The response data is rendered into the appropriate response format (HTML, JSON, etc.).

9. **Thread Handover to Container**:
   Once the request is processed and the response is prepared, the `DispatcherServlet` hands back control to the servlet container by returning from the request processing method.

10. **Response Transmission by Servlet Container**:
    The servlet container takes the generated response and sends it back to the client over the network.

Throughout this process, the thread handling the request in the servlet container may change several times. The NIO-based thread pool efficiently manages these threads, allowing them to handle multiple requests concurrently without blocking on I/O operations.

This architecture allows servlet-based applications to handle a large number of concurrent connections efficiently, especially when using NIO-based thread pools. It's worth noting that while this explanation gives a high-level overview, the actual details can vary based on your servlet container configuration and the specifics of your Spring application.

Yes, your understanding is generally correct. Here's a bit more detail to clarify:

1. **Servlet Container and Thread Pools**:
   Servlet containers, like Tomcat, Jetty, and Undertow, use thread pools to efficiently manage incoming requests. These thread pools are often based on the NIO (Non-blocking I/O) model, which allows a smaller number of threads to handle a larger number of connections by avoiding blocking I/O operations.

2. **Connector Threads**:
   When a request arrives, the servlet container's connector thread is responsible for accepting and parsing the request. These connector threads handle the initial I/O operations and parsing.

3. **Thread Handover**:
   After the request is parsed, the connector thread hands over the further processing of the request to a thread from the container's thread pool. This handover ensures that the connector threads are quickly freed up to handle more incoming connections, and the actual processing of the request is delegated to a thread that is part of the container's thread pool.

4. **Application Threads**:
   The threads from the container's thread pool are often referred to as "application threads" or "worker threads." These threads are responsible for executing the application code, including handling servlets, filters, and forwarding requests to the Spring MVC framework.

5. **Spring MVC and Application Logic**:
   Within the application threads, Spring's DispatcherServlet and related components handle the processing of the request. This includes mapping the request to controller methods, executing business logic, and preparing the response.

6. **Concurrency and Non-Blocking**:
   The NIO-based thread pool model allows the servlet container to handle a large number of concurrent connections without requiring a one-to-one mapping between connections and threads. This is achieved by using asynchronous I/O operations and non-blocking techniques, which minimize thread contention and improve scalability.

Your understanding captures the main concepts of how servlet containers efficiently handle multiple incoming requests concurrently by utilizing thread pools and non-blocking I/O. This architecture is designed to maximize resource utilization and improve the application's ability to handle a high number of concurrent users.

Certainly! Let's delve deeper into connector threads and application threads in the context of a servlet container.

**Connector Threads**:

Connector threads are responsible for accepting incoming client requests, reading the data from the network, and parsing the HTTP request. These threads are part of the servlet container's core functionality and manage the low-level I/O operations. When a client sends an HTTP request, it arrives at the servlet container through a network socket.

Key points about connector threads:

- **Accepting Connections**: Connector threads listen on specific network ports (e.g., HTTP port 80, HTTPS port 443) and wait for incoming connections. When a connection request arrives, the connector thread handles the initial handshake.

- **Parsing Requests**: Once the connection is established, the connector thread reads the incoming data from the network and parses the HTTP request. It extracts information like HTTP method, headers, and URL.

- **Handover to Application Threads**: After parsing the request headers, the connector thread decides which application thread from the container's thread pool will handle the rest of the request. This thread handover ensures that the connector thread can quickly return to listening for new connections.

- **Non-Blocking**: Connector threads are typically designed to be non-blocking. They spend minimal time processing each connection and hand over the actual request processing to other threads in the pool. This allows the connector thread to handle many connections concurrently without being blocked by slow or long-running requests.

**Application Threads**:

Application threads, also known as worker threads, are part of the servlet container's thread pool and are responsible for executing application code, including servlets, filters, and application logic. These threads handle the more complex processing of the request, such as determining which controller method to invoke, executing business logic, and generating the response.

Key points about application threads:

- **Executing Servlets and Filters**: Application threads execute the servlets and filters defined in your application. They handle the higher-level processing of requests, such as invoking the appropriate controller method based on the URL mapping and HTTP method.

- **Spring MVC Processing**: Within an application thread, the DispatcherServlet of Spring MVC takes over and manages the processing of the request. It identifies the controller method to execute, resolves method arguments, invokes the business logic, and prepares the response.

- **Concurrency and Scalability**: The servlet container's thread pool manages a limited number of application threads. These threads are designed to handle the complex processing, allowing the connector threads to quickly return to accepting new connections. The thread pool's concurrency settings impact the number of concurrent requests the application can handle.

- **Non-Blocking I/O**: In modern applications, application threads might also use non-blocking I/O techniques, such as asynchronous servlets, to further improve scalability and responsiveness.

In summary, connector threads are responsible for handling the initial connection and parsing of HTTP requests, while application threads execute higher-level processing and application logic. The division of labor between these two types of threads allows servlet containers to efficiently manage and scale the processing of multiple concurrent client connections.

I apologize for the inconvenience. Unfortunately, I'm unable to display images directly in this text-based interface. However, I can provide a textual representation of the visualization for better understanding:

**Connector Threads**:

- Incoming HTTP requests arrive at the servlet container.
- Connector threads are responsible for accepting connections and parsing initial request headers.
- Connector threads quickly hand over the request to application threads.
- Connector threads return to listening for new connections.

**Application Threads**:

- Application threads are part of the servlet container's thread pool.
- Application threads handle more complex processing of the request.
- They execute servlets, filters, and application logic.
- They generate responses and perform business logic.
- Application threads return to the thread pool after processing.

**Overall Flow**:

1. Client sends an HTTP request.
2. Connector threads accept and parse the request.
3. Connector thread hands over to an available application thread.
4. Application thread processes the request.
5. Application thread generates and sends the response.
6. Application thread returns to the pool.
7. Connector thread listens for new connections.

This division of labor enables efficient handling of multiple incoming connections by utilizing a smaller number of threads to handle the initial I/O and delegation to application threads for higher-level processing.