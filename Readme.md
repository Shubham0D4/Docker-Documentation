# Dockerfile and Docker Commands Reference

## Dockerfile Content

1. **FROM** – Keyword used to get required base image with necessary packages.
   - Example: `FROM node:20` – This uses the 20th version of Node.js.

2. **WORKDIR** – Used to create and set a working directory inside the container where the project will run.

3. **COPY . .** – Copies files into the container.
   - First `.` refers to all files in the current host directory.
   - Second `.` refers to the destination, i.e., the WORKDIR in the container.

4. **RUN** – Used to execute commands during the image build (excluding startup commands).
   - It is mainly used to install packages or dependencies.
   - Example: `RUN npm install`

5. **CMD []** – Used to define the default startup command when the container runs.
   - Each word of the command should be passed as a separate element in the array.
   - Example: `CMD ["npm", "start"]`

---

## Docker Commands

1. To start Docker engine:
   - `systemctl start docker-engine`

   **Note**: Commands below should be executed with `sudo` access.

2. To create a Docker image:
   - `docker build .`
   - `docker build -t image-name:version .` → Tags the image with a name and version, so image ID is not required later.

3. To get a list of created images:
   - `docker image ls`

4. To run a Docker image (you can replace `image-id` with the image name):
   - `docker run image-id`
   - `docker run -p machine-port:container-port image-id` → Maps container port to host machine port.
   - `docker run -d -p machine-port:container-port image-id` → Runs container in detached mode (background).
   - `docker run -d --rm -p machine-port:container-port image-id` → Detached mode and auto-remove when stopped.
   - `docker run -d --rm --name "custom-name" -p machine-port:container-port image-id` → Assigns a custom name.
   - `docker run -it image-id/name` → Interactive mode for user input.

5. To list active containers:
   - `docker ps`

   To list all containers (including stopped ones):
   - `docker ps -a`

6. To stop a running container:
   - `docker stop container-name`
   - *(Use the name from `docker ps`)*

7. To delete an image:
   - `docker rmi image-id`
   - `docker rmi image-name:version`

8. To update an image:
   - `docker build -t image-name:next-version .` → Creates a new version with a new image ID.

9. To get images from Docker Hub (https://hub.docker.com):
   - `docker pull image-name` → Pulls the image.
   - `docker run image-name` → Pulls and runs the image.

10. To share Docker images:
    - Step 1 – Login:
      - `docker login`
    - Step 2 – Push image to Docker Hub:
      - `docker push image-name:version`

11. To rename a Docker image:
    - `docker tag old-image-name:version new-image-name:version`

12. To create a Docker volume (persists data outside the container):
    - `docker run -v volume-name:/directory_path_same_as_workdir image-id`
    - Example: `docker run -v myvol:/testapp/`

13. To view list of Docker volumes:
    - `docker volume ls`

14. **Mount Binding** – Allows file editing outside the container, reflecting changes inside.
    - `docker run -v /local/path:/workdir/path/to/file --rm image-id`
    - Add in Dockerfile before that:
      - `COPY ./to_be_edited_file ./`

15. To inspect container details:
    - `docker inspect container-name`

16. To restart a stopped (but not removed) container:
    - `docker start container-id/name`

17. To create a Docker network:
    - `docker network create network-name`

18. To add a container to a network:
    - `docker run -d --rm --network network-name image-id/name`

---

## Docker Compose

- Docker Compose allows managing multi-container applications using a YAML file.

1. `docker compose up` → Starts services defined in `docker-compose.yml`
2. `docker compose up -d` → Runs services in detached mode
3. `docker compose down` → Stops and removes all services

**Inside `docker-compose.yml`:**
```yaml
services:
  your_service_name:
    image: image-name:version
    environment:
      - KEY1=value1
      - KEY2=value2
    container_name: "your_container_name"
```

---

## Use Cases

### 1. Container-to-Container Communication

- **MySQL Container**
  - To run:
    ```bash
    docker run -d \
      --env MYSQL_ROOT_PASSWORD="your_password" \
      --env MYSQL_DATABASE="database_name" \
      mysql:tag
    ```
  - To connect from another container in same network:
    - Use the MySQL container name as the hostname.

### 2. Container to Local Server Communication

- **MySQL Local Server + Custom Container**
  - Use host as: `host.docker.internal`
  - Tells the container to connect to the host machine where Docker is running.

