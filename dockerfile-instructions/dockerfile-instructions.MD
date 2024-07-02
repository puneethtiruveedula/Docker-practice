# Dockerfile instructions in detail
Below we will discuss the most used Dockerfile instructions which will help you build a basic as well as an advance Dockerfile. Let's begin!

## FROM
We have mainly been talking about the FROM instruction, the syntax is as below:

```
FROM [--platform=<platform>] <image> [AS <name>]
```
```
From <image-name>:tag
```
This can be used multiple times in a Dockerfile and usually sets the build stage in the form of a base image for the next set of instructions until a new `FROM` instruction is issued. `AS` <name>is an optional argument and is used to provide an alias to this layer which can be referenced at a later point. The `--platform=<platform>` flag is optional and is used to define the platform of the image used in the `FROM` instruction in case a multi-platform image is targeted. Values for this flag can include `linux/amd64, linux/arm64, or windows/amd64` . `FROM` is usually the first instruction in a Dockerfile. However, only `ARG` instruction(explained later) can precede the `FROM` instruction at the beginning of a Dockerfile in certain scenarios and these two can work

## RUN
The `RUN` Docker instruction is used to execute commands during the image build process. It allows you to install packages, configure settings, and perform various actions within the container. Each `RUN` instruction creates a new intermediate layer in the image, capturing the changes made by the command. This instruction is crucial for setting up the environment and dependencies required for the application to run correctly. It's important to minimize the number of `RUN` instructions to optimize image size and layer caching.

The syntax for `RUN` instruction is below:

```
RUN <command>
```

The command will be executed when the instruction is run. You can also provide the command in the exec format as shown below

```
RUN ["executable", "param1", "param2"]
```

## CMD
The `CMD` instruction will run at the time of container creation. In a Dockerfile, there can be only one `CMD` instruction. If multiple `CMD` is provided, the last occurrence is the only instruction executed. It has to be noted that, the `CMD` command and parameters can be overridden when the container is run.

There are three ways to represent the `CMD` instruction. In the JSON array format, here the instruction will run the executable and pass the param1 and param2 to the executable. This is the JSON array or exec form of expressing the defaults.

```
CMD ["executable","param1","param2"]
```
The `CMD` can also be provided in a shell form. Here, the command is the executable and param1 and param2 are passed as the parameters to it.

```
CMD command param1 param2
```
There is a third way of providing default parameters to the executable but this works in hand with the ENTRYPOINT instruction (explained next).

```
CMD ["param1","param2"]
```
Here the executable is provided by the ENTRYPOINT instruction and CMD only provides the default parameters. In this method, both CMD and ENTRYPOINT instructions become mandatory in the Dockerfile.

## RUN vs CMD

> RUN - executes at build time means image creation time

> CMD - only executes at the time of container creation i.e docker run

## ENTRYPOINT

The ENTRYPOINT instruction in a Dockerfile specifies the command that should be executed when the container is started, just like CMD. However, unlike CMD, ENTRYPOINT is an executable command itself and it cannot be overridden or ignored even if user try to pass command line arguments.

There are two ways to specify the ENTRYPOINT instruction in a Dockerfile:

* Exec form: This specifies the command to be executed as an array of arguments, with the executable name as the first argument.

```
ENTRYPOINT ["executable", "arg1", "arg2"]
```

* Shell form specifies the command to be executed as a single string, with the command and arguments separated by spaces.

```
ENTRYPOINT command arg1 arg2
```

## Differences between CMD & ENTRYPOINT
These are used to specify programs that execute when a container starts, but the major differences between the two are - CMD commands are ignored by Daemon when there are parameters stated within the docker run command while ENTRYPOINT instructions are not ignored but instead are appended as command line parameters by treating those as arguments of the command.

#### Execute the following command to override the ENTRYPOINT

```
docker run --entrypoint “command-to-override” image-name
```

### ENTRYPOINT with CMD
Usually it’s a good practice to use ENTRYPOINT for the main command and CMD for optional arguments. This allows users to customize the container’s behavior by passing additional arguments, while ensuring that the main command is always executed.

```
FROM ubuntu
ENTRYPOINT [“echo”, “Hello”]
CMD ["World"]
```
When you state a command in docker run it will override CMD only and gets appended .

## WORKDIR
The `WORKDIR` Docker instruction is used to set the working directory for any subsequent instructions within a Dockerfile. By specifying a directory path as an argument to WORKDIR, you define the location where commands like `RUN`, `COPY`, and `ADD` will be executed. If the directory doesn't exist, `WORKDIR` will create it.This instruction is valuable for improving code readability, maintaining a structured file hierarchy, and ensuring that container operations are conducted in the intended directory, enhancing Dockerfile clarity and maintainability.

