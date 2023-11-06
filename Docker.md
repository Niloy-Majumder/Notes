Docker is a platform that enables developers to package and deploy applications along with their dependencies in isolated and portable containers, enhancing consistency across different environments. These containers offer efficient resource utilisation and simplified deployment processes.

---

In the context of Docker:

1. **Images:** Images are read-only snapshots that contain a complete package of an application, its code, runtime, libraries, and dependencies. They serve as the blueprint for creating containers and are stored in a registry, allowing consistent replication and distribution of applications.
2. **Containers:** Containers are lightweight, executable instances created from Docker images. They encapsulate an application and its environment, providing isolation from the host system and other containers. Containers can be started, stopped, and managed independently, making them ideal for consistent and portable application deployment across various environments.

## Parent Images

**Parent Image:** A parent image, also known as a base image, is a starting point for creating Docker images. It serves as the foundation by providing a pre-configured environment, runtime, and essential software components. Developers build upon parent images to add their application code and specific dependencies, resulting in a custom image tailored to their needs.

![Parent Images](Parent%20Image.png)

## DockerFile

A Dockerfile is a plain text configuration file used to define the instructions and steps required to build a Docker image. It contains a series of commands that specify how to assemble the image, what base image to use, which files to include, and how to configure the environment. Dockerfiles provide a way to automate and document the process of creating custom Docker images for specific applications.

Here's a simple example of how to create and use a Dockerfile:

1. **Create a Dockerfile:** Open a text editor and create a file named "Dockerfile" (without any file extension) in your project directory.
2. **Write Dockerfile Instructions:** In the Dockerfile, you'll define the steps to build your image. For example:

   ```
   # Use an existing base image as the starting point
   FROM ubuntu:20.04

   # Set the working directory within the container
   WORKDIR /app

   # Copy application files from the host to the container
   COPY . .

   # Install necessary software or dependencies
   RUN apt-get update && apt-get install -y python3

   # Specify the command to run when the container starts
   CMD ["python3", "app.py"]

   ```

   In this example, the Dockerfile starts from an Ubuntu 20.04 base image, sets the working directory, copies the application files from the host to the container, installs Python 3, and specifies the command to run the application.

3. **Build the Docker Image:** Open a terminal and navigate to the directory containing the Dockerfile. Run the following command to build the image:

   ```bash
   docker build -t my-custom-image .
   ```

   This command tells Docker to build an image with the tag `my-custom-image` using the current directory as the build context.

4. **Use the Docker Image:** Once the image is built, you can create and run containers from it. For example:

   ```bash
   docker run -d my-custom-image
   ```

   This command creates a detached (background) container from the `my-custom-image` image.

By using Dockerfiles, you can automate the image creation process and ensure consistency across different environments. Dockerfiles are especially useful when collaborating on projects or when deploying applications to production, as they provide a repeatable and version-controlled way to build and distribute containerized applications.

### .dockerignore

The `.dockerignore` file is used to specify which files and directories should be excluded from being included in the Docker build context when building an image. This can help reduce the size of the build context and subsequently the size of the resulting image. The syntax and usage of `.dockerignore` is similar to that of `.gitignore` files.

Here's how to use `.dockerignore`:

1. **Create a `.dockerignore` File:** In your project directory, create a file named `.dockerignore`.
2. **Specify Excluded Files and Directories:** In the `.dockerignore` file, list the files, directories, or patterns you want to exclude from the Docker build context. For example:

   ```
   # Exclude editor and system files
   .git
   .vscode
   .idea
   *.swp

   # Exclude temporary and build files
   node_modules
   build
   dist

   ```

   In this example, the `.git` directory, editor-related files, temporary files, and build artifacts will be excluded from the build context.

3. **Build the Docker Image:** When you run the `docker build` command, Docker will use the contents of the current directory (excluding the files and directories listed in `.dockerignore`) as the build context. For instance:

   ```bash
   docker build -t my-custom-image .
   ```

   Docker will use the files and directories in the current directory as the build context, but any entries listed in `.dockerignore` will be excluded from consideration.

