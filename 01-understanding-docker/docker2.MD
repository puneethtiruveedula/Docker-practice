# What is Docker?
Docker is a platform designed to make it easier to create, deploy, and run applications by using containers. Containers allow developers to package an application with all the parts it needs, such as libraries and dependencies, and ship it all out as one package. This guarantees that the application will run on any other machine that has Docker installed, regardless of any customized settings that machine might have.

# Core Concepts in Docker

## Docker Images
Docker images are the foundation of Docker containers. They are read-only templates that contain the application code, libraries, dependencies, and configuration needed to run the application. Images are built from a series of layers, each representing an instruction in the image’s Dockerfile. These layers are stacked on top of each other, and when combined, they form the complete image.

One of the key benefits of Docker images is their portability. Once an image is built, it can be shared and run on any machine that has Docker installed, regardless of the underlying operating system or infrastructure. This ensures consistency across different environments and eliminates the “it works on my machine” problem.

Docker images are stored in image registries, such as Docker Hub or private registries within an organization. These registries allow developers to share, collaborate, and distribute their images easily. Images can be pulled from a registry, and containers can be created from those images.

Images are identified by a unique tag, which typically includes the image name and version. This allows you to manage different versions of an image and ensures that the correct image is used when creating containers.

When a container is created from an image, a new writable layer is added on top of the image’s read-only layers. This writable layer allows the container to make changes and store data specific to that container instance. However, any changes made within the container are isolated and do not affect the underlying image.

Now, let’s take a look at an example of creating a Docker image using a Dockerfile. Note that this is only an example contains the Dockerfile and it needs also the resources it refer to (e.g., `requirements.txtfile`). Later I will provide a full working example.

#### Dockerfile:

```
# Use an official Python runtime as a parent image
FROM python:3.8-slim

# Set the working directory in the container
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```
In this Dockerfile:

* `FROM` specifies the base image.

* `WORKDIR` sets the working directory.

* `COPY` copies files from the host to the container.

* `RUN` executes commands in the container.

* `EXPOSE` opens a port.

* `ENV` sets environment variables.

* `CMD` specifies the command to run.

By understanding Docker images and how they are created using Dockerfiles, you can package your applications in a portable and reproducible manner, making it easier to deploy and run them across different environments.

## Docker Containers
Docker containers are the runnable instances of Docker images. They encapsulate the application code, dependencies, and runtime environment, providing an isolated and portable unit of software. Containers are lightweight and fast, allowing you to run multiple instances of an application on the same host without conflicts.

When you run a Docker container, you essentially create a new writable layer on top of the read-only layers of the image. This writable layer allows the container to make changes and store data specific to that container instance. However, any changes made within the container are isolated and do not affect the underlying image or other containers.

Containers are managed by the Docker runtime, which handles the creation, execution, and teardown of containers. Each container has its own isolated filesystem, network stack, and process space, ensuring that containers are independent of each other and the host system.

One of the key benefits of Docker containers is their portability. Since containers encapsulate all the necessary components, they can be easily moved between different environments, such as development, testing, and production, without modification. This enables consistent behavior and eliminates the need for environment-specific configurations.

Containers also provide a high level of resource efficiency. They share the host machine’s kernel and resources, while maintaining isolation through namespace and control group mechanisms. This allows you to run many containers on a single host, maximizing resource utilization and reducing overhead.

Now, let’s look at an example of running a Docker container. To run a container from the image we created in the previous example:

```
docker build -t my-python-app .
docker run -p 4000:80 my-python-app
```

* `docker build -t my-python-app .` builds an image from the Dockerfile in the current directory.

* `docker run -p 4000:80 my-python-app` runs the container, mapping port 4000 on the host to port 80 in the container.

When you run the `docker run` command, Docker creates a new container from the specified image and starts it. The `-p` flag maps a port from the host to the container, allowing external access to the application running inside the container.

You can manage the lifecycle of containers using various Docker commands:

* `docker start` starts a stopped container.

* `docker stop` stops a running container.

* `docker restart` restarts a container.

* `docker rm` removes a stopped container.

* `docker ps` lists running containers.

* `docker logs` retrieves the logs of a container.

* `docker exec` executes a command within a running container.

