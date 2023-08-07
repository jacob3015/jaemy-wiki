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