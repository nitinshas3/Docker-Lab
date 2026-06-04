# 🐳 Docker Notes

## Image vs Container
- **Image** → Just has the dependencies listed in it, like a text file.  
- **Container** → Actually has those dependencies downloaded and running.  
- When a container is loaded, Docker checks locally if the image exists. If not, it pulls it from **Docker Hub**.

## Base Images
- To create an image, we usually start with a **base image** (like Ubuntu, Postgres, Node, etc.).  
- Base images provide OS user‑space, runtimes, or services that your app depends on.

## Virtualization vs Docker
- In virtualization, the OS on which the project was developed had to be packaged and sent as a **guest OS**.  
- Example: If developed on Windows, Windows itself had to be packaged with the code.  
- **Hypervisor** (like VirtualBox) acts as dummy hardware for the guest OS.  
  - Guest OS feels like it’s getting real hardware.  
  - Hypervisor communicates with host OS or directly with hardware.  
  - Guest OS does not access hardware directly.

## Dockerfile
- A **script/recipe** that tells Docker how to build the image.  
- Steps:
  - Pick base image  
  - Install dependencies  
  - Copy files  
  - Set working directory  
  - Define CMD (how to run the app)  
- **Image** → Snapshot with everything baked in.  
- **Container** → Running instance of that image.  
- **Docker Hub** → Registry of images (like GitHub but for Docker).

so like if you add postgres image in your image as base image then in you continer postgres gets downloaded ,like that

# Docker Notes

# Theory

- `docker pull` means downloading an image from Docker Hub to your local machine.

- `docker run` means creating and starting a container from an image.

- When we run an image directly using `docker run`, Docker first checks whether the image exists locally.

- If the image is not found locally, Docker searches Docker Hub, pulls the image, and then runs it.

- So `docker run` can automatically perform:

```text
Pull Image → Create Container → Start Container
```

if the image is not already available.

---

- In a Dockerfile, if your application normally starts using a command like:

```text
npm start
app:begin
java -jar app.jar
```

that command is written inside:

```text
CMD
```

instruction.

- During image build, Docker does NOT execute the command.

- Docker only stores the instruction inside the image.

- When a container starts, Docker executes that stored command inside the container environment.

---

- Containers depend on images.

- Therefore:

```text
Container must be removed first.
Image must be removed later.
```

- Trying to remove an image before removing its containers will fail.

---

# Docker Workflow

## Step 1: Search Image

Search whether image exists:

```text
docker search <image-name>
```

Official images are usually marked as:

```text
OFFICIAL
```

---

## Step 2: Pull Image

Download image:

```text
docker pull <image-name>
```

---

## Step 3: Create Container

Create container from image:

```text
docker create <image-name>
```

Returns:

```text
Container ID
```

---

## Step 4: Start Container

Start created container:

```text
docker start <container-id>
```

Usually first few characters of container id are enough.

Example:

```text
docker start a1b2
```

---

## Step 5: Stop Container

Stop running container:

```text
docker stop <container-id>
```

---

## Step 6: Pause Container

Temporarily pause container:

```text
docker pause <container-id>
```

---

## Step 7: Remove Container

Delete container:

```text
docker rm <container-id>
```

---

# Important

`docker run` combines multiple steps together:

```text
Pull Image
    ↓
Create Container
    ↓
Start Container
```

into a single command.

---

# Commands

## Show Running Containers

```text
docker ps
```

Shows only running containers.

---

## Show All Containers

```text
docker ps -a
```

Shows:

- Running containers
- Stopped containers

---

## Docker Help

```text
docker help
```

Displays available Docker commands and usage information.

# 🐳 Docker Architecture (Short Note)

- **Docker Client** → User interacts via CLI or GUI, sends commands through Docker API.  
- **Docker Daemon** → Background service that manages images, containers, networks, and volumes.  
- **Image** → Read‑only blueprint (base OS, runtimes, app code).  
- **Container** → Running instance of an image with writable layer.  
- **Network** → Virtual networks for containers to communicate with each other or outside world.  
- **Volume** → Persistent storage for data that survives container restarts.  
- **Registry** → Storage/distribution system for images.  
  - Public → Docker Hub.  
  - Private → AWS ECR, Azure Container Registry, Google Artifact Registry, etc.
