## Docker Commands for a Node.js App

### 1. Build the Docker Image
Build the image after setting up the Docker configuration.
```sh
docker build .
```

**Example for a Node.js app** ([Docker config explanation](https://github.com/dimahike/notes/blob/main/docker/Docker%20config.md)):
```Dockerfile
FROM node:14

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

EXPOSE 3000

CMD [ "node", "app.mjs" ]
```

### 2. Run the Docker Container
Run the container with the following command:
```sh
docker run -p 8000:3000 b275b9b05010 [folder_name]
```
- *folder_name*: If needed. Since we built `.` (current directory), we don't need to add `folder_name`.
- *3000:3000*: Opens local port 8000 from the container port 3000.
- *b275b9b05010*: Image ID. You can copy the first 10 symbols from the full ID (e.g., `b275b9b05010af5fd9b1c2530fca9b59b13932ec868e6e2e26db933c07b59d86`). You can also find it in Docker Desktop.

### 3. Display Running Containers
Use the following command to display your running containers:
```sh
docker ps
```
- `docker ps -a`: Shows all containers created by Docker.

### 4. Stop a Running Container
Stop a container using its name:
```sh
docker stop vigilant_vaughan
```
- *vigilant_vaughan*: The name of the running image (found in the last column of `docker ps`). It may take a few seconds to stop.

### 5. Run a Node.js Image
Run the Node.js image:
```sh
docker run node
```
If the image is not available locally, it will be downloaded from Docker Hub.
- `docker run -it node`: Runs an interactive session, allowing interaction with the Node terminal.

---
### 6. Restart Container

To rerun an already created container, use the following command:

```sh
docker start [container_name/id]
```

---
### 7. Detach Mode

Create and run a new container in detach mode, which means we won't see a terminal for the running image.

```sh
docker run -p 8000:80 -d [container_name/id]
```

if we wanna see a terminal for the running image then we can run the below command

```sh
docker attach [container_name/id]
```
Also, we can see just logs with this command

```sh
docker logs [container_name/id]
```
If we add the flag `-f` then we will see the all logs and start attach mode

```sh
docker logs -f [container_name/id]
```

