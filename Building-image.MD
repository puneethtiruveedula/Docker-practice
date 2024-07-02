# Building a simple image using Dockerfile
To build our own Docker images, we need to instruct the Docker CLI on how to build it. To achieve this, we must create a specific file with all the instructions. It is called the **Dockerfile**. The Dockerfile is a text file with no extension of any sort. It is simply named “Dockerfile”.

Once we have this file created and set up, the docker CLI can then use this file as input to build images using the command ```docker build```. The images can then be used to create containers.

### Format of Dockerfile instructions
Each line in the Dockerfile build instruction will look something like this:
* ```INSTRUCTION <arguments>```