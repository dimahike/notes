```Docker
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

Explaine:

```Dockerfile
FROM node:14
```
- **Explanation**: This line specifies the base image to use for your Docker image. In this case, it uses the official Node.js image with version 14. The base image contains a minimal operating system with Node.js pre-installed, which is necessary to run your Node.js application.

```Dockerfile
WORKDIR /app
```
- **Explanation**: This line sets the working directory inside the container to `/app`. Any subsequent commands in the `Dockerfile` will be executed in this directory. This helps organize the filesystem within the container.

```Dockerfile
COPY package.json .
```
- **Explanation**: This line copies the `package.json` file from your local machine to the working directory in the container. The `.` at the end represents the current directory inside the container, which is `/app` because of the previous `WORKDIR` command.

```Dockerfile
RUN npm install
```
- **Explanation**: This line runs the `npm install` command inside the container, which installs the dependencies listed in `package.json`. By copying `package.json` and running `npm install` before copying the rest of the application files, Docker can cache this step, making subsequent builds faster if dependencies haven't changed.

```Dockerfile
COPY . .
```

- **Explanation**: This line copies all files from your local machine's current directory to the working directory in the container. The first `.` represents the current directory on your local machine, and the second `.` represents the current directory inside the container (`/app`).

```Dockerfile
ARG DEFAULT_PORT=80
```

- **Explanation**: This line sets the build arguments. We can change arguments when we build an image with the `--build-arg DEFAULT_PORT=8000` flag

```Dockerfile
ENV PORT 80

EXPOSE $PORT
```
- **Explanation**: This line sets environment variables that we can use in the project, such as `process.env.PORT`.

```Dockerfile
EXPOSE 3000
```
- **Explanation**: This line documents that the container listens on port 3000 at runtime. While this doesn't actually publish the port, it is a way to specify which port your application will use. When running the container, you still need to map this port to your host machine using the `-p` flag.

```Dockerfile
CMD [ "node", "app.mjs" ]
```
- **Explanation**: This line specifies the command to run when the container starts. In this case, it runs `node app.mjs`, which starts your Node.js application. The `CMD` instruction allows you to provide defaults for an executing container, including specifying the executable to run.


---
### Info:
- **Layers**: Every layer executes from top to bottom. If there are any changes, the cache will not be used for the subsequent layers.
