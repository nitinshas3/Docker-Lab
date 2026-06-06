# Docker Lab 🐳

A hands-on playground for learning Docker basics with a Java developer's perspective.
Inspired by [Telusko's YouTube series](https://www.youtube.com/@Telusko).

## What's Inside

- **Docker basics** — images, containers, Dockerfile, Docker Hub
- **Docker + Java** — running OpenJDK, JShell inside containers
- **Dockerizing Spring Boot** — JAR and WAR deployment
- **Docker Compose** — multi-container setup with PostgreSQL
- **Networking** — how containers talk to each other
- **Volumes** — persisting database data

## Project Structure

```
Docker-Lab/
└── DockerDemo/   # Spring Boot app with Dockerfile
```

## Quick Start

```bash
# Build the JAR
mvn clean package

# Build Docker image
docker build -t dockerdemo:1.0 .

# Run the container
docker run -p 8080:8080 dockerdemo:1.0
```

App available at `http://localhost:8080`

## Sample Dockerfile (Spring Boot JAR)

```dockerfile
FROM openjdk:17-jdk-alpine
WORKDIR /app
ADD target/DockerDemo.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```

## Sample Docker Compose (Spring Boot + PostgreSQL)

```yaml
services:
  app:
    build: .
  db:
    image: postgres:17
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

```bash
docker compose up
```

> Inside Docker Compose, use `jdbc:postgresql://db:5432/mydb` — not `localhost`.

## Prerequisites

- Docker
- Java JDK 17+
- Maven

## Author

**Nitin S Shastri**
