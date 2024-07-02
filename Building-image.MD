# Building a simple image using Dockerfile
To build our own Docker images, we need to instruct the Docker CLI on how to build it. To achieve this, we must create a specific file with all the instructions. It is called the **Dockerfile**. The Dockerfile is a text file with no extension of any sort. It is simply named “Dockerfile”.

Once we have this file created and set up, the docker CLI can then use this file as input to build images using the command ```docker build```. The images can then be used to create containers.

### Format of Dockerfile instructions
Each line in the Dockerfile build instruction will look something like this:

 ```
 INSTRUCTION <arguments>
 ```

The instruction is not case-sensitive and contains the Build command. The arguments will follow this instruction. Example ```FROM alpine```

Every Dockerfile begins with a `FROM` instruction barring some exceptions which we will discuss later.

### Adding comments in the Dockerfile
You can also add comments to the file using a `#` symbol. Example of a comment `#This is a comment`

Comments are removed from the execution during a preprocessing step before the builder instructions are executed.

### Example 1: A simple Dockerfile looks something like this:

```
#This is the Hello World Dockerfile
FROM alpine`
CMD ["echo", "Hello World"]
```