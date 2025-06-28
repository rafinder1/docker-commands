# docker-commands

# 🌐 Docker Networking – Quick Summary
## 🔗 Network Types

| Network Type | Description |
|--------------|-------------|
| `bridge`     | Default network. Containers can communicate with each other on the same host. DNS supported. |
| `host`       | Container shares the host’s network stack (no isolation). |
| `none`       | No networking. Useful for security or testing. |
| `overlay`    | Allows communication between containers across multiple Docker hosts. Used in Docker Swarm. |
| `macvlan`    | Assigns a MAC address to the container. Appears as a physical device on the network. |

---

### 🔍 List all networks
```commandline
docker network ls
```
### ➕ Create a custom network
```commandline
docker network create --driver bridge my-network
```
### 📄 Inspect a network
```commandline
docker network inspect my-network
```
### ✅ Attach the running container
```commandline
docker network connect my-network my-app
```
### ❌ To detach a container from a network
```commandline
docker network disconnect my-network my-app
```

# 🗄️ Docker Volumes vs Bind Mounts – Summary

Docker provides two main ways to persist and share data between containers and the host system:

---

## 1. Volumes

- Managed by Docker and stored in Docker's storage area (usually `/var/lib/docker/volumes/`).
- Best for persistent data that Docker controls.
- Easier to back up, migrate, and manage.
- Decoupled from the host filesystem path.

### 🔹 Defining Volumes in Dockerfile
```dockerfile
VOLUME ["/app/data"]
```
This creates a volume at /app/data inside the container.

### 🔹 Using Volumes with docker run
```commandline
docker volume create my-volume

docker run -d \
  -v my-volume:/app/data \
  --name my-container \
  my-image
```

## 2. Bind Mounts

- Maps a specific directory or file from the host filesystem into the container.
- Useful for sharing code, configs, or logs during development.
- Host controls the lifecycle and path.

### 🔹 Using Bind Mounts with docker run
```commandline
docker run -d \
  -v $(pwd)/local-data:/app/data \
  --name test-container \
  my-image
```

## 🔒 Mounting Modes for Volumes and Bind Mounts

Docker allows you to control **access permissions** when mounting volumes or bind mounts. There are two main syntax styles:

### 🔹 1. Legacy `-v` flag (short syntax)

```bash
docker run -v /host/path:/container/path[:ro|rw] my-image
```

# Building a Docker Image from a Dockerfile
You can build a Docker image using the `docker build` command. Below are typical scenarios:
### 1. Build image from Dockerfile
#### 1.1. Only one Dockerfile in project:
```commandline
docker build -t image-name:tag .
```
#### 1.2. More Dockerfile in project:
```commandline
docker build -t image-name:tag -f Dockerfile.name .
```
###### Example:
```commandline
docker build -t my-app:1.0 -f Dockerfile ./args-env
```
#### 1.3. Pass a Build Argument (ARG) When Building the Image:
```commandline
docker build --build-arg VERSION=1.0 -t image-name:tag .
```
###### Example:
```commandline
docker build --build-arg VERSION=1.0 -t my-app:1.0 ./args-env
```

## Running a Docker Image
```commandline
docker run -d -p 80:80 -e PASSWORD=passwd -v $(pwd)/logs-dir:/logs --name my-app nginx
```
## 🧰 Most Commonly Used `docker run` Flags

| Flag                | Description                                                             |
|---------------------|-------------------------------------------------------------------------|
| `-d`                | Run in background (detached mode)                                       |
| `-p host:container` | Port mapping between host and container                                 |
| `-e VAR=value`      | Set environment variable                                                 |
| `-v host:container` | Mount a volume or bind host directory                                    |
| `--name`            | Assign a name to the container                                           |
| `--rm`              | Automatically remove the container after it exits                       |
| `--restart`         | Restart policy (`no`, `on-failure`, `always`, `unless-stopped`)         |
| `--env-file`        | Load environment variables from a file                                  |
| `--network`         | Set the container’s network                                              |
| `--cpus`            | Limit container CPU usage (e.g. `--cpus="0.5"`)                          |
| `--memory`          | Limit container memory (e.g. `--memory="256m"`)                          |
| `--entrypoint`      | Override the image’s default `ENTRYPOINT`                               |
| `--log-driver`      | Set the log driver (`json-file`, `none`, `syslog`, etc.)                |
| `--user`            | Run container as a specific user (e.g. `--user 1000:1000`)               |
| `--read-only`       | Make the container's root filesystem read-only                          |


## 🐳 Dockerfile Instructions: `RUN` vs `CMD` vs `ENTRYPOINT`

### Summary Table

| Instruction | When it Runs          | Purpose                            | Saved in Image | Runs at Runtime | Can Be Overridden? |
|-------------|------------------------|-------------------------------------|----------------|------------------|---------------------|
| `RUN`       | During `docker build` | Install software, build config     | ✅ Yes         | ❌ No           | ❌ No               |
| `CMD`       | At container runtime  | Default command if none is given   | ✅ Yes         | ✅ Yes          | ✅ Yes (`docker run`)|
| `ENTRYPOINT`| At container runtime  | Main executable                    | ✅ Yes         | ✅ Yes          | ⚠️ Only with `--entrypoint` |
