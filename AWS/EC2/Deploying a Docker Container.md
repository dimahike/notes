## Deploying a Docker Container

### 1. Build and Test the Docker Container

Build the Docker image and run the container locally to test it:

```shell
docker build -t node-dev-example .  
docker run -d --rm --name node-dev -p 80:80 node-dev-example
```

**Note**: If you are building on an M1 architecture, build for the target architecture (e.g., Amazon Linux):

```shell
docker buildx build --platform linux/amd64 -t node-dev-example .
```

- `docker buildx build`: Builds Docker images using `buildx`, an extended version of Docker's build tool.
- `--platform linux/amd64`: Specifies the target platform (Linux OS and AMD64 architecture).

Open `http://localhost` in your browser to verify the home page.

### 2. Set Up Remote Service (AWS)

Log in to your remote service (e.g., AWS) and navigate to the EC2 service.

### 3. Create and Configure an EC2 Instance

Create a new EC2 instance by following these steps:

1. Go to the EC2 Dashboard.
2. Click on "Launch Instance".
3. Choose an Amazon Machine Image (AMI) (e.g., Amazon Linux 2).
4. Select the instance type (e.g., t2.micro for free tier).
5. Configure the instance details as needed.
6. Add storage (default settings are usually sufficient).
7. Add tags (optional).
8. Configure security group (ensure SSH and HTTP access).
9. Review and launch the instance.
10. Download the key pair (.pem file) for SSH access.

### 4. Connect to the EC2 Instance Using SSH

1. Open an SSH client (terminal).
2. Navigate to the directory where your key is stored (e.g., `~/.ssh` folder).

    ```shell
    cd ~/.ssh
    ```

3. Ensure your key is not publicly viewable.

    ```shell
    chmod 400 "your-key.pem"
    ```

4. Connect to the instance using SSH (`ec2-user` for Amazon Linux).

    ```shell
    ssh -i "your-key.pem" ec2-user@your-ec2-public-ip
    ```

### 5. Install Docker on the EC2 Instance

1. Update installed packages and package cache.

    ```shell
    sudo yum update -y
    ```

2. Install Docker.

    ```shell
    sudo yum -y install docker
    ```

3. Start the Docker service.

    ```shell
    sudo service docker start
    ```

4. Optionally, add the `ec2-user` to the `docker` group to run Docker commands without `sudo`.

    ```shell
    sudo usermod -a -G docker ec2-user
    ```

5. Log out and back in to apply the group changes, or use the following command:

    ```shell
    sudo newgrp - docker
    ```

6. Enable Docker to start on boot.

    ```shell
    sudo systemctl enable docker
    ```

### 6. Create a Repository on Docker Hub

1. Log in to Docker Hub.
2. Create a new repository (e.g., `dimadeveloper2020/node-example-1`).

### 7. Push the Docker Image to Docker Hub

1. Rename the image on your local machine.

    ```shell
    docker tag node-dev-example dimadeveloper2020/node-example-1
    ```

2. Push the image to the Docker Hub repository.

    ```shell
    docker push dimadeveloper2020/node-example-1
    ```

### 8. Pull the Docker Image on the EC2 Instance

Pull the Docker image from Docker Hub to your EC2 instance:

```shell
docker pull dimadeveloper2020/node-example-1
```

If an updated image is pushed to Docker Hub, pull the updated image:

```shell
sudo docker pull dimadeveloper2020/node-example-1
```

### 9. Run the Docker Container on the EC2 Instance

Run the Docker container on your EC2 instance:

```shell
docker run -d --rm --name node-dev -p 80:80 dimadeveloper2020/node-example-1
```

### 10. Configure Security Groups for HTTP Access

Allow inbound traffic to the EC2 instance by configuring Security Groups with the appropriate inbound rules:

1. Navigate to the EC2 Dashboard.
2. Select "Security Groups" from the sidebar.
3. Find the security group associated with your EC2 instance.
4. Edit the inbound rules to allow HTTP (port 80) traffic from any IP.

### 11. Access the Application

Open the public IP address of your EC2 instance in your browser to access the application:

```shell
http://your-ec2-public-ip
```