By leveraging Docker containers, you can package your applications along with their dependencies, making them portable and easy to deploy. Containers provide isolation, consistency, and efficiency, enabling you to build, ship, and run applications seamlessly across different environments.

Understanding the relationship between Docker images and containers is crucial. Images are the static, read-only templates that define the application and its dependencies, while containers are the running instances of those images. Images are built once and can be used to create multiple containers, each with its own isolated environment.

Docker containers have revolutionized the way applications are packaged, shipped, and run, providing a consistent and efficient platform for software development and deployment.

## Dockerfile
A Dockerfile is a text file that contains a set of instructions used to automate the process of building a Docker image. It serves as a blueprint for creating an image that includes all the necessary components, such as the application code, dependencies, and runtime environment.

Dockerfiles follow a specific format and use a declarative syntax to define the steps required to build an image. Each instruction in a Dockerfile creates a new layer in the image, allowing for efficient building and sharing of images.

Here are some commonly used Dockerfile instructions:

* `FROM`: Specifies the base image to start building from. It is usually the first instruction in a Dockerfile.

* `COPY`: Copies files and directories from the host system into the image.

* `ADD`: Similar to COPY, but can also handle remote URLs and extract compressed files.

* `RUN`: Executes commands during the image build process, such as installing dependencies or running scripts.

* `CMD`: Specifies the default command to run when a container is started from the image.

* `ENTRYPOINT`: Configures the container to run as an executable, allowing for parameterized commands.

* `ENV`: Sets environment variables within the image.

* `EXPOSE`: Exposes a port that the container will listen on at runtime.

* `WORKDIR`: Sets the working directory for subsequent instructions in the Dockerfile.

* `USER`: Sets the user and group for running subsequent instructions and the container.

* `LABEL`: Adds metadata to the image in the form of key-value pairs.

Now, let’s describe a new Dockerfile example and explain its components in more detail. This example is a full working one containing all three file described.

Assume the following structure:

```
.
├── Dockerfile
├── app.py
├── requirements.txt
```

#### ***Dockerfile***

```
# Build Stage
FROM python:3.9-slim AS build

# Set the working directory
WORKDIR /app

# Copy the requirements file
COPY requirements.txt .

# Copy the application code
COPY . .

# Run any build commands if necessary
# RUN flask build

# Final Stage
FROM python:3.9-slim

# Set the working directory
WORKDIR /app

# Copy the built artifacts from the build stage
COPY --from=build /app /app

# Install build dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Expose the port on which the Flask app will run
EXPOSE 5000

# Set the command to run the Flask application with Gunicorn
CMD ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]
```

#### In this advanced Dockerfile:

1. For demonstration, we use here a multi-stage build which usually is used to optimize the final image size. Multi-stage builds allow us to use multiple `FROM` instructions in a single Dockerfile, each representing a separate stage.

2. The first stage, named `build`, starts from the `python:3.9-slim` image. This stage is responsible for building the application and installing build dependencies.

3. We set the working directory to `/app` using the `WORKDIR` instruction.

4. We copy the `requirements.txt` file into the image using the COPY instruction. This file typically contains the Python dependencies required for the application.

5. We copy the entire application code into the image using another `COPY` instruction.

6. If the application requires any build steps, such as compiling assets or generating files, we can uncomment the `RUN flask build` instruction and modify it according to the specific build requirements of the Flask application.

7. The second stage starts from a fresh `python:3.9-slim` image. This stage is responsible for running the application in a production environment.

8. We set the working directory to `/app` using the WORKDIR instruction.

9. We use the COPY `--from=build` instruction to copy the built artifacts from the `/app` directory in the build stage into the `/app` directory in the final stage. This ensures that only the necessary files are included in the final image.

10. We run `pip install` to install the build dependencies specified in the `requirements.txt` file using the `RUN` instruction. The `--no-cache-di`r flag ensures that the package cache is not stored, reducing the image size.

11. We expose port 5000 using the `EXPOSE` instruction, which is the default port for Flask applications.

12. Finally, we specify the command to run when the container starts using the `CMD` instruction. It uses Gunicorn to run the Flask application, binding it to `0.0.0.0:5000`. The `app:app` syntax assumes that the Flask application is defined in a file named `app.py` with the application instance named `app`.

**app.py**

