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