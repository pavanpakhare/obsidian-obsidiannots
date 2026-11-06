[[java]]
# Java Servlet — Interview Tutorial

A **Servlet** is a Java class that runs on a web server and handles **HTTP requests and responses**. Servlets are the foundation behind many Java web technologies, including the architecture used by Spring MVC.

---

## 1. What is a Servlet?

A Servlet is a server-side Java component used to:

- Receive HTTP requests
    
- Process business logic
    
- Communicate with databases/services
    
- Generate HTTP responses
    
- Manage sessions/cookies
    

Typical flow:

```text
Client / Browser
       ↓
HTTP Request
       ↓
Web Server / Servlet Container
       ↓
Servlet
       ↓
Service / DAO / Database
       ↓
Servlet
       ↓
HTTP Response
       ↓
Client
```

Examples of servlet containers:

- Tomcat
    
- Jetty
    
- Undertow
    

---

# 2. Servlet Container

The **Servlet Container** manages the lifecycle of servlets.

For example, Tomcat:

```text
Tomcat
 ├── Creates Servlet
 ├── Initializes Servlet
 ├── Sends requests
 ├── Manages threads
 ├── Manages sessions
 └── Destroys Servlet
```

The container handles things like:

- Servlet creation
    
- Lifecycle
    
- Request/response objects
    
- URL mapping
    
- Multithreading
    
- Session management
    
- Security
    

### Interview question

**Q: What is a servlet container?**

**Answer:**  
A servlet container is a component of a web server that manages servlet lifecycle and provides the runtime environment for processing HTTP requests and responses.

---

# 3. Basic Servlet

Modern Servlet API uses:

```java
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

public class HelloServlet extends HttpServlet {

    @Override
    protected void doGet(
            HttpServletRequest request,
            HttpServletResponse response) {

        // process request
    }
}
```

Older applications may use:

```java
javax.servlet.*
```

Modern Jakarta applications generally use:

```java
jakarta.servlet.*
```

---

# 4. Servlet Lifecycle

This is **very important for interviews**.

```text
Servlet Class
     ↓
Class Loading
     ↓
Object Creation
     ↓
init()
     ↓
service()
     ↓
doGet()/doPost()
     ↓
destroy()
```

### Important methods

|Method|Purpose|
|---|---|
|`init()`|Initialize servlet|
|`service()`|Processes request|
|`doGet()`|Handles GET|
|`doPost()`|Handles POST|
|`destroy()`|Cleanup|

---

## 5. `init()`

Called when the servlet is initialized.

```java
@Override
public void init() {
    System.out.println("Servlet initialized");
}
```

Normally called **once** during the servlet's lifecycle.

---

# 6. `service()`

The container calls:

```java
service(request, response)
```

For `HttpServlet`, it determines the HTTP method and dispatches to methods such as:

```text
GET     → doGet()
POST    → doPost()
PUT     → doPut()
DELETE  → doDelete()
PATCH   → doPatch()
```

Conceptually:

```text
HTTP Request
     ↓
service()
     ↓
 ┌──────────────┐
 │ HTTP Method  │
 └──────────────┘
    ↓       ↓
  GET      POST
   ↓         ↓
doGet()   doPost()
```

---

# 7. `doGet()`

Used to handle GET requests.

```java
@Override
protected void doGet(
        HttpServletRequest request,
        HttpServletResponse response)
        throws IOException {

    response.getWriter().println("Hello");
}
```

Example:

```http
GET /users
```

Typical uses:

- Fetch data
    
- Display a page
    
- Search
    
- Retrieve resources
    

---

# 8. `doPost()`

Used to handle POST requests.

```java
@Override
protected void doPost(
        HttpServletRequest request,
        HttpServletResponse response)
        throws IOException {

    String username = request.getParameter("username");

    response.getWriter()
            .println("Hello " + username);
}
```

Example:

```http
POST /users
```

Usually used when submitting data.

---

# 9. Request and Response

Two extremely important objects:

```java
HttpServletRequest
HttpServletResponse
```

### Request

Contains information coming **from the client**.

```java
request.getParameter("name");
request.getHeader("Authorization");
request.getMethod();
request.getRequestURI();
```

### Response

Used to send information **back to the client**.

