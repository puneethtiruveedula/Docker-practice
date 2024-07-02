## ARG
`ARG` (Build-time Variables) is used to define build-time variables. Build-time variables are used during the build process and are not available in the final image. These variables can be used to customize the build process.

If the `ARG` is declared before the `FROM`, this parameter can be used in the `FROM `instruction. However, it can not be used after the `FROM`. To use it after the `FROM`, redeclare again but without any initialization value.

we can add an argument step in the Dockerfile like

```
ARG VERSION=0.1
```

we can use `ARG` command to set the build argument in the docker file and we can access it using the `$` or `${}` syntax.

```
COPY $VERSION .
OR
COPY ${VERSION} .
```

It will copy the 0.1 version from the host machine to the docker image at the defined `WORKDIR.`

It is not mandatory to give the default value of the build-argument, we can just declare it as well as shown below.

```ARG VERSION```

and can pass the value in the docker build command.

### Defining build argument in the docker build command
We can override the default value of the ARG in the build command using the `--build-arg` command.

```
docker build -t hello-world:latest --build-arg version= 0.2 .
```

So it will copy the 0.2 version from the host file to the docker image.

### Defining default values

```
ARG version
FROM almalinux:${version:-8}
```

> If we don't declare `ARG` value in build in command then it takes default value.

> You can always have default values to arg and override if required.

### Defining multiple docker arguments
In Dockerfile, we can define multiple build arguments with multiple `ARG` commands like

```
ARG VERSION
ARG PORT
```
and in docker build command we have to use multiple `— build-arg`

```
docker build -t hello-world:latest --build-arg VERSION=0.2 --build-arg PORT=80 .
```
### Using Build Arguments in the docker-compose file.
We can declare the build arguments in the Dockerfile and can set the value in the docker-compose file.

Adding build-arguments in the Dockerfile

```
ARG VERSION=0.1
ARG PORT=8080
```
and can use it in the docker-compose file.

```
build:
  context: .
  args:
    VERSION: 1
    PORT: 9090
```
docker-compose dynamically adds or updates the value of the build arguments declared in the Dockerfile. We can omit the values in the docker-compose file in that case it will pick the value present in the environment in which the docker-compose is running.

## ENV
`ENV` (Run-time Variables)  is used to define run-time variables. Run-time variables are set in the final image and can be accessed during the container runtime. These variables can be used to configure the container environment, like setting environment variables, defining working directory, and setting user.

### Defining environment variables in Dockerfile
we can directly define the environment variables in the Dockerfile
```
ENV PORT_NUMBER=8080
```

We can access the environment variable in the Dockerfile with `$` or `${}` syntax, as we used for the build arguments

```
EXPOSE $PORT_NUMBER
OR 
EXPOSE ${PORT_NUMBER}
```

we cannot override the default value of the environment variable defined in the Dockerfile at the build phase directly using the “-e” parameter but we can use build arguments to do that.

We can define both the build-argument and environment variable in the Dockerfile and use the build-arguments to dynamically set the value of environment variables in the build phase.

```
ARG PORT
ENV PORT_NUMBER=$PORT
```

The value of the build argument will determine the default value of the environment variable.

### Defining environment variable in the docker run command
The other way to define the environment variable is to pass it in the docker run command using `--env` tag or `-e` tag.

```
 docker run --env PORT_NUMBER=8080 <image-name>
OR
 docker run -e PORT_NUMBER=8080 <image-name>
```

If the PORT_NUMBER environment variable is already defined in the Dockerfile then it will override it.

### Defining multiple environment variables
We can define multiple environment variables in the Dockerfile like

```
ENV PORT_NUMBER=8080
ENV HOST_NAME=localhost
ENV DB_NAME=MySQL
```

Similarly, we can define multiple environment variables in the docker run command like

```
docker run -e PORT_NUMBER=8080 -e HOST_NAME=localhost -e DB_NAME= MySQL <image-name>
```

but as we can see, the more the number of environment variables the readability of the command decreases. So, in this scenario, we can use the environment variable file.

### Environment variable file
It is used to separate the Dockerfile and environment variables, it gives us the flexibility to use different environment variables for running different containers using the same Dockerfile.

We can create a file with any name you want (best practice is to create with `.env` name) at any location (best practice is to create at the Dockerfile level). In that file, we can directly place all our environment variable like:

```
PORT_NUMBER=8080
HOST_NAME=localhost
DB_NAME=MySQL
```
We can use this environment variable file in the docker run command like:

```
docker run --env-file ./.env <IMAGE-NAME>
```

### Environment variables in the docker-compose file
Environment variables can be used in the docker-compose file in several ways. The most common way is to add the environment variables in the file `(.env)`, docker-compose file directly reads for it. The file must be present at the same level as the docker-compose file.

`.env file`

```
TAG: 7.3
```

docker-compose file

```
version: '3'
services:
  web:
    image: 'webapp:${TAG}'
```

But, we can have multiple environments like prod, dev, test, etc. In that case, we need to build different files. In that scenario we have to use `“--env-file”` with the `“docker-compose up”` command to define the location of the environment file like

```
docker-compose --env-file ./config/.env.dev up 
docker-compose --env-file ./config/.env.prod up 
docker-compose --env-file ./config/.env.test up
```

Each command is referring to a different environment file.

We can pass the environment variables to containers in docker-compose like we use to do it in the `“docker run”` command using `“-e”` and `“--env-file”` option

```
version:3
services:
  node:
    environment:
      - PORT: 9090
and

version:3
services:
  node:
    env_file:
      - /config/.env.test
```

We can have more than one environment file for the same service in the Dockerfile.

```
version:3
services:
  node:
    env_file:
      - /config/.env.test1 
      - /config/.env.test2
```

We can pass the environment variable at the “docker-compose up” using the “-e” option

```
version:3
services:
  node:
    environment:
      - PORT
```

docker-compose run -e PORT=9090 node
Here, ‘node’ is one of the services that we are running using the docker-compose.

We can define the multiple services in the same command (let there is another service with the name java).

```
docker-compose run -e PORT=9090 node java
```

But there is one condition to use this command for running multiple services is that both cannot have the same environment variable defined in the docker-compose file.

It is a limitation, if two containers have the same environment variable then we cannot run both the containers using the “docker-compose run -e …” command as it cannot populate the environment variable of both the services simultaneously.

## Sample Dockerfile using both Build arguments and Environment variable

```
FROM node:alpine
ARG APP_DIR
WORKDIR $APP_DIR
COPY package.json .
RUN npm install
COPY . .
ENV PORT_NUMBER=80
EXPOSE PORT $PORT_NUMBER
CMD ["npm", "install"]
```

Here we are populating the working directory inside the docker image using the build arguments and exposing the port number using the environment variable (80 is the default port number that shall get exposed in case no value is provided in the ‘docker run’ command).

## Difference between Build Arguments and Environment variables

* Build arguments and the environment variable both provides a level of flexibility but at different phases. We cannot replace one with another, in fact, we can use them together to get higher flexibility.
* Build arguments works on the BUILD phase, it has no impact on the RUN phase of docker whereas the ENV can be used at both phases. Environment variables declared in Dockerfile are available at both the phases but environment variables declared in the environment file or passed in the RUN command are available at only the RUN phase.
* Build arguments cannot be used in the application code to be containerized, but we can use environment variables in the application code.

## ENV vs ARG
1. ENV variables can be accessed both at the build time and containers.
2. ARG instruction can only be accessed at the time of build or image.

