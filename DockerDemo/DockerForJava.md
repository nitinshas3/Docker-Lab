# 🐳 Docker for Java

## Base Image

- Use `openjdk` (e.g., `openjdk:17-jdk`) which already has JDK installed.
- This image provides the Java runtime environment inside the container.

## JShell

- Can be invoked directly inside the container:

```bash
docker run -it openjdk:17 jshell
```

Starts the Java REPL (JShell) inside the container.

## Image Search & Pull

Search via CLI:

```bash
docker search openjdk
```

Or check Docker Desktop GUI for versions compatible with your OS/architecture:

- AMD64 → Intel/AMD processors
- ARM64 → Apple Silicon (M1/M2) Macs

Pull the chosen version:

```bash
docker pull openjdk:17-jdk
```

## Run Behavior

- Running the container executes the command defined in Dockerfile (`CMD`/`ENTRYPOINT`).
- If `CMD` is `jshell`, JShell starts.
- If `CMD` is `java -jar app.jar`, the server starts.

## Continuous Running

Interactive mode → Keeps container alive until you exit:

```bash
docker run -it openjdk:17-jdk
```

Detached mode → Runs in background without auto-exit:

```bash
docker run -dit openjdk:17-jdk
```

# MOST IMPORTANT PART BEGINS HERE #

# Dockerizing a Spring Boot Application

## 1. Running the JAR

After building the project, Spring Boot generates a JAR file inside the `target` directory.

### Run the generated JAR

```bash
java -jar target/DockerDemo-0.0.1-SNAPSHOT.jar
```

### Optional: Use a Cleaner JAR Name

Configure `<finalName>` in `pom.xml`:

```xml
<build>
    <finalName>DockerDemo</finalName>
</build>
```

This generates:

```text
target/DockerDemo.jar
```

Run it using:

```bash
java -jar target/DockerDemo.jar
```

---

# Dockerizing a JAR Application

## Step 1: Build the JAR

```bash
mvn clean package
```

**What it does:**

* Cleans previous build files.
* Compiles source code.
* Runs tests.
* Packages the application as a JAR.

Generated file:

```text
target/DockerDemo.jar
```

---

## Step 2: Create a Dockerfile

Create a file named `Dockerfile` in the project root:

```dockerfile
FROM openjdk:17-jdk-alpine

# Working directory inside container
WORKDIR /app

# Copy JAR into container
COPY target/DockerDemo.jar app.jar

# Run the Spring Boot application
CMD ["java", "-jar", "app.jar"]
```

### Explanation

| Instruction | Purpose                               |
| ----------- | ------------------------------------- |
| `FROM`      | Uses OpenJDK 17 as the base image     |
| `WORKDIR`   | Sets `/app` as the working directory  |
| `COPY`      | Copies the JAR from host to container |
| `CMD`       | Starts the Spring Boot application    |

---

## Step 3: Build Docker Image

```bash
docker build -t dockerdemo:1.0 .
```

### Explanation

* `docker build` → Builds an image.
* `-t dockerdemo:1.0` → Assigns image name and tag.
* `.` → Uses the current directory as build context.

---

## Step 4: Run Docker Container

```bash
docker run -p 8080:8080 dockerdemo:1.0
```

### Explanation

* `docker run` → Creates and starts a container.
* `-p 8080:8080` → Maps host port 8080 to container port 8080.
* `dockerdemo:1.0` → Image to run.

---

# Dockerizing a WAR Application (Servlet/JSP)

WAR files are deployed to an external servlet container such as Tomcat.

---

## Step 1: Build the WAR

```bash
mvn clean package
```

Generated file:

```text
target/DockerDemo.war
```

---

## Step 2: Create Dockerfile

```dockerfile
FROM tomcat:10.1-jdk17

# Deploy WAR into Tomcat
COPY target/DockerDemo.war /usr/local/tomcat/webapps/DockerDemo.war
```

### Explanation

| Instruction | Purpose                                     |
| ----------- | ------------------------------------------- |
| `FROM`      | Uses Tomcat 10 with JDK 17                  |
| `COPY`      | Deploys WAR to Tomcat's `webapps` directory |

Tomcat automatically extracts and deploys the WAR during startup.

---

## Step 3: Build Docker Image

```bash
docker build -t dockerdemo-war:1.0 .
```

---

## Step 4: Run Docker Container

