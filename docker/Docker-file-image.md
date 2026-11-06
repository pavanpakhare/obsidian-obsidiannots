[[Docker]]
Absolutely. Let's focus specifically on **Dockerfile → Docker image generation → running the image**, with a Spring Boot example.

## 1. What is a Dockerfile?

A `Dockerfile` is a text file containing instructions for building a Docker image.

```text
Dockerfile
    │
    │ docker build
    ▼
Docker Image
    │
    │ docker run
    ▼
Container
```

For example:

```text
Dockerfile → my-spring-app:1.0 → running container
```

---

# 2. Create a simple Dockerfile

Create a directory:

```bash
mkdir docker-demo
cd docker-demo
```

Create:

```bash
touch Dockerfile
```

Put this inside:

```dockerfile
FROM ubuntu:24.04

RUN apt update && apt install -y curl

CMD ["curl", "https://example.com"]
```

---

# 3. Understanding each instruction

### `FROM`

```dockerfile
FROM ubuntu:24.04
```

Defines the **base image**.

Think:

```text
Your image
   │
   └── Ubuntu base
```

Every normal Dockerfile starts with `FROM`.

Examples:

```dockerfile
FROM ubuntu:24.04
FROM eclipse-temurin:21
FROM node:22
FROM python:3.13
FROM nginx
```

---

### `RUN`

```dockerfile
RUN apt update && apt install -y curl
```

Executes a command **while building the image**.

Important distinction:

```text
docker build
      │
      └── RUN happens here
```

versus:

```text
docker run
      │
      └── CMD/ENTRYPOINT normally runs here
```

---

### `CMD`

```dockerfile
CMD ["curl", "https://example.com"]
```

Defines the default command when a container starts.

---

# 4. Generate the Docker image

From the directory containing `Dockerfile`:

```bash
docker build -t docker-demo .
```

Breakdown:

```text
docker build
     │
     ├── -t docker-demo
     │       └── image name
     │
     └── .
         └── build context
```

Check the image:

```bash
docker images
```

You should see something similar to:

```text
REPOSITORY    TAG       IMAGE ID
docker-demo   latest    abc123...
```

---

# 5. Run the generated image

```bash
docker run docker-demo
```

Docker creates a container from your image:

```text
docker-demo image
       │
       ▼
   container
       │
       ▼
      curl
```

---

# 6. Image tags

Instead of:

```bash
docker build -t docker-demo .
```

you can specify a version:

```bash
docker build -t docker-demo:1.0 .
```

Then:

```bash
docker images
```

You can have:

```text
docker-demo:1.0
docker-demo:2.0
docker-demo:latest
```

Run a particular version:

```bash
docker run docker-demo:1.0
```

---

# 7. Dockerfile for a simple Java application

Suppose you have:

```text
my-app/
├── Dockerfile
└── app.jar
```

Dockerfile:

```dockerfile
FROM eclipse-temurin:21-jre

WORKDIR /app

COPY app.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

Build:

```bash
docker build -t my-java-app:1.0 .
```

Run:

```bash
docker run -p 8080:8080 my-java-app:1.0
```

Architecture:

```text
             Docker build
                  │
                  ▼
        ┌──────────────────┐
        │ my-java-app:1.0  │
        │                  │
        │ Java Runtime     │
        │ app.jar          │
        └──────────────────┘
                  │
             docker run
                  │
                  ▼
             Container
                  │
                  ▼
             Java process
                  │
                  ▼
              :8080
```

---

# 8. Spring Boot Dockerfile

Suppose Maven produces:

```text
target/
└── myapp-0.0.1-SNAPSHOT.jar
```

Dockerfile:

```dockerfile
FROM eclipse-temurin:21-jre

WORKDIR /app

COPY target/myapp-0.0.1-SNAPSHOT.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

Build:

```bash
mvn clean package
```

Then:

```bash
docker build -t my-spring-app:1.0 .
```

Run:

```bash
docker run -p 8080:8080 my-spring-app:1.0
```

Now:

```text
Browser
   │
   │ localhost:8080
   ▼
Docker host :8080
   │
   ▼
Container :8080
   │
   ▼
Spring Boot
```

---

# 9. Better Spring Boot Dockerfile — Multi-stage Build

You don't actually need to build the JAR manually.

You can let Docker build the application.

```dockerfile
FROM maven:3.9-eclipse-temurin-21 AS builder

WORKDIR /app

COPY pom.xml .
COPY src ./src

RUN mvn clean package -DskipTests


FROM eclipse-temurin:21-jre

WORKDIR /app

COPY --from=builder /app/target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

Now:

```bash
docker build -t my-spring-app:1.0 .
```

Docker performs:

```text
Stage 1
Maven + JDK
     │
     ├── compile
     ├── test/build
     └── JAR
          │
          ▼
