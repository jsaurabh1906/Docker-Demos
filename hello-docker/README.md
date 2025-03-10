# Docker Demo App for Node.js

This demo app shows how to containerize a simple Node.js application using Docker. The app consists of a single JavaScript file (`hello.js`) that prints a message to the console, and a `Dockerfile` that builds an image to run the application.

## Project Structure

├── Dockerfile
└── hello.js

- **hello.js:** A Node.js script that logs a greeting message.
- **Dockerfile:** A file that defines the Docker image, specifying the base image, working directory, file copy, and the command to run the app.

## File Explanations

### hello.js

This file contains the following code:

```js
console.log("Hello Docker!!!");
```

It simply logs the message "Hello Docker!!!" to the console.

### Dockerfile

The `Dockerfile` is used to create a Docker image. Here's a breakdown of its contents:

```Dockerfile
# Use the node:23-alpine3.20 image as the base image
FROM node:23-alpine3.20

# Set the working directory to /app
WORKDIR /app

# Copy all files from the current directory to the /app directory in the container
COPY . .

# Set the command to run when the container starts
CMD node hello.js

```

#### Step-by-step breakdown:

**FROM node:23-alpine3.20:**

- Uses a lightweight Alpine-based Node.js 23 image as the base. This image includes Node.js and runs on Alpine Linux for a smaller footprint.

**WORKDIR /app:**

- Sets /app as the working directory in the container. All subsequent commands will be run from this directory.

**COPY . .:**

- Copies all files from your host's current directory to the /app directory inside the container.

**CMD node hello.js:**

- Defines the command that will run when the container starts. In this case, it runs the Node.js script hello.js.

### How to Build and Run the Docker Image

1. Open a terminal or command prompt.
2. Navigate to the directory containing the `hello.js` and `Dockerfile` files.
3. Build the Docker image using the following command:

```bash
docker build -t hello-docker .
```

- `-t hello-docker` specifies the name of the image to be created.
- This command builds a Docker image with the tag `hello-docker` using the instructions in your Dockerfile.

4. Run the Docker container using the following command:

```bash
docker run hello-docker
#OR
docker run --rm hello-docker
```

- This command starts a new container from the `hello-docker` image and runs the `hello.js` script inside it.
- The --rm flag automatically removes the container when it exits.

You should see the output "Hello Docker!!!" printed to the terminal.
