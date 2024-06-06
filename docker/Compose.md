## Docker Compose Setup

### 1. Create a `docker-compose.yml` file in the root folder

```yaml
services:
  mongodb:
    image: "mongo"
    volumes:
      - "data:/data/db"
    env_file:
      - ./env/mongo.env

  backend:
    build: ./backend
    ports:
      - "80:80"
    volumes:
      - "goals_logs:/app/logs"
      - "./backend:/app"
      - "/app/node_modules"
    env_file:
      - "./env/backend.env"
    depends_on:
      - "mongodb"

  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    volumes:
      - "./frontend/src:/app/src:ro"
      - "/app/node_modules"
    depends_on:
      - "backend"

volumes:
  data:
  goals_logs:
```

### Explanation

- **`services:`** The services to run.
- **`mongodb:`** The name of the MongoDB service.
    - **`image: "mongo"`** The image to run.
    - **`volumes:`** The volumes to mount.
        - **`data:/data/db`** The volume to mount.
    - **`env_file:`** The file with the environment variables.
        - **`./env/mongo.env`** The path to the file with the environment variables.

- **`backend:`** The name of the backend service.
    - **`build: ./backend`** The build context.
    - **`ports:`** The ports to expose.
        - **`"80:80"`** Maps port 80 of the host to port 80 of the container.
    - **`volumes:`** The volumes to mount.
        - **`goals_logs:/app/logs`** A named volume for logs.
        - **`./backend:/app`** Bind mount for live code updates.
        - **`/app/node_modules`** Anonymous volume for node modules.
    - **`env_file:`** The file with the environment variables.
        - **`./env/backend.env`** The path to the file with the environment variables.
    - **`depends_on:`** Dependencies.
        - **`mongodb`** The backend depends on MongoDB.

- **`frontend:`** The name of the frontend service.
    - **`build: ./frontend`** The build context.
    - **`ports:`** The ports to expose.
        - **`"3000:3000"`** Maps port 3000 of the host to port 3000 of the container.
    - **`volumes:`** The volumes to mount.
        - **`./frontend/src:/app/src:ro`** Bind mount in read-only mode for source files.
        - **`/app/node_modules`** Anonymous volume for node modules.
    - **`depends_on:`** Dependencies.
        - **`backend`** The frontend depends on the backend.

- **`volumes:`** Named volumes declaration.
    - **`data:`** Named volume for MongoDB data.
    - **`goals_logs:`** Named volume for backend logs.

### Commands

1. **Start the containers:**
    ```bash
    docker-compose up
    ```
    - `-d` Run in detached mode.
    - `--build` Build the images before starting the containers.

   Alternatively, build the images separately:
    ```bash
    docker-compose build
    ```

2. **Stop the containers and remove them:**
    ```bash
    docker-compose down
    ```
    - `-v` Remove the volumes as well.

3. **Build the images:**
    ```bash
    docker-compose build
    ```

### Notes

- **Using the latest Docker Compose version:** Specifying the version is no longer necessary as Docker Compose uses the latest version by default.
- **Volume Mounting:**
    - Use `-` for a single value.
    - Use `:` for multiple values.

- **Network Configuration:** Docker Compose creates a network by default. You can specify a custom network if needed:
    ```yaml
    networks:
      - goals-net
    ```

Feel free to customize the setup as per your project requirements.