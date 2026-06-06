# 🐳 Docker + Spring Boot + PostgreSQL Quick Notes

## 1. What is a Docker Image?

A Docker Image is a packaged blueprint containing:

* Application code
* Runtime (JDK, Node.js, etc.)
* Libraries and dependencies
* Startup command

Example:

```dockerfile
FROM openjdk:17-jdk

WORKDIR /app

ADD target/app.jar app.jar

ENTRYPOINT ["java","-jar","app.jar"]
```

Build:

```bash
docker build -t my-app .
```

Run:

```bash
docker run my-app
```

---

## 2. Image vs Container

### Image

Blueprint / Template

```text
my-app-image
```

### Container

Running instance of an image

```text
my-app-image
      ↓
my-app-container
```

---

## 3. One Project ≠ One Image

A project may contain multiple services.

Example:

```text
E-Commerce Project
│
├── Frontend
├── Backend
├── PostgreSQL
└── Redis
```

Each service usually gets its own image:

```text
frontend-image
backend-image
postgres-image
redis-image
```

Reason:

* Easier scaling
* Easier maintenance
* Follows Docker's "one service per container" philosophy

---

## 4. Spring Boot + PostgreSQL Setup

### App Image

Contains:

```text
Java Runtime
+
Spring Boot
+
Application Code
+
PostgreSQL JDBC Driver
```

### PostgreSQL Image

Contains:

```text
PostgreSQL Server
+
Database Engine
+
Storage
```

Important:

```text
JDBC Driver ≠ PostgreSQL Server
```

The JDBC dependency only allows Java to communicate with PostgreSQL.

---

## 5. What Happens During mvn package?

```bash
mvn package
```

Produces:

```text
app.jar
```

Contains:

```text
Your Code
+
Spring Libraries
+
JDBC Driver
```

Does NOT contain:

```text
❌ PostgreSQL Server
❌ Database Data
❌ PostgreSQL Process
```

---

## 6. Why Use Separate Containers?

Instead of:

```text
Java + PostgreSQL + Redis
```

inside one container,

use:

```text
Container 1 → Spring Boot
Container 2 → PostgreSQL
Container 3 → Redis
```

Benefits:

* Independent updates
* Independent scaling
* Better isolation

---

## 7. Docker Compose

Docker Compose manages multiple containers together.

File:

```text
docker-compose.yml
```

Example:

```yaml
services:

  app:
    build: .

  db:
    image: postgres:17

  redis:
    image: redis:7
```

Run everything:

```bash
docker compose up
```

Docker will:

1. Build images
2. Pull required images
3. Create a network
4. Start containers
5. Connect containers

---

## 8. How Containers Communicate

Docker Compose automatically creates a network.

```text
app-container
      |
      v
db-container
```

Containers communicate using service names.

Example:

```yaml
services:
  db:
    image: postgres:17
```

Service name:

```text
db
```

can be used as a hostname.

---

## 9. JDBC URL Rules

### Local Development

Spring Boot in IntelliJ:

```properties
jdbc:postgresql://localhost:5432/mydb
```

because PostgreSQL runs on your machine.

---

### Docker Compose

```yaml
services:
  app:
  db:
```

Use:

```properties
jdbc:postgresql://db:5432/mydb
```

because:

```text
db
↓
PostgreSQL Container
```

Do NOT use:

```properties
jdbc:postgresql://localhost:5432/mydb
```

inside a container.

Inside a container:

```text
localhost
```

means:

```text
the container itself
```

---

## 10. PostgreSQL Docker Image

Usually no custom Dockerfile is needed.

Just use:

```yaml
db:
  image: postgres:17
```

with environment variables:

```yaml
environment:
  POSTGRES_DB: mydb
  POSTGRES_USER: user
  POSTGRES_PASSWORD: password
```

The official image already contains:

```text
PostgreSQL Server
+
Tools
+
Configuration
```

---

## 11. Persisting Database Data

Without volumes:

```text
Delete Container
      ↓
Data Lost
```

Use a volume:

```yaml
db:
  image: postgres:17
  volumes:
    - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

Result:

```text
Delete Container
      ↓
Volume Remains
      ↓
Data Remains
```

---

# Final Mental Model

```text
Project
   ↓
Docker Compose
   ↓
Multiple Services
   ↓
Each Service has an Image
   ↓
Each Image creates Containers
   ↓
