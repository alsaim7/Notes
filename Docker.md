# Docker Learning Notes

## What is Docker?

Docker is a tool used to create isolated environments called containers.

It helps developers:

* Run applications consistently on every machine
* Avoid "it works on my machine" problems
* Package backend, frontend, databases, and dependencies together
* Deploy applications easily

---

# Core Docker Concepts

## 1. Image

An image is a blueprint/template.

Example:

* Python image
* Ubuntu image
* FastAPI backend image

Images contain:

* Operating system
* Runtime
* Dependencies
* Application code

### Command

```bash
docker images
```

### Use

Shows all downloaded/built images.

---

## 2. Container

A container is a running/stopped instance created FROM an image.

You can:

* Start it
* Stop it
* Delete it
* Create multiple containers from same image

### Command

```bash
docker ps
```

### Use

Shows running containers.

---

### Command

```bash
docker ps -a
```

### Use

Shows ALL containers:

* running
* stopped
* exited

---

# Docker Workflow

## Step 1 → Create Dockerfile

Dockerfile contains instructions for creating an image.

Example:

```dockerfile
FROM python:3.11.5

WORKDIR /app

COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

---

## Step 2 → Build Image

### Command

```bash
docker build -t panelsense-backend .
```

### Explanation

| Part               | Meaning                      |
| ------------------ | ---------------------------- |
| docker build       | Build image                  |
| -t                 | Tag/name image               |
| panelsense-backend | Image name                   |
| .                  | Current folder/build context |

### Use

Creates a custom image from Dockerfile.

---

## Step 3 → Run Container

### Command

```bash
docker run -p 8000:8000 --env-file .env panelsense-backend
```

### Explanation

| Part               | Meaning                    |
| ------------------ | -------------------------- |
| docker run         | Create and start container |
| -p 8000:8000       | Port mapping               |
| --env-file .env    | Load environment variables |
| panelsense-backend | Image name                 |

---

# Important Docker Concepts

## Port Mapping

```bash
-p 8000:8000
```

Format:

```text
HOST_PORT:CONTAINER_PORT
```

Example:

```text
localhost:8000 → container:8000
```

Without port mapping:

* browser cannot access container app

---

## Environment Variables

Containers are isolated.

They do NOT automatically know:

* API keys
* database URLs
* local environment variables

That is why we use:

```bash
--env-file .env
```

---

## Container Isolation

Inside Docker:

```text
localhost ≠ your PC
```

Inside container:

```text
localhost = current container itself
```

---

# .dockerignore

Used to ignore unnecessary files during image build.

Example:

```text
backend-env/
__pycache__/
.git
.env
```

---

## Why .dockerignore is Important

Without it:

* images become huge
* builds become slow
* secrets may leak

---

# Useful Docker Commands

## Show Images

```bash
docker images
```

Shows all images.

---

## Show Running Containers

```bash
docker ps
```

Shows running containers only.

---

## Show All Containers

```bash
docker ps -a
```

Shows:

* running
* stopped
* exited containers

---

## Run Container

```bash
docker run image-name
```

Creates and starts container.

---

## Run Container with Port Mapping

```bash
docker run -p 8000:8000 image-name
```

Makes app accessible in browser.

---

## Run Container in Background

```bash
docker run -d image-name
```

### Use

Runs container in detached/background mode.

---

## Stop Container

```bash
docker stop <container_id>
```

### Use

Stops running container.

---

## Remove Container

```bash
docker rm <container_id>
```

### Use

Deletes container.

---

## Remove Image

```bash
docker rmi <image_name>
```

### Use

Deletes image.

---

## View Logs

```bash
docker logs <container_id>
```

### Use

Shows container logs/errors.

Very useful for debugging.

---

## Cleanup Unused Docker Data

```bash
docker system prune
```

### Use

Removes:

* stopped containers
* unused cache
* dangling layers

---

## More Aggressive Cleanup

```bash
docker system prune -a
```

### Use

Removes almost all unused Docker data.

---

# Docker Storage Concepts

## Images Share Layers

Docker images reuse common layers.

Example:

* Python image layer
* Linux layer

This saves storage.

---

# FastAPI + Docker Architecture

Current Architecture:

```text
Browser
↓
localhost:8000
↓
Docker Port Mapping
↓
FastAPI Container
↓
Neon PostgreSQL + Gemini API
```

---

# Important Lessons Learned

## Lesson 1

Image = blueprint/template.

---

## Lesson 2

Container = running instance of image.

---

## Lesson 3

Containers are isolated environments.

---

## Lesson 4

Dockerfile = environment as code.

---

## Lesson 5

.dockerignore is very important.

---

## Lesson 6

Cloud databases work naturally with containers.

---

# Current Skill Level Achieved

You now know:

* Docker images
* Containers
* Dockerfile
* Port mapping
* Environment variables
* Container isolation
* FastAPI Dockerization
* Docker cleanup
* Docker debugging basics

This is already strong beginner Docker knowledge.

---

# Docker Compose Learning

## What is Docker Compose?

Docker Compose is used to manage multiple containers together.

Instead of manually running:

* frontend container
* backend container
* database container

one by one,

Docker Compose controls all services using one file:

```text
docker-compose.yml
```

---

# Why Docker Compose is Useful

Without Compose:

```bash
docker run backend
docker run frontend
docker run postgres
```

Very messy.

---

With Compose:

```bash
docker compose up
```

Everything starts automatically.

---

# Important Compose Concept

Docker Compose can automatically:

* build images
* create containers
* start services

You do NOT always need:

```bash
docker build
```

manually.

---

# Example Project Structure

```text
prac_gen_ai/
 ├── backend/
 ├── frontend/
 └── docker-compose.yml
