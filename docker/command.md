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
docker run -p 8000:3000 b275b9b05010
```
- `-it`: Runs an `-i` interactive session, `-t` allowing interaction with the Node terminal. For example, if your code requires user input:
```py
min_number = int(input('Please enter the min number: '))
```
- `--rm`: Automatically removes the container after it exits.
- `--name custom_container_name`: Allows you to specify a custom name for the container.

- `8000:3000`: Maps local port 8000 to container port 3000.
- `b275b9b05010`: Image ID. You can use the first 10 characters of the full ID (e.g., `b275b9b05010af5fd9b1c2530fca9b59b13932ec868e6e2e26db933c07b59d86`). This ID can be found in Docker Desktop.

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
- `vigilant_vaughan`: The name of the running container (found in the last column of `docker ps`). It may take a few seconds to stop.

### 5. Run a Node.js Image
Run the Node.js image:
```sh
docker run node
```
If the image is not available locally, it will be downloaded from Docker Hub.

### 6. Restart Container
To rerun an already created container, use the following command:
```sh
docker start CONTAINER
```

### 7. Detach Mode
Create and run a new container in detach mode, which means we won't see a terminal for the running image.
```sh
docker run -p 8000:80 -d CONTAINER
```
To attach to the terminal of a running image, use:
```sh
docker attach CONTAINER
```
By default, running a container without `-d` starts it in "attached mode".
If you started a container in detached mode (with `-d`), you can attach to it later without restarting the container.

To see the logs of a running container:
```sh
docker logs CONTAINER
```
To continuously stream the logs, use the `-f` flag:
```sh
docker logs -f CONTAINER
```

### 8. Remove Container
To remove one or more containers, use:
```sh
docker rm CONTAINER CONTAINER CONTAINER ...
```
You must stop a container before removing it.

### 9. Remove Image
To remove one or more images, use:
```sh
docker rmi IMAGE IMAGE IMAGE ...
```
You must remove all containers associated with an image before removing the image itself.

### 10. Inspect Image
To see all the information about an image, use:
```sh
docker image inspect IMAGE
```

### 11. Copy Files to Container
Usually used for testing. To copy files from the local system to a container:
```sh
docker cp /local_folder/. CONTAINER:path_to_container_folder
```
The `.` copies all files from the local folder.

To copy files from a container to the local system:
```sh
docker cp CONTAINER:path_to_container_folder path_to_local_folder
```
