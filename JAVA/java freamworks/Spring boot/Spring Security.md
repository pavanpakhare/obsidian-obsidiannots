[[Spring Boot]]
## 1. `HttpSecurity` — most important methods

```java
@Bean
SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {

    http
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/public/**").permitAll()
            .requestMatchers("/admin/**").hasRole("ADMIN")
            .requestMatchers("/user/**").hasAnyRole("USER", "ADMIN")
            .anyRequest().authenticated()
        );

    return http.build();
}
```

### Important `HttpSecurity` methods

|Method|Purpose|
|---|---|
|`authorizeHttpRequests()`|Configure URL authorization|
|`requestMatchers()`|Select URLs|
|`permitAll()`|Allow everyone|
|`authenticated()`|Require login|
|`hasRole()`|Require a role|
|`hasAnyRole()`|Require one of multiple roles|
|`hasAuthority()`|Require authority|
|`hasAnyAuthority()`|Require one of multiple authorities|
|`denyAll()`|Deny everyone|
|`anonymous()`|Configure anonymous access|
|`csrf()`|Configure CSRF|
|`cors()`|Configure CORS|
|`sessionManagement()`|Configure sessions|
|`formLogin()`|Form-based login|
|`httpBasic()`|HTTP Basic authentication|
|`logout()`|Logout configuration|
|`exceptionHandling()`|Handle authentication/authorization errors|
|`authenticationProvider()`|Add authentication provider|
|`addFilterBefore()`|Add custom filter before another filter|
|`addFilterAfter()`|Add custom filter after another filter|
|`addFilterAt()`|Add filter at a specific position|
|`securityMatcher()`|Restrict an entire `SecurityFilterChain`|
|`headers()`|Configure security headers|
|`requiresChannel()`|Configure HTTP → HTTPS requirements|

---

# 2. `authorizeHttpRequests()`

Controls **authorization**.

```java
http.authorizeHttpRequests(auth -> auth
    .requestMatchers("/login", "/register").permitAll()
    .requestMatchers("/admin/**").hasRole("ADMIN")
    .requestMatchers("/api/**").authenticated()
    .anyRequest().denyAll()
);
```

The order matters:

```text
First matching rule → applied
```

---

# 3. `requestMatchers()`

Matches requests based on URL.

```java
.requestMatchers("/api/users").authenticated()
```

Multiple URLs:

```java
.requestMatchers(
    "/login",
    "/register",
    "/css/**"
).permitAll()
```

HTTP method-specific matching:

```java
.requestMatchers(HttpMethod.GET, "/products/**")
    .permitAll()

.requestMatchers(HttpMethod.POST, "/products/**")
    .hasRole("ADMIN")
```

Useful imports:

```java
import org.springframework.http.HttpMethod;
```

So you can have:

```text
GET /products      → public
POST /products     → ADMIN
DELETE /products   → ADMIN
```

---

# 4. `permitAll()`

No authentication required.

```java
.requestMatchers("/public/**").permitAll()
```

Example:

```text
GET /products
GET /register
POST /login
```

can be public.

---

# 5. `authenticated()`

User must be authenticated.

```java
.requestMatchers("/profile/**").authenticated()
```

---

# 6. `hasRole()`

Checks a role.

```java
.requestMatchers("/admin/**")
    .hasRole("ADMIN")
```

Important:

```java
hasRole("ADMIN")
```

internally expects:

```text
ROLE_ADMIN
```

So don't normally write:

```java
hasRole("ROLE_ADMIN")
```

---

# 7. `hasAnyRole()`

```java
.requestMatchers("/dashboard/**")
    .hasAnyRole("USER", "ADMIN")
```

User needs either:

```text
ROLE_USER
```

or:

```text
ROLE_ADMIN
```

---

# 8. `hasAuthority()`

Checks an exact authority.

```java
.requestMatchers("/reports/**")
    .hasAuthority("REPORT_READ")
```

Unlike roles, authorities don't automatically get the `ROLE_` prefix.

---

# 9. `hasAnyAuthority()`

```java
.requestMatchers("/reports/**")
    .hasAnyAuthority(
        "REPORT_READ",
        "REPORT_ADMIN"
    )
```

---

# 10. `anyRequest()`

Matches everything not previously matched.

```java
.anyRequest().authenticated()
```

A common pattern:

```java
http.authorizeHttpRequests(auth -> auth
    .requestMatchers("/public/**").permitAll()
    .requestMatchers("/admin/**").hasRole("ADMIN")
    .anyRequest().authenticated()
);
```

---

# 11. `csrf()`

CSRF protection.

Modern Spring Security:

```java
http.csrf(csrf -> csrf.disable());
```

Common for stateless REST APIs using JWT:

