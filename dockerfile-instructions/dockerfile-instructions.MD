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

## CMD
