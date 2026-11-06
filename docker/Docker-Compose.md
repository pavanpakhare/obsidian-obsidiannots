[[Docker]]
# Docker Compose 

**Docker Compose** lets you define and run **multiple Docker containers as one application** using a YAML file, usually `compose.yaml`.

For example, a Spring Boot application might need:

```text
Spring Boot
    │
    ├── PostgreSQL
    ├── Redis
    └── Kafka
```

Instead of creating each container manually with `docker run`, you can define everything in one Compose file.

---

## 1. Basic Compose file

Create:

```text
my-project/
├── compose.yaml
└── app/
    └── ...
```

`compose.yaml`:

```yaml
services:

  postgres:
    image: postgres:17
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
    ports:
      - "5432:5432"

  redis:
    image: redis:7
    ports:
      - "6379:6379"
```

Start:

```bash
docker compose up
```

Or run in background:

```bash
docker compose up -d
```

Check:

```bash
docker compose ps
```

Stop:

```bash
docker compose down
```

---

# 2. Important Compose concepts

The basic structure is:

```yaml
services:

  service-name:
    image: image-name
    ports:
      - "host:container"
    environment:
      VARIABLE: value
    volumes:
      - volume:/container/path
```

For example:

```yaml
services:

  postgres:
    image: postgres:17
```

Here:

```text
postgres
   │
   └── service name

postgres:17
   │
   ├── image = postgres
   └── tag   = 17
```

---

# 3. `image`

Specifies the Docker image.

```yaml
services:
  postgres:
    image: postgres:17
```

You can use:

```yaml
image: postgres:17
```

or:

```yaml
image: redis:7
```

or:

```yaml
image: nginx:latest
```

---

# 4. Ports

Syntax:

```yaml
ports:
  - "HOST_PORT:CONTAINER_PORT"
```

Example:

```yaml
ports:
  - "5432:5432"
```

Meaning:

```text
Your computer
localhost:5432
      │
      ▼
Docker container
5432
```

Another example:

```yaml
ports:
  - "8080:8080"
```

You can then access:

```text
http://localhost:8080
```

---

# 5. Environment variables

Very important for databases and applications.

```yaml
environment:
  POSTGRES_DB: mydb
  POSTGRES_USER: admin
  POSTGRES_PASSWORD: secret
```

Inside the container:

```text
POSTGRES_DB=mydb
POSTGRES_USER=admin
POSTGRES_PASSWORD=secret
```

You can also use your `.env` file.

`.env`:

```env
POSTGRES_DB=mydb
POSTGRES_USER=admin
POSTGRES_PASSWORD=my-secret-password
```

Compose:

```yaml
services:
  postgres:
    image: postgres:17
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
```

This is preferable to putting passwords directly in the Compose file.

---

# 6. Volumes

Without a volume, container data can disappear when the container is removed.

Example:

```yaml
services:
  postgres:
    image: postgres:17
    volumes:
      - postgres-data:/var/lib/postgresql/data

volumes:
  postgres-data:
```

Architecture:

```text
PostgreSQL container
        │
        │
        ▼
postgres-data
        │
        ▼
Docker-managed storage
```

Check volumes:

```bash
docker volume ls
```

Inspect:

```bash
docker volume inspect postgres-data
```

---

# 7. Bind mounts

You can also mount a directory from your computer:

```yaml
services:
  app:
    volumes:
      - ./app:/app
```

Meaning:

```text
project/app
     │
     ▼
container:/app
```

This is particularly useful during development.

---

# 8. Container networking

Compose automatically creates a network for your application.

Suppose:

```yaml
services:

  backend:
    image: my-backend

  postgres:
    image: postgres:17
```

The backend can connect to PostgreSQL using:

```text
postgres:5432
```

**Not:**

```text
localhost:5432
```

This is a very important Docker concept.

Inside the backend container:

```text
localhost
   ↓
backend container itself
```

Whereas:

```text
postgres
   ↓
PostgreSQL container
```

Example Spring Boot configuration:

```properties
spring.datasource.url=jdbc:postgresql://postgres:5432/mydb
spring.datasource.username=admin
spring.datasource.password=secret
```

---

# 9. `depends_on`

You can specify startup dependencies:

```yaml
services:

  backend:
    image: my-backend
    depends_on:
      - postgres

  postgres:
    image: postgres:17
```

Compose starts PostgreSQL before the backend.

However, **`depends_on` does not necessarily mean PostgreSQL is ready to accept connections**.

For that, use a health check.

---

# 10. Health checks