```java
response.setStatus(200);
response.setContentType("text/plain");

response.getWriter()
        .println("Hello");
```

---

# 10. URL Mapping

You can map a servlet to a URL.

Using annotation:

```java
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    
    @Override
    protected void doGet(
            HttpServletRequest request,
            HttpServletResponse response) {
    }
}
```

Request:

```text
GET /hello
```

will be handled by:

```text
HelloServlet
```

---

# 11. ServletConfig vs ServletContext

Very common interview question.

### ServletConfig

Configuration specific to **one servlet**.

```text
Servlet
   ↓
ServletConfig
```

### ServletContext

Shared across the **entire web application**.

```text
             ServletContext
             /     |      \
        Servlet1 Servlet2 Servlet3
```

### Easy difference

|ServletConfig|ServletContext|
|---|---|
|Per servlet|Per application|
|Servlet-specific configuration|Application-wide information|
|One servlet has its own config|All servlets can access same context|

---

# 12. Session Management

HTTP is stateless.

For example:

```text
Request 1 → Server
Request 2 → Server
```

The server doesn't inherently know that both requests came from the same user.

Servlets provide session management:

```java
HttpSession session = request.getSession();

session.setAttribute("username", "Pavan");
```

Retrieve:

```java
String username =
    (String) session.getAttribute("username");
```

---

# 13. Cookies

A cookie stores small pieces of data on the client.

Create:

```java
Cookie cookie =
    new Cookie("username", "Pavan");

response.addCookie(cookie);
```

Read:

```java
Cookie[] cookies = request.getCookies();
```

Common uses:

- Session identification
    
- Preferences
    
- Tracking information
    

---

# 14. Forward vs Redirect

Very common interview topic.

### Forward

```java
request.getRequestDispatcher("/home")
       .forward(request, response);
```

Flow:

```text
Browser
   ↓
Servlet A
   ↓
Servlet B
```

The browser doesn't make a new request.

### Redirect

```java
response.sendRedirect("/home");
```

Flow:

```text
Browser
   ↓
Servlet A
   ↓
HTTP Redirect
   ↓
Browser
   ↓
Servlet B
```

A **new request** is made.

### Interview difference

|Forward|Redirect|
|---|---|
|Server-side|Client-side|
|Same request|New request|
|Request attributes preserved|Not preserved automatically|
|Usually faster|Extra request|
|Browser URL normally unchanged|Browser URL changes|

---

# 15. Servlet Thread Safety

This is a **very important interview question**.

Generally, the container creates **one servlet instance** and multiple requests may be handled by multiple threads.

```text
             Servlet Object
             /     |      \
          Thread Thread Thread
          Req 1   Req 2   Req 3
```

Therefore, avoid mutable instance variables for request-specific data.

### Bad

```java
public class MyServlet extends HttpServlet {

    private String username;

    protected void doGet(...) {
        username = request.getParameter("username");
    }
}
```

Multiple users can access the same servlet concurrently.

### Better

```java
protected void doGet(
        HttpServletRequest request,
        HttpServletResponse response) {

    String username =
        request.getParameter("username");
}
```

Use local variables for request-specific data.

---

# 16. Servlet vs JSP

|Servlet|JSP|
|---|---|
|Java class|HTML-oriented page|
|Good for request processing|Historically used for presentation|
|Java code|HTML + JSP syntax|
|Controller-like role|View-like role|

MVC:

```text
Browser
   ↓
Servlet / Controller
   ↓
Model / Service
   ↓
Database
   ↓
Servlet
   ↓
JSP / Response
```

Spring MVC follows a similar controller-oriented architecture, although modern Spring applications often use REST APIs and frontend frameworks instead of JSP.

---

# 17. Servlet Filter

A Filter intercepts requests/responses before or after servlet processing.

```text
Request
   ↓
Filter
   ↓
Servlet
   ↓
Filter
   ↓
Response
```

Example:

```java
@WebFilter("/*")
public class LoggingFilter implements Filter {

    @Override
    public void doFilter(
            ServletRequest request,
            ServletResponse response,
            FilterChain chain)
            throws IOException, ServletException {

        System.out.println("Request received");

        chain.doFilter(request, response);
    }
}
```