Using `.dockerignore` is important to ensure that only relevant files are included in the Docker build context, which can significantly reduce build times and image sizes. It's particularly useful for excluding files that are not required for the runtime environment of your application or files that could potentially introduce security risks or vulnerabilities into the image.

## Starting and Stopping Docker Containers

### Start Containers

Use the `docker run` command to start a container from an image. The basic syntax is:

```bash
docker run [options] IMAGE [COMMAND] [ARG...]
```

For example, to start a container from the "nginx" image and run it in the background (detached mode), you can use:

```bash
docker run -d nginx
```

This will start an Nginx web server container in the background.

### Stop Containers

To stop a running container, you use the `docker stop` command followed by the container's ID or name. The basic syntax is:

```bash
docker stop [OPTIONS] CONTAINER [CONTAINER...]
```

To stop a container with a specific name or ID, use:

```bash
docker stop container_name_or_id
```

For example, if you started the Nginx container as "my-nginx-container," you can stop it using:

```bash
docker stop my-nginx-container
```

If you want to stop all running containers, you can do so with:

```bash
docker stop $(docker ps -q)
```

This command uses `docker ps -q` to list the IDs of all running containers and stops them one by one.

Additionally, you can restart a stopped container using the `docker start` command followed by the container's ID or name:

```bash
docker start container_name_or_id
```

These commands allow you to manage the lifecycle of Docker containers efficiently, starting them when needed and stopping them when they are no longer required.

### Remove Components

```
docker rmi/rm [image/container] [image_id/container_id]
```
---
Refer [[Docker-Compose]] for automating the process

---
# Cheat Sheet

### Instructions

- .dockerignore
- [FROM](https://docs.docker.com/engine/reference/builder/#from) Sets the Base Image for subsequent instructions.
- [MAINTAINER (deprecated - use LABEL instead)](https://docs.docker.com/engine/reference/builder/#maintainer-deprecated) Set the Author field of the generated images.
- [RUN](https://docs.docker.com/engine/reference/builder/#run) execute any commands in a new layer on top of the current image and commit the results.
- [CMD](https://docs.docker.com/engine/reference/builder/#cmd) provide defaults for an executing container.
- [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) informs Docker that the container listens on the specified network ports at runtime. NOTE: does not actually make ports accessible.
- [ENV](https://docs.docker.com/engine/reference/builder/#env) sets environment variable.
- [ADD](https://docs.docker.com/engine/reference/builder/#add) copies new files, directories or remote file to container. Invalidates caches. Avoid `ADD` and use `COPY` instead.
- [COPY](https://docs.docker.com/engine/reference/builder/#copy) copies new files or directories to container. By default this copies as root regardless of the USER/WORKDIR settings. Use `--chown=<user>:<group>` to give ownership to another user/group. (Same for `ADD`.)
- [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#entrypoint) configures a container that will run as an executable.
- [VOLUME](https://docs.docker.com/engine/reference/builder/#volume) creates a mount point for externally mounted volumes or other containers.
- [USER](https://docs.docker.com/engine/reference/builder/#user) sets the user name for following RUN / CMD / ENTRYPOINT commands.
- [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) sets the working directory.
- [ARG](https://docs.docker.com/engine/reference/builder/#arg) defines a build-time variable.
- [ONBUILD](https://docs.docker.com/engine/reference/builder/#onbuild) adds a trigger instruction when the image is used as the base for another build.
- [STOPSIGNAL](https://docs.docker.com/engine/reference/builder/#stopsignal) sets the system call signal that will be sent to the container to exit.
- [LABEL](https://docs.docker.com/config/labels-custom-metadata/) apply key/value metadata to your images, containers, or daemons.
- [SHELL](https://docs.docker.com/engine/reference/builder/#shell) override default shell is used by docker to run commands.
- [HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) tells docker how to test a container to check that it is still working.


![[Pasted image 20231027102956.png]]