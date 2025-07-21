## **Lesson 1: Introduction to Docker**

### **1.0 What is Docker?**

Docker is a **platform to build, run, and manage containers**.

A **container** is a lightweight, standalone package that contains:

- An application
- All its dependencies (libraries, code, system tools)

Think of it like a mini virtual machine, but more efficient and faster.

### Why use Docker?

- **Consistency:** Works the same on any machine (dev, staging, prod)
- **Isolation:** Each container runs independently
- **Portability:** Runs anywhere Docker is installed
- **Speed:** Much faster than traditional VMs

### Real-life Analogy:

Think of a container like a **shipping container**. No matter what’s inside—cars, electronics, clothes—the container fits on any ship, train, or truck. Similarly, a Docker container can run on any system with Docker installed.

### Common Terms:

| Term       | Meaning                                      |
| ---------- | -------------------------------------------- |
| Image      | Blueprint/template for a container           |
| Container  | A running instance of an image               |
| Dockerfile | Instructions to build an image               |
| Docker Hub | Public registry to share and download images |

### Quick Recap

1. **Docker**
   A platform to **build, run, and manage containers**. It ensures consistency and portability across environments.
2. **Docker Image**
   A **blueprint or template** for creating containers. It includes your app and all its dependencies.
3. **Docker Container**
   A **running instance** of an image. It executes the app in a lightweight, isolated environment using the host OS kernel.

### **1.1 Installing Docker**

To use Docker, you need to install the **Docker Engine**, which includes everything needed to build and run containers.

---

### Step-by-Step Installation (Desktop)

#### 🪟 **For Windows 10/11 (Home/Pro)**

1. Go to: [https://www.docker.com/products/docker-desktop/]()
2. Download **Docker Desktop for Windows**
3. Run the installer and follow instructions
4. Enable **WSL 2** integration if prompted (required for Home edition)
5. After installation, open **Docker Desktop**

Verify installation:

```
docker --version
```

### 🐧 **For Linux (Ubuntu example)**

```
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
docker --version
```

Give user permission to run Docker without `sudo`:

```
sudo usermod -aG docker $USER
# Then logout and login again

```

Test it works
Run your first container:

```
docker run hello-world
```

You should see a success message saying Docker is working.

**“What OS does a Docker container run?”**

A Docker container **runs the OS it was built for inside the image** — typically a **minimal Linux distro**, like:

- Alpine Linux (very small)
- Debian
- Ubuntu
- CentOS

But technically:

- It **shares the kernel** with the host machine.
- So if you’re on Windows, Docker uses a Linux VM behind the scenes.
- On Linux, containers run directly on the host kernel.

> ⚠️ Containers don’t run a full OS — they run an app on top of the host kernel with only the needed files/libraries.

### **1.2 Basic Docker Commands**

Now that Docker is installed and ready, let’s learn some basic commands to interact with Docker images and containers.

---

### 1. **Check Docker Version**

```
docker --version
```

Shows the installed Docker version.

---

### 2. **Search for Images**

```
docker search <image_name>
```

Search Docker Hub for available images.

Example:

```
docker search nginx
```

---

### 3. **Pull an Image**

```
docker pull <image_name>:<tag>
```

Downloads an image to your local machine.

Example:

```
docker pull ubuntu:latest
```

---

### 4. **List Images**

```
docker images
```

Shows all images downloaded locally.

---

### 5. **Run a Container**

```
docker run <image_name>
```

Starts a container from an image.

Example:

```
docker run ubuntu
```

- By default, it runs and then exits (because Ubuntu needs a command to keep running).

---

### 6. **Run Interactive Container**

```
docker run -it <image_name> /bin/bash
```

Runs container interactively with terminal access.

Example:

```
docker run -it ubuntu /bin/bash
```

You get a shell inside the container.

---

### 7. **List Running Containers**

```
docker ps
```

Shows containers currently running.

Add `-a` to see all (running + stopped):

```
docker ps -a
```

---

### 8. **Stop a Container**

```
docker stop <container_id or name>
```

---

### 9. **Remove a Container**

```
docker rm <container_id or name>
```

---

### 10. **Remove an Image**

```
docker rmi <image_name>
```

---

### Summary Table:

| Command                | Purpose                     |
| ---------------------- | --------------------------- |
| docker search \<name>  | Find image on Docker Hub    |
| docker pull \<name>    | Download image              |
| docker images          | List local images           |
| docker run \<name>     | Run a container             |
| docker run -it \<name> | Run container interactively |
| docker ps              | List running containers     |
| docker stop \<id>      | Stop a running container    |
| docker rm \<id>        | Remove a container          |
| docker rmi \<name>     | Remove an image             |

### **1.3 Dockerfile and Building Images**

Now we’ll learn how to create a **Dockerfile** to build your own Docker image.

---

### What is a Dockerfile?

- A text file with instructions to build a Docker image.
- Defines base image, app files, commands to run, environment, etc.

---

### Basic Dockerfile Example

write the `Dockerfile` in the **root directory of your project**

```
# Use official Node.js 18 image as base
FROM node:18

# Set working directory inside the container to /src
WORKDIR /src

# Copy package.json and package-lock.json to the container
COPY package*.json ./

# Install dependencies from package.json
RUN npm install

# Copy the rest of the application source code to the container
COPY . .

# Expose port 3000 to allow communication to/from container
EXPOSE 3000

# Run the dev script defined in package.json to start app in dev mode
CMD ["npm", "run", "start:dev"]
```

---

### Explanation of commands:

| Instruction | Description                                          |
| ----------- | ---------------------------------------------------- |
| FROM        | Base image to start from                             |
| WORKDIR     | Sets working directory inside container              |
| COPY        | Copies files from host into container                |
| RUN         | Executes commands during image build (e.g., install) |
| EXPOSE      | Declares the port the app listens on                 |
| CMD         | Default command to run when container starts         |

---

### How to build image from Dockerfile

In the folder with Dockerfile:

```
docker build -t my-node-app .
```

- `-t` tags the image as `my-node-app`
- `.` means current directory is context

---

### **1.4 Running and Managing Containers**

Now that you’ve built a Docker image, let’s go deeper into running and managing containers.

---

### Starting a Container

```
docker run -p 3000:3000 university-management-system
```

- Starts a new container from your image.
- Maps **port 3000 of the container** to **port 3000 of your host**.
- If your app logs to console, you’ll see logs directly.

---

### Run in Background (Detached Mode)

```
docker run -d -p 3000:3000 university-management-system
```

- `-d` = **detached mode**
- Container runs in background (you won't see logs directly)

---

### List Running Containers

```
docker ps

```

- Lists all currently running containers

To see all containers (even stopped):

```
docker ps -a
```

---

### View Logs of a Container

```
docker logs <container_id>
```

Get `<container_id>` from `docker ps`

---

### Stop a Running Container

```
docker stop <container_id>
```

---

### Remove a Stopped Container

```
docker rm <container_id>
```

---

### Remove an Image

```
docker rmi university-management-system
```
