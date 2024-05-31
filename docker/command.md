## Docker Commands for a Node.js App

### 1. Build the Docker Image
Build the image after setting up the Docker configuration.
```sh
docker build .
```
- `-t name:tag` add a name and tag for creating image
- `--build-arg DOCKER_ARGUMENT_NAME=VALUE` change doocker arguments when we build an image.
  
  ```Dockerfile
  ARG DOCKER_ARGUMENT_NAME=VALUE
  ```

**Example for a Node.js app** ([Docker config explanation](https://github.com/dimahike/notes/blob/main/docker/Docker%20config.md)):
```Dockerfile
FROM node:14

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

ARG DEFAULT_PORT=80

ENV PORT $DEFAULT_PORT

# or "EXPOSE 3000"
EXPOSE $PORT

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
- add volumes (store)
  - `-v IMAGE_PATH_DATA`: create the anonymous volume (remove volume if we remove a container) and can not be shared across containers

    Instead of the command You can add an anonymous volume in the `Dockerfile`

    ```Dockerfile
      VOLUME ["IMAGE_PATH_DATA"]
    ```
  - `-v VOLUME_NAME:/IMAGE_PATH_DATA`: Named Volume. It is persistent and can be shared across containers
  - `-v LOACAL_PATH_TO_CODE:/IMAGE_PATH_APP`: Bind Mount. Store on the host system (local machine) and can be shared across containers [Bind mount](#15-bind-mounts-managed-by-you)
    we can add option read only (`ro`) to the volume `-v LOACAL_PATH_TO_CODE:/IMAGE_PATH_APP:ro`    
- `-p 8000:3000`: Maps local port 8000 to container port 3000.
- Environment variables
  - `--env ENV_NAME=VALUE` (-e ENV_NAME=VALUE): Set Environment variables. multiple env-variables `-e ENV_NAME=VALUE -e ENV2_NAME=VALUE`
  - `--env-file ./.env`: use `.env` file from current path in the terminal.
  - We can set env-variables in the `Dockerfile`

    ```Dockerfile
    ENV PORT 80
    ```

    Also, we can use it in the docker with the `$` symbol

    ```Dockerfile
    ENV PORT 80
    EXPOSE $PORT
    ```
    
*Notes* `b275b9b05010`: Image ID. You can use the first 10 characters of the full ID (e.g., `b275b9b05010af5fd9b1c2530fca9b59b13932ec868e6e2e26db933c07b59d86`). This ID can be found in Docker Desktop.

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

To remove all containers, use:
```sh
docker container prune -f
```

### 9. Remove Image
To remove one or more images, use:
```sh
docker rmi IMAGE IMAGE IMAGE ...
```
You must remove all containers associated with an image before removing the image itself.

If you wanna remove all images then you can run this command:
```sh
docker image prune -a
```

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

### 12. Rename Image
```sh
docker tag IMAGE_NAME:IMAGE_TAG NEW_IMAGE_NAME:NEW_IMAGE_TAG 
```
Actually, we don't rename we create a renamed clone image

### 13. Sharing Image
- Docker Hub [Official Docker](https://hub.docker.com/)
1. We have to create a repository on the Docker Hub
2. Our shared image should have the name `DOCKER_HUB_USER_NAME/IMAGE_NAME(dimadeveloper2020/node-hello-world)`
```sh
docker push IMAGE_NAME
docker pull IMAGE_NAME
```
Everyone can pull if the image is public

- Private Register Hub
```sh
docker push HOST:NAME
docker pull HOST:NAME
```

### 14. Volume
```sh
docker volume COMMAND
```
- `ls`: List volumes
- `inspect VOLUME_NAME`: Display info
- `prune`: Remove unused local volumes
- `rm VOLUME_NAME`: Remove one or more volumes
- `create VOLUME_NAME`: Create a named volume

   We create a volume when we create container (`docker run ...`) with the `-v VOLUME_NAME:/PATH_FOLDER` flag

### 15. Bind Mounts (Managed by you)

When developing with Docker, you can use volume mounts to sync your local project directory with the container's file system. This allows you to work on your code locally while running it inside the container.

#### Basic Volume Mount Command

For macOS / Linux:
```sh
-v $(pwd):/app
```

For Windows:
```sh
-v "%cd%":/app
```

#### Example Command

Assuming your project directory is `/Users/dima/project_1`, the Docker run command would look like this:

```sh
docker run -v "/Users/dima/project_1:/app" <image_name>
```

This command mounts your local project directory (`/Users/dima/project_1`) to the container's `/app` directory. 

<img width="1614" alt="Снимок экрана 2024-05-26 в 17 36 33" src="https://github.com/dimahike/notes/assets/61499375/d18b31bf-8041-4121-87cb-53ffc4a4a109">
You have to have access for sharing

#### Preserving `node_modules`

If you want to avoid overwriting certain directories like `node_modules` after running `npm install`, you can create an anonymous volume for `node_modules`:

```sh
docker run -v "/Users/dima/project_1:/app" -v /app/node_modules <image_name>
```

This ensures that `node_modules` in the container won't be overwritten by the local directory and won't require reinstalling dependencies each time.

##### Notes:
- It is good to defined only read mode for the host volume and rest read/write data defined to the volumes
  `-v LOACAL_PATH_TO_CODE:/IMAGE_PATH_APP:ro`
- We have to rerun server if we do any changes on the local machine so we have to use something like `nodemon`
```package.json
...
  "scripts": {
    "start": "nodemon server.js"
  },
  "devDependencies": {
    "nodemon": "^2.0.4"
  }
...
```

Run nodemon script in the conainer
```Dockerfile
...
CMD [ "npm", "start"]
```

### 16. Docker ignore
We can add more "to-be-ignored" files and folders to your `.dockerignore` file.

```.dockerignore
node_modules
package-lock.json
.env
.git
.DS_Store
.idea
```

### 17. Request from container to host machine
Use `host.docker.internal` as an address.

#### For Example:

```js
mongoose.connect(
  'mongodb://host.docker.internal:27017/database_name',
);
```
*Notes*: **27017** is the default port

### 18. Request from container to container
 
#### IP Address

Use `NetworkSettings.IPAddress` as an address.

#### For Example:
```bash
docker container inspect mongodb  
// find `NetworkSettings.IPAddress` (172.17.0.2)
```

```js
mongoose.connect(
  'mongodb://172.17.0.2:27017/database_name',
);
```
*Notes*: **27017** is the default port

---
 
#### IP Docker Networks

We can create your network. Don't need it API

```bash
docker network create NETWORK_NAME

// connect with container
docker run --name CONTAINER_NAME_1 -p 3000:3000 --network NETWORK_NAME IMAGE_NAME_1
docker run --name CONTAINER_NAME_2 --network NETWORK_NAME IMAGE_NAME_2
```

We don't need to set a port for docker when we talk between containers. 

```js
mongoose.connect(
  'mongodb://CONTAINER_NAME_2:27017/database_name',
);
```

We have to set port `-p 3000:3000` when we address outside to the `CONTAINER_NAME_1`, but we can't address to the `CONTAINER_NAME_2`

```bash
curl --location 'http://localhost:3000/'
```



### 19. Network command

```sh
docker network COMMAND
```
-  `connect`     Connect a container to a network
-  `create`      Create a network
-  `disconnect`  Disconnect a container from a network
-  `inspect`     Display detailed information on one or more networks
-  `ls`          List networks
-  `prune`       Remove all unused networks
-  `rm`          Remove one or more networks