```java
http
    .csrf(csrf -> csrf.disable())
    .sessionManagement(session ->
        session.sessionCreationPolicy(
            SessionCreationPolicy.STATELESS
        )
    );
```

Don't blindly disable CSRF for every application. It depends on how authentication and browser requests are handled.

---

# 12. `cors()`

Configure Cross-Origin Resource Sharing.

```java
http.cors(cors -> {});
```

Usually combined with a `CorsConfigurationSource`.

```java
@Bean
CorsConfigurationSource corsConfigurationSource() {

    CorsConfiguration configuration =
        new CorsConfiguration();

    configuration.setAllowedOrigins(
        List.of("http://localhost:3000")
    );

    configuration.setAllowedMethods(
        List.of("GET", "POST", "PUT", "DELETE")
    );

    configuration.setAllowedHeaders(
        List.of("*")
    );

    UrlBasedCorsConfigurationSource source =
        new UrlBasedCorsConfigurationSource();

    source.registerCorsConfiguration(
        "/**",
        configuration
    );

    return source;
}
```

---

# 13. `sessionManagement()`

Controls sessions.

```java
http.sessionManagement(session ->
    session.sessionCreationPolicy(
        SessionCreationPolicy.STATELESS
    )
);
```

Important policies:

```java
SessionCreationPolicy.ALWAYS
SessionCreationPolicy.IF_REQUIRED
SessionCreationPolicy.NEVER
SessionCreationPolicy.STATELESS
```

For JWT APIs, commonly:

```text
STATELESS
```

Meaning:

```text
Request
   ↓
JWT
   ↓
Authentication
   ↓
Response

No server-side login session
```

---

# 14. `formLogin()`

Traditional browser login.

```java
http.formLogin(form -> form
    .loginPage("/login")
    .defaultSuccessUrl("/home")
    .permitAll()
);
```

Important methods:

```java
.loginPage()
.loginProcessingUrl()
.defaultSuccessUrl()
.failureUrl()
.successHandler()
.failureHandler()
.permitAll()
```

---

# 15. `httpBasic()`

HTTP Basic authentication.

```java
http.httpBasic(Customizer.withDefaults());
```

Client sends:

```http
Authorization: Basic username:password
```

Actually encoded as Base64.

Example:

```http
Authorization: Basic dXNlcjpwYXNz
```

HTTPS should be used because Basic credentials otherwise aren't confidential in transit.

---

# 16. `logout()`

Configure logout.

```java
http.logout(logout -> logout
    .logoutUrl("/logout")
    .logoutSuccessUrl("/login")
);
```

Important methods:

```java
.logoutUrl()
.logoutSuccessUrl()
.logoutSuccessHandler()
.invalidateHttpSession()
.deleteCookies()
```

Example:

```java
http.logout(logout -> logout
    .logoutUrl("/logout")
    .invalidateHttpSession(true)
    .deleteCookies("JSESSIONID")
);
```

---

# 17. `exceptionHandling()`

Handles security exceptions.

```java
http.exceptionHandling(exception -> exception
    .authenticationEntryPoint(
        (request, response, authException) ->
            response.sendError(401)
    )
);
```

Two important concepts:

### Authentication failure

User isn't logged in:

```text
401 Unauthorized
```

### Authorization failure

User is logged in but doesn't have permission:

```text
403 Forbidden
```

---

# 18. `accessDeniedHandler()`

Handles `403`.

```java
http.exceptionHandling(exception -> exception
    .accessDeniedHandler(
        (request, response, exception) ->
            response.sendError(403)
    )
);
```

---

# 19. `authenticationEntryPoint()`

Handles unauthenticated requests.

```java
.authenticationEntryPoint(
    (request, response, exception) ->
        response.sendError(401)
)
```

---

# 20. Custom filters

Very important for JWT.

### `addFilterBefore()`

```java
http.addFilterBefore(
    jwtAuthenticationFilter,
    UsernamePasswordAuthenticationFilter.class
);
```

Typical JWT architecture:

```text
HTTP Request
     ↓
JWT Filter
     ↓
Validate JWT
     ↓
SecurityContext
     ↓
Authorization
     ↓
Controller
```

---

### `addFilterAfter()`

```java
http.addFilterAfter(
    myFilter,
    SomeFilter.class
);
```

---

### `addFilterAt()`

```java
http.addFilterAt(
    myFilter,
    SomeFilter.class
);
```

---

# 21. `securityMatcher()`

Useful when you have **multiple security filter chains**.

```java
@Bean
SecurityFilterChain apiSecurity(HttpSecurity http)
        throws Exception {

    http
        .securityMatcher("/api/**")
        .authorizeHttpRequests(auth -> auth
            .anyRequest().authenticated()
        );

    return http.build();
}
```

