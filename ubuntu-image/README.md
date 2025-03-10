# Running Ubuntu in Docker: A Step-by-Step Guide

This document provides a step-by-step guide to pulling an Ubuntu image from Docker Hub and using it in an interactive shell.

## Prerequisites

- Ensure you have Docker installed on your system.
- Verify Docker is running by executing:

  ```bash
  docker --version
  ```

  If Docker is not installed, follow the instructions on the [Docker website](https://docs.docker.com/get-docker/) to install it.

## Step 1: Pull the Ubuntu Image

Open a terminal and execute the following command to pull the latest Ubuntu image from Docker Hub:

```bash
docker pull ubuntu
```

**Explanation**

- This command will download the Ubuntu image from Docker Hub and store it locally on your system.
- If no tag is specified, Docker will pull the latest version of the Ubuntu image.
- The output confirms the download of the image and provides the image digest.

## Step 2: Run the Ubuntu Container

Once the image is downloaded, you can run an interactive shell by executing the following command to start a container from the Ubuntu image :

```bash
docker run -it ubuntu
```

**Explanation**

- docker run: Starts a new container.

- -it:

  -i (interactive mode): Keeps the container’s standard input open.
  -t (TTY mode): Allocates a pseudo-terminal.

- ubuntu: Specifies the image to use (latest Ubuntu image).
- The `-it` flag stands for "interactive terminal."
- The output displays the container ID and the Ubuntu shell prompt. example:

```bash
root@2a0866044c78:/#
```

## Step 3: Explore the Ubuntu Environment

You can now interact with the Ubuntu environment within the container. You can run commands, navigate through directories, and perform other tasks as you would on a regular Ubuntu system.

For example, you can list the files in the current directory by running:

```bash
ls
```

Or, you can check the system's version by running:

```bash

cat /etc/os-release
```

## Step 4: Exit the Container

When you are done exploring the Ubuntu environment, you can exit the container by pressing `Ctrl + D` or by typing `exit` in the terminal.

```bash
exit
```

This will stop the container and return you to the host system.

## Conclusion

You have successfully pulled an Ubuntu image from Docker Hub and used it in an interactive shell. This guide provides a basic introduction to running Ubuntu in Docker. You can now explore more advanced features and customization options to suit your needs.