```yaml
services:

  postgres:
    image: postgres:17
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: mydb

    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin -d mydb"]
      interval: 5s
      timeout: 5s
      retries: 5
```

Then:

```yaml
backend:
  image: my-backend
  depends_on:
    postgres:
      condition: service_healthy
```

Now:

```text
PostgreSQL starts
       ↓
health check
       ↓
READY
       ↓
Backend starts
```

---

# 11. Building your own Docker image

Instead of:

```yaml
image: my-backend
```

you can tell Compose to build it:

```yaml
backend:
  build: .
```

For example:

```text
project/
├── compose.yaml
├── Dockerfile
└── src/
```

`compose.yaml`:

```yaml
services:
  backend:
    build: .
    ports:
      - "8080:8080"
```

Run:

```bash
docker compose up --build
```

Compose will:

```text
Dockerfile
    ↓
docker build
    ↓
image
    ↓
container
```

---

# 12. Spring Boot + PostgreSQL example

A very useful setup for you:

```yaml
services:

  backend:
    build: .
    ports:
      - "8080:8080"
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://postgres:5432/mydb
      SPRING_DATASOURCE_USERNAME: admin
      SPRING_DATASOURCE_PASSWORD: secret
    depends_on:
      postgres:
        condition: service_healthy

  postgres:
    image: postgres:17
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
    ports:
      - "5432:5432"
    volumes:
      - postgres-data:/var/lib/postgresql/data

    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin -d mydb"]
      interval: 5s
      timeout: 5s
      retries: 5

volumes:
  postgres-data:
```

Architecture:

```text
             Docker Compose
                  │
       ┌──────────┴──────────┐
       │                     │
       ▼                     ▼
  Spring Boot           PostgreSQL
  :8080                  :5432
       │                     │
       └──────────┬──────────┘
                  │
              Docker network
```

---

# 13. Useful commands

### Start

```bash
docker compose up
```

### Start in background

```bash
docker compose up -d
```

### Build and start

```bash
docker compose up --build
```

### Stop containers

```bash
docker compose stop
```

### Start stopped containers

```bash
docker compose start
```

### Stop and remove containers/network

```bash
docker compose down
```

### Remove containers + volumes

```bash
docker compose down -v
```

⚠️ `-v` can delete database data stored in Compose-managed volumes.

### See containers

```bash
docker compose ps
```

### Logs

```bash
docker compose logs
```

Specific service:

```bash
docker compose logs postgres
```

Follow logs:

```bash
docker compose logs -f backend
```

### Execute command inside container

```bash
docker compose exec postgres bash
```

Or:

```bash
docker compose exec postgres psql -U admin -d mydb
```

### Pull images

```bash
docker compose pull
```

### Restart

```bash
docker compose restart
```

---

# 14. `docker compose` vs `docker run`

Without Compose:

```bash
docker network create my-network

docker run -d \
  --name postgres \
  --network my-network \
  -e POSTGRES_PASSWORD=secret \
  postgres:17

docker run -d \
  --name backend \
  --network my-network \
  -p 8080:8080 \
  my-backend
```

With Compose:

```yaml
services:
  postgres:
    image: postgres:17

  backend:
    image: my-backend
```

Then:

```bash
docker compose up -d
```

That's the main benefit of Compose: **application infrastructure becomes configuration/code instead of a long list of commands.**

---

# 15. Multiple environments

You can use environment variables:

```yaml
services:
  backend:
    image: my-backend:${APP_VERSION}
```

`.env`:

```env
APP_VERSION=1.0
```

Then:

```bash
docker compose up
```

---

# 16. Override Compose configuration

You can have:

```text
compose.yaml
compose.dev.yaml
compose.prod.yaml
```

For development:

```bash
docker compose \
  -f compose.yaml \
  -f compose.dev.yaml \
  up
```

This allows you to keep development and production settings separate.

---

# 17. Compose profiles

Useful when you don't always want every service.

```yaml
services:

  backend:
    image: my-backend

  postgres:
    image: postgres:17

  adminer:
    image: adminer
    profiles:
      - tools
```

Normal:

```bash
docker compose up
```

With tools:

```bash
docker compose --profile tools up
```

---

# 18. The most important mental model

Think of `compose.yaml` as describing your entire local infrastructure:

```text
compose.yaml
     │
     ├── backend
     │     ├── image/build
     │     ├── ports
     │     ├── environment
     │     ├── volumes
     │     └── dependencies
     │
     ├── postgres
     │     ├── image
     │     ├── environment
     │     └── volume
     │
     ├── redis
     │
     └── kafka
```

Then one command:

```bash
docker compose up -d
```