```

---

# Backend Dockerfile

```dockerfile
FROM python:3.11.5

WORKDIR /app

COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

---

# Frontend Dockerfile (Vite React)

```dockerfile
FROM node:20

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 5173

CMD ["npm", "run", "dev", "--", "--host"]
```

---

# Frontend .dockerignore

```text
node_modules
dist
.git
.env
```

---

# docker-compose.yml

```yaml
services:
  backend:
    build: ./backend
    ports:
      - "8000:8000"
    env_file:
      - ./backend/.env

  frontend:
    build: ./frontend
    ports:
      - "5173:5173"
```

---

# YAML Important Notes

## Hyphen (-)

Used for arrays/lists.

Example:

```yaml
ports:
  - "8000:8000"
```

Without hyphen:

* YAML structure becomes incorrect.

---

# Important Compose Commands

## Start All Services

```bash
docker compose up
```

### Use

Builds images if needed.

Creates containers.

Starts all services together.

---

## Start in Background

```bash
docker compose up -d
```

### Use

Runs all services in detached/background mode.

---

## Stop Services

```bash
docker compose down
```

### Use

Stops and removes compose containers.

---

## Rebuild Services

```bash
docker compose up --build
```

### Use

Forces image rebuild.

Useful after code or Dockerfile changes.

---

## View Running Containers

```bash
docker ps
```

---

# Current Multi-Container Architecture

```text
Browser
↓
React Frontend Container
↓
FastAPI Backend Container
↓
Neon PostgreSQL + Gemini API
```

---

# What You Successfully Achieved

You successfully:

* Dockerized FastAPI backend
* Dockerized React frontend
* Connected both using Docker Compose
* Connected backend with Neon PostgreSQL
* Connected Gemini API inside container
* Learned container networking
* Learned Docker environment variables
* Learned Docker image building
* Learned Docker debugging

This is already strong real-world Docker beginner knowledge.

---

# Next Topics Later

Future Docker topics:

* Docker Compose
* Multi-container apps
* PostgreSQL container
* React container
* Docker volumes
* Docker networking
* Deployment on VPS/cloud
