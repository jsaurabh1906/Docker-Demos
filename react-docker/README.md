# Dockerizing a React Application with Vite

This document provides a comprehensive overview of how to containerize a React application using Docker. It includes the Dockerfile configuration, the commands used to build and run the container, and explanations of why certain commands are preferred over others.

---

## Dockerfile

Below is the Dockerfile used to create an image for the React app:

```dockerfile
# set the base image to create the image for react app
FROM node:23-alpine3.20

# create a user with permissions to run the app
# -S -> create a system user
# -G -> add the user to a group
# This is done to avoid running the app as root
# If the app is run as root, any vulnerability in the app can be exploited to gain access to the host system
# It's a good practice to run the app as a non-root user
RUN addgroup app && adduser -S -G app app

# set the user to run the app
USER app

# set the working directory to /app
WORKDIR /app

# copy package.json and package-lock.json to the working directory
# This is done before copying the rest of the files to take advantage of Docker’s cache
# If the package.json and package-lock.json files haven’t changed, Docker will use the cached dependencies
COPY package*.json ./

# sometimes the ownership of the files in the working directory is changed to root
# and thus the app can't access the files and throws an error -> EACCES: permission denied
# to avoid this, change the ownership of the files to the root user
USER root

# change the ownership of the /app directory to the app user
# chown -R <user>:<group> <directory>
# chown command changes the user and/or group ownership of for given file.
RUN chown -R app:app .

# change the user back to the app user
USER app

# install dependencies
RUN npm install

# copy the rest of the files to the working directory
COPY . .

# expose port 5173 to tell Docker that the container listens on the specified network ports at runtime
EXPOSE 5173

# command to run the app
CMD npm run dev
```

### Explanation

1. **Base Image**: The base image is set to `node:23-alpine3.20`, which is a lightweight version of Node.js. Alpine Linux is known for its small size and security features.

2. **User Creation**: A user named `app` is created with the `adduser` command. This user is added to the `app` group with the `addgroup` command. Running the app as a non-root user is a security best practice.

3. **Working Directory**: The working directory is set to `/app` using the `WORKDIR` command. This is the directory where the app’s files will be copied and installed.

4. **Copying Package Files**: The `package.json` and `package-lock.json` files are copied to the working directory. This is done before copying the rest of the files to take advantage of Docker’s cache. If the package files haven’t changed, Docker will use the cached dependencies, which speeds up the build process.

5. **Ownership Change**: The ownership of the files in the working directory is changed to the `app` user using the `chown` command. This is done to avoid permission errors when the app tries to access the files.

6. **Dependencies Installation**: The `npm install` command is run to install the app’s dependencies.

7. **Copying Files**: The rest of the app’s files are copied to the working directory.

8. **Port Exposure**: The `EXPOSE` command is used to tell Docker that the container listens on port 5173 at runtime. This is useful for networking purposes.

9. **Command to Run the App**: The `CMD` command is used to run the app when the container starts. In this case, it runs the `npm run dev` command, which starts the React development server.

---

### Key Points

- Non-Root User (app): Minimizes security risks.
- WORKDIR /app: Sets /app as the working directory.
- Caching Dependencies: Copying package\*.json before other files ensures Docker’s build cache can be leveraged.
- File Ownership Fix: Ensures the app user owns all files and can run the app without permission errors.
- Port 5173 Exposed: The default Vite dev server port is exposed.

## Commands

### Build the Docker Image

To build the Docker image, use the following command in the same directory as the Dockerfile:

```bash
docker build -t react-docker .
```

- -t react-docker: Assigns the name (tag) react-docker to the newly built image.
- .: Specifies the current directory as the build context.

### Run the Docker Container

Below are multiple ways to run the Docker container, each with different trade-offs.

1. Basic Run Command

```bash
docker run react-docker
```

#### What Happens Here:

- The container starts and executes npm run dev inside the container.
- However, no port mapping is specified. You cannot access the app from your host at localhost:5173 because the container’s port is not exposed to your host machine.

#### Why its NOT IDEAL:

- You can’t open the app in your browser from the host because port 5173 is not published.

