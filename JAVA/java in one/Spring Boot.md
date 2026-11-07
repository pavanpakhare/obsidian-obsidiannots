# **Spring Boot Tutorial: Building Modern Java Applications**  

Spring Boot is a **powerful framework** built on top of the Spring ecosystem that simplifies Java-based web development. It provides:  
✅ **Auto-configuration** (minimal setup)  
✅ **Embedded servers** (Tomcat, Jetty, Undertow)  
✅ **Production-ready features** (Actuator, Metrics)  
✅ **Dependency management** (via Spring Boot Starter POMs)  

This tutorial covers **Spring Boot fundamentals** and **advanced topics** to help you build **REST APIs, Microservices, and Full-Stack Applications**.  

---

## **Table of Contents**  
1. [**Spring Boot Basics**](#spring-boot-basics)  
   - Creating a Spring Boot Project  
   - Project Structure  
   - `@SpringBootApplication`  
   - Running the Application  

2. [**Spring Boot REST API**](#spring-boot-rest-api)  
   - `@RestController`  
   - `@GetMapping`, `@PostMapping`, etc.  
   - Request/Response Handling  
   - Exception Handling  

3. [**Spring Data JPA (Database Access)**](#spring-data-jpa-database-access)  
   - `@Entity`, `@Repository`  
   - CRUD Operations  
   - Custom Queries (`@Query`)  

4. [**Spring Boot Security**](#spring-boot-security)  
   - Basic Auth  
   - JWT Authentication  
   - OAuth2 (Social Login)  

5. [**Spring Boot Testing**](#spring-boot-testing)  
   - Unit Testing (`@SpringBootTest`)  
   - MockMvc (Testing REST APIs)  

6. [**Spring Boot Actuator (Monitoring)**](#spring-boot-actuator-monitoring)  
   - Health Checks  
   - Metrics, Loggers  

7. [**Spring Boot Deployment**](#spring-boot-deployment)  
   - Running as a JAR  
   - Dockerizing Spring Boot  
   - Deploying to Cloud (AWS, Heroku)  

8. [**Advanced Topics**](#advanced-topics)  
   - Caching (`@Cacheable`)  
   - Async Methods (`@Async`)  
   - WebSockets  
   - Microservices with Spring Cloud  

---

## **1. Spring Boot Basics**  

### **Creating a Spring Boot Project**  
1. **Using Spring Initializr ([start.spring.io](https://start.spring.io/))**  
   - Select: **Maven, Java, Spring Boot 3.x**  
   - Dependencies: **Spring Web, Spring Data JPA, H2 Database**  

2. **Manual Setup (Maven)**  
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.1.0</version>
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

### **Project Structure**  
```
src/
├── main/
│   ├── java/
│   │   └── com.example.demo/
│   │       ├── DemoApplication.java (Main Class)
│   │       ├── controller/ (REST APIs)
│   │       ├── model/ (Entities)
│   │       └── repository/ (Database Layer)
│   └── resources/
│       ├── application.properties (Config)
│       └── static/ (Frontend Files)
```

### **`@SpringBootApplication`**  
This annotation enables:  
✔ **Auto-configuration**  
✔ **Component scanning**  
✔ **Embedded server setup**  
```java
@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

### **Running the Application**  
```bash
mvn spring-boot:run
# OR
java -jar target/demo-0.0.1-SNAPSHOT.jar
```
Access: `http://localhost:8080`  

---

## **2. Spring Boot REST API**  

### **Creating a REST Controller**  
```java
@RestController
@RequestMapping("/api/books")
public class BookController {

    @GetMapping
    public List<Book> getAllBooks() {
        return List.of(
            new Book(1, "Spring Boot in Action", "Craig Walls"),
            new Book(2, "Clean Code", "Robert Martin")
        );
    }

    @GetMapping("/{id}")
    public Book getBookById(@PathVariable int id) {
        return new Book(id, "Sample Book", "Author");
    }

    @PostMapping
    public Book createBook(@RequestBody Book book) {
        return book; // In real apps, save to DB
    }
}
```

### **Request/Response Handling**  
| Annotation | Usage |  
|------------|-------|  
| `@GetMapping` | HTTP GET |  
| `@PostMapping` | HTTP POST |  
| `@PutMapping` | HTTP PUT |  
| `@DeleteMapping` | HTTP DELETE |  
| `@PathVariable` | Extract URL params |  
| `@RequestParam` | Extract query params |  
| `@RequestBody` | Parse JSON request |  

### **Exception Handling**  
```java
@ControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(BookNotFoundException.class)
    public ResponseEntity<String> handleBookNotFound(BookNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(ex.getMessage());
    }
}
```

---

## **3. Spring Data JPA (Database Access)**  

### **Configuring Database**  
`application.properties`:  
```properties
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.h2.console.enabled=true
```

### **Entity & Repository**  
```java
@Entity
public class Book {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String title;
    private String author;
    // Getters & Setters
}

@Repository
public interface BookRepository extends JpaRepository<Book, Long> {
    // Custom query
    List<Book> findByAuthor(String author);
}
```

### **Using Repository in Controller**  
```java
@RestController
@RequestMapping("/api/books")
public class BookController {
    
    @Autowired
    private BookRepository bookRepository;

    @GetMapping
    public List<Book> getAllBooks() {
        return bookRepository.findAll();
    }
}
```

---

## **4. Spring Boot Security**  

### **Basic Auth**  
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/public/**").permitAll()
                .anyRequest().authenticated()
            )
            .httpBasic(Customizer.withDefaults());
        return http.build();
    }
}
```

### **JWT Authentication**  
1. Add dependency:  
```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.9.1</version>
</dependency>
```
2. Configure JWT Filter:  
```java
@Component
public class JwtTokenFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain chain) {
        // Validate JWT token
    }
}
```

---

## **5. Spring Boot Testing**  

### **Unit Testing**  
```java
@SpringBootTest
class BookServiceTest {
    
    @Autowired
    private BookService bookService;

    @Test
    void testGetAllBooks() {
        List<Book> books = bookService.getAllBooks();
        assertFalse(books.isEmpty());
    }
}
```

### **Testing REST APIs with MockMvc**  
```java
@WebMvcTest(BookController.class)
class BookControllerTest {
    
    @Autowired
    private MockMvc mockMvc;

    @Test
    void testGetAllBooks() throws Exception {
        mockMvc.perform(get("/api/books"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$[0].title").value("Spring Boot in Action"));
    }
}
```

---

## **6. Spring Boot Actuator (Monitoring)**  

### **Enabling Actuator**  
`application.properties`:  
```properties
management.endpoints.web.exposure.include=health,metrics,info
management.endpoint.health.show-details=always
```

### **Accessing Endpoints**  
- `/actuator/health` (App status)  
- `/actuator/metrics` (Performance metrics)  
- `/actuator/info` (Custom app info)  

---

## **7. Spring Boot Deployment**  

### **Running as a JAR**  
```bash
mvn clean package
java -jar target/demo-0.0.1-SNAPSHOT.jar
```

### **Dockerizing Spring Boot**  
`Dockerfile`:  
```dockerfile
FROM openjdk:17-jdk-slim
COPY target/demo-0.0.1-SNAPSHOT.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```
Run:  
```bash
docker build -t spring-boot-app .
docker run -p 8080:8080 spring-boot-app
```

---

## **8. Advanced Topics**  

### **Caching (`@Cacheable`)**  
```java
@Service
public class BookService {
    
    @Cacheable("books")
    public Book getBookById(Long id) {
        // Expensive DB call
    }
}
```

### **Async Methods (`@Async`)**  
```java
@Service
public class EmailService {
    
    @Async
    public void sendEmail(String to) {
        // Async email sending
    }
}
```

### **Microservices with Spring Cloud**  
- **Service Discovery**: Eureka  
- **API Gateway**: Spring Cloud Gateway  
- **Distributed Config**: Spring Cloud Config  

---

## **Conclusion**  
This **Spring Boot tutorial** covered:  
✅ **REST API Development**  
✅ **Database Access (JPA)**  
✅ **Security (JWT, OAuth2)**  
✅ **Testing & Deployment**  
✅ **Advanced Features (Caching, Async, Microservices)**  