Containers communicate through Docker Network
```

### Example

```text
User
  |
  v
Frontend
  |
  v
Backend
 /     \
v       v
Redis PostgreSQL
```

Compose manages everything, while each service runs in its own container.

Can one container contain multiple services?
YES

Should production applications usually do it?
NO

Will it still work?
ABSOLUTELY YES


# 🌐 Docker Networking & Docker Compose (Short Notes)

## Why Networks?

Containers are isolated by default.

```text
Container A  ✖  Container B
```

A network allows containers to communicate.

```text
App Container  --->  DB Container
```

---

## Manual Docker Networking

Create a network:

```bash
docker network create my-network
```

Run containers on it:

```bash
docker run --network my-network --name db postgres:17
docker run --network my-network --name app my-app
```

---

## Docker Compose Networking

```yaml
services:
  app:
    build: .

  db:
    image: postgres:17
```

Run:

```bash
docker compose up
```

Docker automatically:

1. Creates a network
2. Creates containers
3. Connects containers

No manual network creation needed.

---

## Service Name = Hostname

Compose:

```yaml
services:
  db:
    image: postgres:17
```

Spring Boot:

```properties
spring.datasource.url=jdbc:postgresql://db:5432/mydb
```

`db` is automatically resolved to the PostgreSQL container.

---

## localhost vs Service Name

Inside a container:

```text
localhost = current container
```

Wrong:

```properties
jdbc:postgresql://localhost:5432/mydb
```

Correct:

```properties
jdbc:postgresql://db:5432/mydb
```

---

## Custom Networks (Optional)

```yaml
services:
  app:
    networks:
      - backend

  db:
    networks:
      - backend

networks:
  backend:
```

Used mainly for large applications and security.

---

## Docker Compose Workflow

```text
docker compose up
        ↓
Build/Pull Images
        ↓
Create Network
        ↓
Create Containers
        ↓
Start Containers
        ↓
Application Running
```

---

## Quick Revision

```text
Image      = Blueprint
Container  = Running Image
Network    = Communication Channel
Compose    = Manages Multiple Containers

localhost  = Current Container
db         = PostgreSQL Container
```
# 📦 Docker Volumes - Short Notes

## What is a Volume?

A Docker Volume is a storage location used to persist data outside a container.

Think:

```text
Container = Running Application
Volume    = Persistent Storage
```

---

## Why Are Volumes Needed?

Containers are temporary, but data should be permanent.

Databases (PostgreSQL, MySQL, MongoDB, etc.) need volumes so that data survives even if containers are removed.

---

## Without a Volume

```text
Container
├── PostgreSQL
└── Database Data
```

* Data is stored inside the container.
* Stopping and starting the container keeps the data.
* Deleting the container deletes the data.

```text
docker stop  → Data Safe ✅
docker start → Data Safe ✅
docker rm    → Data Lost ❌
```

---

## With a Volume

```text
Container
└── PostgreSQL

Volume
└── Database Data
```

* Data is stored in the volume.
* Container can be deleted safely.
* Data remains available.

```text
Delete Container → Data Safe ✅
Create New Container → Reuse Same Data ✅
```

---

## Where Is a Volume Stored?

On the machine running Docker.

Examples:

```text
Laptop Running Docker
      ↓
Volume Stored on Laptop
```

```text
Cloud Server Running Docker
      ↓
Volume Stored on Server Disk
```

Clients/users do NOT store the volume.

---

## Does a Volume Reduce Image Size?

No.

The image size remains the same.

A volume simply moves application data outside the container filesystem, making it persistent and easier to manage.

---

## Container Lifecycle & Data

```text
Image
  ↓
Container
```

### Stop Container

* Container exists
* Data remains

### Start Container

* Same container resumes
* Data remains

### Remove Container

* Container deleted
* Internal container data deleted

### Remove Container + Use Volume

* Container deleted
* Volume remains
* Data remains

---

## Quick Revision

```text
Without Volume:
    Data inside container
    Delete container → Data lost

With Volume:
    Data outside container
    Delete container → Data survives

Volume = Persistent Storage
Container = Running Application
```
### Docker Image Storage & Sharing

- `docker build` stores the image **locally** on your machine.
- Images are **not uploaded to Docker Hub automatically**.
- Share online: `docker push` → Docker Hub/Registry → others use `docker pull`.
- Share offline: `docker save` → `.tar` file → others use `docker load`.