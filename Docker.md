Docker Learning Notes

What is Docker?

Docker is a tool used to create isolated environments called containers.

It helps developers:

Run applications consistently on every machine

Avoid "it works on my machine" problems

Package backend, frontend, databases, and dependencies together

Deploy applications easily



---

Core Docker Concepts

1. Image

An image is a blueprint/template.

Example:

Python image

Ubuntu image

FastAPI backend image


Images contain:

Operating system

Runtime

Dependencies

Application code


Command

docker images

Use

Shows all downloaded/built images.


---

2. Container

A container is a running/stopped instance created FROM an image.

You can:

Start it

Stop it

Delete it

Create multiple containers from same image


Command

docker ps

Use

Shows running containers.


---

Command

docker ps -a

Use

Shows ALL containers:

running

stopped

exited



---

Docker Workflow

Step 1 → Create Dockerfile

Dockerfile contains instructions for creating an image.

Example:

FROM python:3.11.5

WORKDIR /app

COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]


---

Step 2 → Build Image

Command

docker build -t panelsense-backend .

Explanation

Part	Meaning

docker build	Build image
-t	Tag/name image
panelsense-backend	Image name
.	Current folder/build context


Use

Creates a custom image from Dockerfile.


---

Step 3 → Run Container

Command

docker run -p 8000:8000 --env-file .env panelsense-backend

Explanation

Part	Meaning

docker run	Create and start container
-p 8000:8000	Port mapping
--env-file .env	Load environment variables
panelsense-backend	Image name



---

Important Docker Concepts

Port Mapping

-p 8000:8000

Format:

HOST_PORT:CONTAINER_PORT

Example:

localhost:8000 → container:8000

Without port mapping:

browser cannot access container app



---

Environment Variables

Containers are isolated.

They do NOT automatically know:

API keys

database URLs

local environment variables


That is why we use:

--env-file .env


---

Container Isolation

Inside Docker:

localhost ≠ your PC

Inside container:

localhost = current container itself


---

.dockerignore

Used to ignore unnecessary files during image build.

Example:

backend-env/
__pycache__/
.git
.env


---

Why .dockerignore is Important

Without it:

images become huge

builds become slow

secrets may leak



---

Useful Docker Commands

Show Images

docker images

Shows all images.


---

Show Running Containers

docker ps

Shows running containers only.


---

Show All Containers

docker ps -a

Shows:

running

stopped

exited containers



---

Run Container

docker run image-name

Creates and starts container.


---

Run Container with Port Mapping

docker run -p 8000:8000 image-name

Makes app accessible in browser.


---

Run Container in Background

docker run -d image-name

Use

Runs container in detached/background mode.


---

Stop Container

docker stop <container_id>

Use

Stops running container.


---

Remove Container

docker rm <container_id>

Use

Deletes container.


---

Remove Image

docker rmi <image_name>

Use

Deletes image.


---

View Logs

docker logs <container_id>

Use

Shows container logs/errors.

Very useful for debugging.


---

Cleanup Unused Docker Data

docker system prune

Use

Removes:

stopped containers

unused cache

dangling layers



---

More Aggressive Cleanup

docker system prune -a

Use

Removes almost all unused Docker data.


---

Docker Storage Concepts

Images Share Layers

Docker images reuse common layers.

Example:

Python image layer

Linux layer


This saves storage.


---

FastAPI + Docker Architecture

Current Architecture:

Browser
↓
localhost:8000
↓
Docker Port Mapping
↓
FastAPI Container
↓
Neon PostgreSQL + Gemini API


---

Important Lessons Learned

Lesson 1

Image = blueprint/template.


---

Lesson 2

Container = running instance of image.


---

Lesson 3

Containers are isolated environments.


---

Lesson 4

Dockerfile = environment as code.


---

Lesson 5

.dockerignore is very important.


---

Lesson 6

Cloud databases work naturally with containers.


---

Current Skill Level Achieved

You now know:

Docker images

Containers

Dockerfile

Port mapping

Environment variables

Container isolation

FastAPI Dockerization

Docker cleanup

Docker debugging basics


This is already strong beginner Docker knowledge.


---

Next Topics Later

Future Docker topics:

Docker Compose

Multi-container apps

PostgreSQL container

React container

Docker volumes

Docker networking

Deployment on VPS/cloud