Stage 2
JRE
     │
     └── app.jar
```

The final image doesn't need Maven or the full JDK.

This is called a **multi-stage build**.

---

# 10. `.dockerignore`

You should usually create:

```text
.dockerignore
```

Example:

```text
.git
.gitignore
.idea
.vscode
target
node_modules
README.md
.env
```

This prevents unnecessary files from being sent to Docker as build context.

For example, without it:

```text
project
├── .git       ❌ unnecessary
├── node_modules ❌ huge
├── target     ❌ possibly unnecessary
├── src        ✅
├── pom.xml    ✅
└── Dockerfile ✅
```

With `.dockerignore`:

```text
Docker build context
├── src
├── pom.xml
└── Dockerfile
```

---

# 11. `COPY` vs `ADD`

Usually prefer:

```dockerfile
COPY app.jar app.jar
```

over:

```dockerfile
ADD app.jar app.jar
```

`COPY` simply copies files.

`ADD` has additional behavior, so `COPY` is generally clearer when you just need to copy files.

---

# 12. `CMD` vs `ENTRYPOINT`

This is an important interview question.

### CMD

```dockerfile
CMD ["java", "-jar", "app.jar"]
```

Provides a default command.

You can override it:

```bash
docker run myapp echo hello
```

### ENTRYPOINT

```dockerfile
ENTRYPOINT ["java", "-jar", "app.jar"]
```

Defines the main executable.

For a Spring Boot application, this is commonly used:

```dockerfile
ENTRYPOINT ["java", "-jar", "app.jar"]
```

You can also combine them:

```dockerfile
ENTRYPOINT ["java", "-jar", "app.jar"]

CMD ["--server.port=8080"]
```

---

# 13. `ARG` vs `ENV`

### ARG

Available mainly during image building:

```dockerfile
ARG APP_VERSION=1.0

RUN echo "Building $APP_VERSION"
```

Build:

```bash
docker build \
  --build-arg APP_VERSION=2.0 \
  -t myapp .
```

### ENV

Available in the image/container environment:

```dockerfile
ENV APP_ENV=production
```

Run:

```bash
docker run myapp
```

Inside:

```bash
echo $APP_ENV
```

---

# 14. Don't put secrets in Dockerfile

Avoid:

```dockerfile
ENV DB_PASSWORD=secret123
```

because the value can become part of the image configuration/history.

Instead:

```bash
docker run \
  -e DB_PASSWORD=secret123 \
  myapp
```

Or use Compose/secrets for more serious deployments.

---

# 15. Docker image layers

Consider:

```dockerfile
FROM ubuntu

RUN apt update

RUN apt install -y curl

COPY app /app
```

Docker creates reusable layers roughly like:

```text
┌────────────────────┐
│ COPY app /app      │
├────────────────────┤
│ apt install curl   │
├────────────────────┤
│ apt update         │
├────────────────────┤
│ ubuntu             │
└────────────────────┘
```

This is why Docker can build subsequent images faster.

---

# 16. View image history

Very useful for understanding image generation:

```bash
docker history my-spring-app:1.0
```

Inspect the image:

```bash
docker image inspect my-spring-app:1.0
```

You can see configuration, layers, environment variables, etc.

---

# 17. Build with no cache

Sometimes Docker reuses an old layer.

Force a fresh build:

```bash
docker build --no-cache -t myapp:1.0 .
```

---

# 18. A practical Spring Boot project

Your project could be:

```text
ecommerce/
├── src/
├── pom.xml
├── Dockerfile
├── .dockerignore
└── compose.yaml
```

### Dockerfile

```dockerfile
FROM maven:3.9-eclipse-temurin-21 AS builder

WORKDIR /app

COPY pom.xml .
RUN mvn dependency:go-offline

COPY src ./src

RUN mvn clean package -DskipTests


FROM eclipse-temurin:21-jre

WORKDIR /app

COPY --from=builder /app/target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

Build:

```bash
docker build -t ecommerce:1.0 .
```

Run:

```bash
docker run \
  --name ecommerce \
  -p 8080:8080 \
  ecommerce:1.0
```

Check:

```bash
docker ps
```

Logs:

```bash
docker logs -f ecommerce
```

Stop:

```bash
docker stop ecommerce
```

Remove:

```bash
docker rm ecommerce
```

---

## The complete mental model

Remember this:

```text
                 Dockerfile
                     │
                docker build
                     │
                     ▼
              ┌─────────────┐
              │ Docker Image│
              │             │
              │ Java runtime│
              │ app.jar     │
              └─────────────┘
                     │
                 docker run
                     │
                     ▼
              ┌─────────────┐
              │  Container  │
              │             │
              │ Spring Boot │
              └─────────────┘
                     │
                     ▼
                  :8080
```