The syntax is very simple:

```
WORKDIR /path/to/workdir
```

If you run multiple `WORKDIR` instructions, the final working directory will be the result of every `WORKDIR` instruction combined.

```
WORKDIR /dir1
WORKDIR dir2
WORKDIR dir3
ENTRYPOINT [ "pwd" ]
```

the output of the pwd command will be /dir1/dir2/dir3

## COPY
The `COPY` Docker instruction facilitates the copying of files and directories from the host machine into a Docker image during the build process. It requires two arguments: the source path (relative to the context of the build) and the destination path within the image. This instruction is useful for incorporating application code, configuration files, and other assets into the image. `COPY` solely deals with file system content and doesn't perform URL retrieval or unpacking. It helps maintain a more straightforward and predictable image construction process, enhancing Dockerfile clarity and reproducibility.

The syntax is simple:

```
COPY <src>... <dest>
```

Multiple source paths can be given and they all work as relative paths under the build context.

If the paths have whitespaces, an alternative method can be used :

```
COPY ["<src>",... "<dest>"]
```

wildcard characters like * and ? can be used in the source paths to match the file names.

```
COPY resource-* /resources/
COPY resource-x? /resources/
```

If a `WORKDIR` instruction is been added before the `COPY` instruction, the destination path will be relative to the `WORKDIR` directory.

## ADD
The `ADD` Docker instruction serves to incorporate files and directories from the build context or remote URLs into a Docker image during construction. It functions similarly to the `COPY` instruction but includes additional capabilities like unpacking compressed files and fetching remote resources. While versatile, caution should be exercised with `ADD` as it might lead to unexpected behaviours when dealing with URLs. For simpler file copying, `COPY` is generally preferred. Utilize `ADD` when the advanced features it offers are necessary, such as when dealing with compressed archives or web content retrieval.

The syntax for `ADD`is similar to `COPY` and follows similar use of wildcards. Additionally, remote paths like Git repositories and archive files can be provided as the source path.

```
ADD ["<src>",... "<dest>"]
```

## COPY vs ADD
`COPY` and `ADD` both are to copy the files from workspace to docker image.
but `ADD` have two extra advantages
1. it can directly download the content from internet into the image
2. it can directly untart the files into the image.

## What is Docker Expose?
The `EXPOSE` instruction in a Dockerfile lets Docker know which network ports the container listens on at runtime.

However, it doesn’t directly make the container accessible from outside the Docker host or publish those ports to the host system. Instead, it serves as a sort of note, detailing which ports the container is set to listen on for incoming connections.

For example, if you include `EXPOSE 80` in a Dockefile, it indicates that the containerized application listens on `port 80` for incoming connections.

This information is handy for developers or administrators trying to understand the networking needs of the containerized application.


## What is Docker Publish?
The docker run command with the `-p or — publish flag` allows you to publish ports from the container to the host system, making them accessible from the external world.

When you publish a port using the `-p`flag, Docker creates a mapping between a port on the host system and a port on the container.

For instance, running `docker run -p 8080:80 <image name> `would publish `port 80` from the container to `port 8080` on the host system. So, any traffic directed to `port 8080` on the host will be routed to `port 80` on the container.

## Example and Use case :
Consider a scenario where you have a web application running in a Docker container that listens on `port 80`.To expose this port using the `EXPOSE` instruction in the Dockerfile, you would add the following line:

```
EXPOSE 80
```
This informs Docker that the containerized web application listens on `port 80`.

Now, when running the container using the docker run command, you can publish `port 80` to a port on the host system, say port 80, using the `-p`flag:

```
docker run -p 8080:80 <image name>
```

This command maps `port 80` in the container to `port 8080` on the host system, allowing external access to the web application.

The below command starts a container based on the specified image in detached mode (-d) and _publishes all exposed ports_ to random ports on the host system using the` — publish-all option`.

For example, if your Dockerfile contains `EXPOSE` instructions for `ports 80` and `443`, running the below command will automatically map `ports 80`and `443`  from the container to random ports on the host system.

```
docker run -d — publish-all <image name>
```
### Key Differences :

### Expose:
> _Used within the Dockerfile to specify which ports the container listens on._

>_Serves as a kind of heads-up and doesn’t directly open ports to the host system._

>_Helpful for communicating networking needs to colleagues or admins._

### Publish:
> _Employed with the docker run command to expose container ports to the host system._

>_Creates a bridge between ports on the host and container, enabling access from the outside._

>_Essential for making containerized applications reachable from beyond the Docker host._