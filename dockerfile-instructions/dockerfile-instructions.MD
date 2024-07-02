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