2. Publishing a Port

```bash
docker run -p 5173:5173 react-docker
```

#### What This Fixes:

- -p 5173:5173 maps the container’s port 5173 to your host’s port 5173, allowing you to access the app via http://localhost:5173.

#### Remaining Issue:

If you make changes to your React code on your host machine, the container won’t automatically reflect those changes unless you rebuild the image or manually copy files again. There’s no volume mounting yet.

3. Volume Mounting for Live Code Updates

```bash
docker run -p 5173:5173 -v "$(pwd):/app" -v /app/node_modules react-docker
```

#### How It Helps:

- -v "$(pwd):/app" mounts the current directory on your host to the /app directory inside the container. This allows the container to see the latest changes you make to your React code.
- -v /app/node_modules mounts the node_modules directory inside the container to the same directory on your host. This ensures that the container uses the latest dependencies you install.

#### Potential Issues (mainly on Windows):

- On Windows, file system events might not always propagate correctly, requiring additional configuration (e.g., polling with CHOKIDAR_USEPOLLING=true).
- The container still needs to rebuild the image to pick up new dependencies.
- If you forget to separate node_modules, you can run into permission conflicts or mismatches between host and container OS.

## Additional Docker Commands

### List Running Containers

```bash
docker ps
```

- It shows the currently running containers.

### List All Containers (including stopped ones)

```bash
docker ps -a
```

- It shows all containers, including those that have been stopped.

### Stop a Container

```bash
docker stop <container_id_or_name>
```

- Gracefully stops the container with the specified ID or Name.
- Example :

```bash
docker stop 234
```

- where 234 is the first few characters of the container ID.

### Remove Stopped Containers

```bash
docker rm <container_id_or_name>

```

- Removes a container that is not running.
- Example:

```bash
docker rm aa7
```

### Prune Containers

```bash
docker container prune
```

- Removes all stopped containers.
- Warning: This action is irreversible.

## Publishing Docker Image

Once you have built your Docker image, you can publish it to Docker Hub so others (or your deployment systems) can pull and run it. Follow these steps:

1. **Login to Your Docker Account:**

   ```bash
   docker login
   ```

#### Explanation

- This command prompts you to enter your Docker Hub username and password.
- It authenticates your local Docker CLI with Docker Hub, so you can push and pull images from your account.
- If you are already logged in, Docker will use the stored credentials.

2. **Tag your image with your dockerhub username:**

```bash
docker tag react-docker <username>/react-docker
```

#### Explanation:

- The docker tag command assigns a new tag to your existing image.
- Replace <username> with your Docker Hub username.
- This creates a new tag in the format <username>/react-docker which is required for pushing the image to your Docker Hub repository.
- Tagging is important because Docker Hub uses the repository name (which includes your username) to organize images

3. **Push the image to your dockerhub account:**

```bash
docker push <username>/react-docker
```

#### Explanation:

- This command uploads your tagged image to your Docker Hub repository.
- It makes your image publicly (or privately, depending on your repository settings) available for others to download.
- The image will be stored under the repository name <username>/react-docker on Docker Hub.
- Once pushed, you can pull the image on any system by running docker pull <username>/react-docker.

## Conclusion

- **docker build -t react-docker .**: Builds the Docker image from the Dockerfile and assigns it the tag "react-docker".
- **docker run react-docker**: Runs the container using the image, but without publishing any ports (so the app isn't accessible from the host).
- **docker run -p 5173:5173 react-docker**: Runs the container and maps port 5173 from the container to port 5173 on the host, enabling access to the app.
- **docker run -p 5173:5173 -v "$(pwd):/app" -v /app/node_modules react-docker**: Runs the container with port mapping and volume mounting, allowing live code updates without rebuilding the image.

- **docker login**: Authenticates your Docker CLI with Docker Hub, enabling you to push and pull images.
- **docker tag react-docker <username>/react-docker**: Tags your image with your Docker Hub username, which is required for publishing it to your repository.
- **docker push <username>/react-docker**: Pushes the tagged image to Docker Hub, making it available for use on any system or by other users.