```bash
docker run -p 8080:8080 dockerdemo-war:1.0
```

---

## Access the Application

Open a browser and visit:

```text
http://localhost:8080/DockerDemo
```

---

# Summary

## JAR Deployment

* Uses Spring Boot's embedded Tomcat.
* Runs directly using `java -jar`.
* Smaller and simpler deployment.
* Docker image is based on OpenJDK.

### Flow

```text
Spring Boot App
       ↓
mvn clean package
       ↓
DockerDemo.jar
       ↓
OpenJDK Docker Image
       ↓
Container Running Application
```

---

## WAR Deployment

* Uses an external Tomcat server.
* WAR file is deployed into Tomcat.
* Suitable for traditional Servlet/JSP applications.
* Docker image is based on Tomcat.

### Flow

```text
Spring Boot / Servlet App
           ↓
mvn clean package
           ↓
DockerDemo.war
           ↓
Tomcat Docker Image
           ↓
Tomcat Deploys WAR
           ↓
Container Running Application
```
# Dockerizing a Spring Boot JAR Application

## 01. Pull Base Image

Start with a lightweight Java runtime environment.

```bash
docker pull openjdk:17-jdk-alpine
```

* Downloads OpenJDK base image from Docker Hub.
* Provides the JVM required to run the JAR file.

---

## 02. Set Working Directory

Choose a clean folder inside the container to store your application.

```dockerfile
WORKDIR /app
```

* Creates the `/app` directory inside the container.
* All subsequent commands execute from this directory.

---

## 03. Add JAR File

Move the packaged JAR into the container.

```dockerfile
ADD target/DockerDemo.jar app.jar
```

* Copies the JAR from the host machine into `/app`.
* Renames it to `app.jar` for simplicity.

### COPY vs ADD

| COPY                                 | ADD                                                         |
| ------------------------------------ | ----------------------------------------------------------- |
| Only copies files/directories.       | Can copy files/directories and perform additional features. |
| Simple and preferred for most cases. | Can extract local `.tar` archives automatically.            |
| More predictable.                    | Slightly more powerful but often unnecessary.               |

**For a normal Spring Boot JAR, `COPY` is generally recommended, but `ADD` also works.**

---

## 04. Define Startup Command

Tell Docker how to run the application when the container starts.

```dockerfile
ENTRYPOINT ["java", "-jar", "app.jar"]
```

* JVM automatically executes the JAR.
* Container starts the Spring Boot application immediately.

### CMD vs ENTRYPOINT

| CMD                                                  | ENTRYPOINT                                                         |
| ---------------------------------------------------- | ------------------------------------------------------------------ |
| Provides a default command.                          | Defines the main command of the container.                         |
| Can be easily overridden when running the container. | Runs every time the container starts unless explicitly overridden. |
| Used for optional/default behavior.                  | Used when the container should always execute a specific program.  |

#### Example

Dockerfile:

```dockerfile
CMD ["java", "-jar", "app.jar"]
```

Running:

```bash
docker run myimage ls
```

Output:

* `ls` replaces the CMD.

---

Dockerfile:

```dockerfile
ENTRYPOINT ["java", "-jar", "app.jar"]
```

Running:

```bash
docker run myimage
```

Output:

* Always runs the Spring Boot application.

---

## 05. Build Docker Image

Create a reusable image containing the application and runtime environment.

```bash
docker build -t dockerdemo:1.0 .
```

* Reads instructions from the Dockerfile.
* Creates an image tagged `dockerdemo:1.0`.

---

## 06. Run Docker Container

Start a live instance of the Docker image.

```bash
docker run -p 8080:8080 dockerdemo:1.0
```

* Launches a container from the image.
* Maps container port `8080` to host port `8080`.

### Access the Application

```text
http://localhost:8080
```

* Open the URL in a browser to access the application.

---

## Complete Dockerfile

```dockerfile
FROM openjdk:17-jdk-alpine

WORKDIR /app

ADD target/DockerDemo.jar app.jar

ENTRYPOINT ["java", "-jar", "app.jar"]
```

### Dockerfile Flow

```text
Pull OpenJDK Image
        ↓
Set Working Directory (/app)
        ↓
Add DockerDemo.jar
        ↓
Define ENTRYPOINT
        ↓
Build Image
        ↓
Run Container
        ↓
Application Available on Port 8080
```
