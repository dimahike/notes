# [Utility Containers and Linux](https://www.udemy.com/course/docker-kubernetes-the-practical-guide/learn/lecture/23074458#questions/12977214/:~:text=https%3A//www.udemy.com/course/docker%2Dkubernetes%2Dthe%2Dpractical%2Dguide/learn/%23questions/12977214/)

On the Linux system, the Utility Container idea doesn't quite work as you describe it. In Linux, by default, Docker runs as the "Root" user. When we do a lot of the things that you are advocating for with Utility Containers, the files that get written to the Bind Mount have ownership and permissions of the Linux Root user. (On macOS and Windows 10, since Docker is being used from within a VM, the user mappings all happen automatically due to NFS mounts.)

For example, on Linux, if I do the following (as you described in the course):

### Dockerfile
```Dockerfile
FROM node:14-slim
WORKDIR /app
```

### Commands
```sh
$ docker build -t node-util:perm .
$ docker run -it --rm -v $(pwd):/app node-util:perm npm init
$ ls -la
```

### Output
```
total 16
drwxr-xr-x  3 scott scott 4096 Oct 31 16:16 ./
drwxr-xr-x 12 scott scott 4096 Oct 31 16:14 ../
drwxr-xr-x  7 scott scott 4096 Oct 31 16:14 .git/
-rw-r--r--  1 root  root   202 Oct 31 16:16 package.json
```

You'll see that the ownership and permissions for the `package.json` file are "root". Regardless of the file being written to the Bind Mounted volume from commands emanating from within the docker container, e.g., "npm install", all come out with "Root" ownership.

## Solution 1: Use Predefined "node" User (if you're lucky)

There is a lot of discussion in the Docker community (DevOps) about security around running Docker as a non-privileged user (which might be a good topic for you to cover as a video lecture - or maybe you have; I haven't completed the course yet). The Official Node.js Docker Container provides such a user called "node".

### Dockerfile
```Dockerfile
FROM node:14-slim
USER node
WORKDIR /app
```

Luckily enough, on my local Linux system, my "scott" uid:gid is also 1000:1000, so this maps nicely to the "node" user defined within the Official Node Docker Image.

If I rebuild my Utility Container in the normal way and re-run "npm init", the ownership of the `package.json` file is written as if "scott" wrote the file.

### Commands
```sh
$ ls -la
```

### Output
```
total 12
drwxr-xr-x  2 scott scott 4096 Oct 31 16:23 ./
drwxr-xr-x 13 scott scott 4096 Oct 31 16:23 ../
-rw-r--r--  1 scott scott 204 Oct 31 16:23 package.json
```

## Solution 2: Remove the Predefined "node" User and Add Yourself as the User

If the Linux user you are running as is not lucky to be mapped to 1000:1000, you can modify the Utility Container Dockerfile to remove the predefined "node" user and add yourself as the user that the container will run as:

### Dockerfile
```Dockerfile
FROM node:14-slim
RUN userdel -r node

ARG USER_ID
ARG GROUP_ID

RUN addgroup --gid $GROUP_ID user
RUN adduser --disabled-password --gecos '' --uid $USER_ID --gid $GROUP_ID user

USER user
WORKDIR /app
```

Build the Docker image using the following (which also gives you a nice use of ARG):

### Commands
```sh
$ docker build -t node-util:cliuser --build-arg USER_ID=$(id -u) --build-arg GROUP_ID=$(id -g) .
$ docker run -it --rm -v $(pwd):/app node-util:cliuser npm init
$ ls -la
```

### Output
```
total 12
drwxr-xr-x  2 scott scott 4096 Oct 31 16:54 ./
drwxr-xr-x 13 scott scott 4096 Oct 31 16:23 ../
-rw-r--r--  1 scott scott 202 Oct 31 16:54 package.json
```

Reference to Solution 2 above: [vsupalov.com/docker-shared-permissions](https://vsupalov.com/docker-shared-permissions/)

Keep in mind that this image will not be portable, but for the purpose of the Utility Containers like this, I don't think this is an issue at all for these "Utility Containers".