```
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

**requirements.txt**

```
flask
gunicorn
```

#### To build and run the Docker container, follow these steps:

1. Put all three files (`requirements.txt, app.py, and Dockerfile`) in the same directory.

2. Open a terminal and navigate to the directory containing the `app.py` file and the `Dockerfile`.

3. Build the Docker image by running the following command:

```
docker build -t flask-app .
```

4. Once the image is built, run the container using the following command:

```
docker run -p 5000:5000 flask-app
```

By using a multi-stage build, we separate the build dependencies and the runtime dependencies, resulting in a smaller final image size. The build stage installs the necessary dependencies and performs any build steps, while the final stage only includes the runtime dependencies and the built artifacts required to run the Flask application.

This approach optimizes the image size and improves the efficiency of the Docker build process by leveraging the benefits of multi-stage builds.

Dockerfiles provide a declarative and version-controlled way to define the steps required to build an image. They ensure reproducibility and make it easy to automate the image-building process.

When you run the `docker build` command and specify a Dockerfile, Docker reads the instructions in the Dockerfile and executes them in the specified order. Each instruction creates a new layer in the image, and the final result is a Docker image that can be used to create containers.

Dockerfiles are a fundamental component of the Docker ecosystem, enabling developers to define and share the exact steps needed to build an image. By version-controlling Dockerfiles, you can track changes, collaborate with others, and ensure consistency in the image-building process.

Mastering Dockerfiles is crucial for creating efficient, reproducible, and maintainable Docker images. They provide the foundation for packaging applications and their dependencies, making it easier to deploy and run applications across different environments.

### Other Useful Docker Terms

1. **Docker Hub:** Docker Hub is a cloud-based registry service that allows you to link to code repositories, build your images, and test them. It provides a centralized resource for container image discovery, distribution, and change management, user and team collaboration, and workflow automation throughout the development pipeline.

2. **Docker Compose:** Docker Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration.

3. **Docker Swarm:** Docker Swarm is a native clustering and orchestration tool for Docker containers. It allows you to manage a cluster of Docker nodes as a single virtual system. Swarm uses the standard Docker API, which means any tool that already communicates with a Docker daemon can use Swarm to scale to multiple hosts.

4. **Docker Volumes:** Volumes are the preferred mechanism for persisting data generated by and used by Docker containers. They provide a way to store data outside of the container’s writable layer, making the data independent of the container’s life cycle.

5. **Docker Network:** Docker networking allows you to connect Docker containers together, on a single host or across multiple hosts. You can create bridge networks, overlay networks, and host networks, each serving a different purpose in Docker container communication.

### Useful Docker Commands
Below are just a few of the commonly used Docker commands. Docker provides a wide range of commands and options to manage images, containers, networks, volumes, and more. You can refer to the official Docker documentation for a comprehensive list of commands and their usage.

Remember to replace `<image_name>, <container_id>`, and other placeholders with the actual values specific to your Docker setup.

### Building Images

```
docker build -t <image_name> .
```
Builds a Docker image from a Dockerfile in the current directory and tags it with the specified `<image_name>.`

### Running Containers

```
docker run -p <host_port>:<container_port> <image_name>
```
Runs a Docker container based on the specified `<image_name>`, mapping the `<container_port> to the <host_port>` on the host machine.

### Listing Containers

```
docker ps
```

Lists all running containers.

```
docker ps -a
```

Lists all containers, including stopped ones.

### Stopping Containers

```
docker stop <container_id>
```
Stops a running container with the specified `<container_id>`.

### Removing Containers

```
docker rm <container_id>
```
Removes a stopped container with the specified `<container_id>`.

### Listing Images

```
docker images
```

Lists all Docker images on your local machine.

### Removing Images

```
docker rmi <image_id>
```
Removes a Docker image with the specified `<image_id>`.

### Accessing Container Logs

```
docker logs <container_id>
```
Retrieves the logs of a container with the specified `<container_id>`.

### Executing Commands in a Running Container

```
docker exec -it <container_id> <command>
```
Executes a command inside a running container with the specified `<container_id>`. The `-it` flags allow for an interactive terminal session.

### Monitoring Container Resources

```
docker stats
``` 

Displays a live stream of container resource usage statistics, including CPU, memory, and network usage.