This chain applies only to:

```text
/api/**
```

---

# 22. `headers()`

Configure HTTP security headers.

```java
http.headers(headers -> headers
    .frameOptions(frame -> frame.deny())
);
```

Other concepts include:

```text
Frame options
Content Security Policy
HSTS
Cache control
Content type options
```

---

# 23. `requiresChannel()` — HTTP → HTTPS

If your question specifically means **HTTPS**, this method is important.

```java
http.requiresChannel(channel ->
    channel.anyRequest().requiresSecure()
);
```

Meaning:

```text
HTTP
 ↓
HTTPS
```

Requests must use HTTPS.

---

# 24. `redirectToHttps()`

Depending on the Spring Security version/configuration, HTTPS enforcement can be configured through the channel-security DSL and supporting web-server/proxy configuration.

For production deployments, HTTPS is commonly terminated at:

```text
Internet
   ↓
HTTPS
   ↓
Nginx / Load Balancer
   ↓
Spring Boot
```

When using a reverse proxy, forwarded-header configuration also matters so Spring knows the original request was HTTPS.

---

# 25. `AuthenticationManager`

Responsible for authentication.

Conceptually:

```text
Username + Password
       ↓
AuthenticationManager
       ↓
AuthenticationProvider
       ↓
UserDetailsService
       ↓
PasswordEncoder
       ↓
Authenticated User
```

Common method:

```java
authenticationManager.authenticate(
    new UsernamePasswordAuthenticationToken(
        username,
        password
    )
);
```

---

# 26. `AuthenticationProvider`

Performs the actual authentication logic.

```java
Authentication authenticate(
    Authentication authentication
)
```

And:

```java
boolean supports(
    Class<?> authentication
)
```

Common implementation:

```java
DaoAuthenticationProvider
```

---

# 27. `UserDetailsService`

Loads the user.

```java
UserDetails loadUserByUsername(
    String username
);
```

Example:

```java
@Bean
UserDetailsService userDetailsService() {

    return username -> userRepository
        .findByUsername(username)
        .orElseThrow();
}
```

---

# 28. `PasswordEncoder`

Important methods:

```java
encode()
matches()
```

Example:

```java
PasswordEncoder encoder =
    new BCryptPasswordEncoder();

String hash = encoder.encode("password");

boolean result =
    encoder.matches("password", hash);
```

Never store plain-text passwords.

---

# 29. `SecurityContext`

Contains the currently authenticated user.

Get authentication:

```java
Authentication authentication =
    SecurityContextHolder
        .getContext()
        .getAuthentication();
```

Then:

```java
authentication.getName();
authentication.getAuthorities();
authentication.isAuthenticated();
authentication.getPrincipal();
```

---

# 30. Method-level security

Enable:

```java
@EnableMethodSecurity
```

Then:

### `@PreAuthorize`

```java
@PreAuthorize("hasRole('ADMIN')")
public void deleteUser() {
}
```

### `@PostAuthorize`

```java
@PostAuthorize(
    "returnObject.username == authentication.name"
)
public User getUser() {
}
```

### `@Secured`

```java
@Secured("ROLE_ADMIN")
public void deleteUser() {
}
```

### `@RolesAllowed`

```java
@RolesAllowed("ADMIN")
public void deleteUser() {
}
```

---

# 31. Most important interview flow

Memorize this:

```text
HTTP Request
     ↓
SecurityFilterChain
     ↓
Security Filters
     ↓
Authentication
     ↓
SecurityContext
     ↓
Authorization
     ↓
Controller
     ↓
Service
     ↓
Repository
```

For JWT:

```text
Request
   ↓
Authorization: Bearer <JWT>
   ↓
JWT Filter
   ↓
Validate JWT
   ↓
Authentication
   ↓
SecurityContextHolder
   ↓
authorizeHttpRequests()
   ↓
Controller
```

## ⭐ Methods to memorize for interviews

```java
http.authorizeHttpRequests()

requestMatchers()

permitAll()
authenticated()

hasRole()
hasAnyRole()

hasAuthority()
hasAnyAuthority()

anyRequest()

csrf()
cors()

sessionManagement()

formLogin()
httpBasic()

logout()

exceptionHandling()

authenticationEntryPoint()
accessDeniedHandler()

addFilterBefore()
addFilterAfter()
addFilterAt()

securityMatcher()

headers()

requiresChannel()
```

And the authentication-side methods/classes:

```text
AuthenticationManager
AuthenticationProvider
UserDetailsService
PasswordEncoder
SecurityContext
SecurityContextHolder
Authentication
```

These are the core Spring Security APIs you'll encounter repeatedly in **JWT, session authentication, REST APIs, and microservices**.