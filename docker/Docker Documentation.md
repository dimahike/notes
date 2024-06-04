# Docker Documentation

## Introduction to Docker

Docker is a platform designed to make it easier to create, deploy, and run applications by using containers. Containers allow developers to package an application with all parts it needs, such as libraries and other dependencies, and ship it all out as one package.

## Key Concepts

### Images

Images are read-only templates used to create containers. They contain the application code, libraries, dependencies, tools, and other files required to run the application. An image is built using a Dockerfile, which contains a set of instructions to create the image layer by layer. Each instruction in the Dockerfile creates a new layer in the image.

### Containers

Containers are runnable instances of images. They include everything needed to run an application: the code, a runtime, libraries, environment variables, and configuration files. Containers are isolated from each other and the host system, ensuring consistent execution regardless of where they are deployed.

### Data Persistence

By default, containers have a writable layer that stores data only as long as the container is running. For persistent data, Docker provides volumes and bind mounts.

## Detailed Concepts

### Images vs Containers

- **Images**: Templates for containers, read-only, and layer-based. They do not run themselves but are used to create containers.
- **Containers**: Instances of images that are created and started to run applications. They have a writable layer on top of the image layers.

### Volumes and Bind Mounts

- **Volumes**: Managed by Docker and used to persist data generated and used by Docker containers. Volumes are stored in a part of the host filesystem which is managed by Docker.
  - **Anonymous Volumes**: Created automatically and removed when the container is removed.
  - **Named Volumes**: Created with a specific name and persist even after the container is removed. They can be shared among multiple containers.
- **Bind Mounts**: You control the exact mountpoint on the host. They are used for sharing data between the host and the container, useful during development.

### Managing Data with Volumes

Volumes provide several advantages over bind mounts:
- Easy to back up or migrate data.
- Volumes are managed entirely by Docker, making them safer and more portable.
- Volumes can be shared between containers.

#### Creating and Using Volumes

- **Anonymous Volume**: `docker run -v /path/in/container IMAGE`
- **Named Volume**: `docker run -v volume_name:/path/in/container IMAGE`
- **Bind Mount**: `docker run -v /path/on/host:/path/in/container IMAGE`

#### Commands for Volumes

- List volumes: `docker volume ls`
- Create a volume: `docker volume create volume_name`
- Remove a volume: `docker volume rm volume_name`
- Remove all unused volumes: `docker volume prune`

### Key Docker Commands

- Build an image: `docker build -t image_name .`
- Run a container: `docker run -d --name container_name image_name`
- List running containers: `docker ps`
- List all containers: `docker ps -a`
- Stop a container: `docker stop container_name`
- Remove a container: `docker rm container_name`
- Push an image to a registry: `docker push image_name`
- Pull an image from a registry: `docker pull image_name`

### Docker with Windows and WSL2

When using Docker with Windows Subsystem for Linux 2 (WSL2), there are specific considerations:
- File change events might not propagate to the container. This can be worked around by modifying the starting script or using environment variables to force polling.
- You can mount Windows paths into WSL2 to ensure file changes are forwarded into the container.
- Using WSL2 as the default terminal and performing all operations inside the Linux environment can eliminate many issues.

## Best Practices

- Use volumes for persistent data to avoid data loss when containers are removed.
- Prefer named volumes for data that needs to persist beyond the container lifecycle.
- Use bind mounts for sharing data between the host and the container, especially during development.
- Regularly prune unused volumes to free up space.

By understanding these core concepts and utilizing Docker’s powerful features, you can streamline the development, deployment, and management of your applications, ensuring consistency across different environments and simplifying the handling of dependencies and configuration.

## Host Docker Internal

Connect to your host machine

```url
host.docker.internal
```