Common uses:

- Authentication
    
- Logging
    
- CORS
    
- Request modification
    
- Authorization-related checks
    

This concept is especially important when learning **Spring Security's filter chain**.

---

# 18. Servlet vs Filter

|Servlet|Filter|
|---|---|
|Handles request|Intercepts request/response|
|Generates/processes response|Usually performs cross-cutting processing|
|Main endpoint|Works around endpoints|
|`service()`, `doGet()`, `doPost()`|`doFilter()`|

---

# 19. Servlet Listener

Listeners allow you to react to lifecycle events.

Examples:

```text
Application starts
Application stops
Session created
Session destroyed
Attribute changed
```

Example:

```java
@WebListener
public class MyListener
        implements ServletContextListener {

    @Override
    public void contextInitialized(
            ServletContextEvent event) {

        System.out.println("Application started");
    }
}
```

---

# 20. Important Servlet Interfaces/Classes

Remember these:

```text
Servlet
 └── GenericServlet
      └── HttpServlet
```

Important HTTP classes:

```text
HttpServlet
HttpServletRequest
HttpServletResponse
HttpSession
Cookie
```

Other important APIs:

```text
ServletConfig
ServletContext
Filter
FilterChain
ServletContextListener
```

---

# 21. Servlet Request Flow — Interview Answer

If interviewer asks:

> **Explain what happens when a request reaches a servlet.**

Answer:

```text
Client
  ↓
HTTP Request
  ↓
Web Server
  ↓
Servlet Container
  ↓
URL Mapping
  ↓
Filter(s)
  ↓
Servlet
  ↓
service()
  ↓
doGet()/doPost()
  ↓
Business Logic
  ↓
HTTP Response
  ↓
Client
```

If the servlet hasn't been initialized, the container initializes it first.

---

# 22. Most Important Interview Questions

### Beginner

1. What is a Servlet?
    
2. What is a Servlet Container?
    
3. What is Tomcat?
    
4. Explain Servlet lifecycle.
    
5. What is `init()`?
    
6. What is `service()`?
    
7. Difference between `doGet()` and `doPost()`.
    
8. What is `HttpServletRequest`?
    
9. What is `HttpServletResponse`?
    
10. How do you map a servlet to a URL?
    

### Intermediate

11. What is `ServletConfig`?
    
12. What is `ServletContext`?
    
13. Difference between ServletConfig and ServletContext.
    
14. What is session management?
    
15. What is `HttpSession`?
    
16. What are cookies?
    
17. Forward vs redirect.
    
18. What is a Servlet Filter?
    
19. What is a Servlet Listener?
    
20. How does servlet handle multiple requests?
    

### Advanced

21. Is a servlet thread-safe?
    
22. How many servlet objects are generally created?
    
23. Can multiple threads execute the same servlet?
    
24. Why shouldn't you store request-specific data in instance variables?
    
25. Explain Servlet Filter Chain.
    
26. Servlet vs JSP.
    
27. Servlet vs Spring MVC Controller.
    
28. What happens when a servlet throws an exception?
    
29. How does session tracking work?
    
30. Explain the complete HTTP request lifecycle in a servlet application.
    

---

## ⭐ 10 Things to Memorize Before an Interview

```text
1. Servlet = server-side Java component

2. Container = manages servlet lifecycle

3. Lifecycle:
   init()
      ↓
   service()
      ↓
   doGet()/doPost()
      ↓
   destroy()

4. Request = client → server

5. Response = server → client

6. GET → doGet()
   POST → doPost()

7. ServletConfig → one servlet

8. ServletContext → whole application

9. Filter → intercepts requests/responses

10. One servlet instance can handle requests
    concurrently using multiple threads
```

**Interview tip:** Since you're preparing for Spring Boot, don't study Servlets in isolation. The most useful connection is:

```text
HTTP
 ↓
Servlet Container (Tomcat)
 ↓
Servlet / Filter
 ↓
Spring DispatcherServlet
 ↓
Controller
 ↓
Service
 ↓
Repository
 ↓
Database
```

Understanding this flow makes **Spring MVC, Spring Security filters, `DispatcherServlet`, and `HttpServletRequest/Response`** much easier to explain in interviews.