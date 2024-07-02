# Best Practices for Running Docker Containers with Non-Root Users

### Why run containers as non-root users?
Running containers as the root user poses several security risks. For example, if an attacker gains access to a container running as the root user, they can easily escalate their privileges to gain root access to the host machine. Additionally, running containers as the root user allows the container to modify files on the host machine.

### Creating a Dockerfile with a non-root user

```
# Use an official lightweight version of Ubuntu as the base image
FROM ubuntu:latest

# Install Apache
RUN apt-get update && apt-get install -y apache2

# Create a non-root user and group to run Apache with
RUN groupadd -r apache && \
    useradd -r -g apache apache

# Change ownership of directories that Apache needs to write to
RUN mkdir -p /var/run/apache2 /var/lock/apache2 /var/log/apache2
RUN chown -R apache:apache /var/run/apache2 /var/lock/apache2 /var/log/apache2 /var/www/html

# Copy files into /var/www/html
COPY . /var/www/html/

# Expose port 80
EXPOSE 80

# Start Apache as the non-root user we created
USER apache
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```

This Dockerfile creates a Docker image based on the latest lightweight version of Ubuntu that includes the Apache2 server. It also creates a non-root user and group to run the Apache server with, and sets the ownership of directories that Apache needs to write to.

This specifies that the base image for our Docker image will be the latest version of Ubuntu.

```
FROM ubuntu:latest
```

This updates the package repository and installs the `Apache2 server`. This creates a new system group called `apache` and a system user also called `apache` that belongs to the `apache group`.

```
RUN groupadd -r apache && \
    useradd -r -g apache apache
```

These two commands create three directories that Apache needs to write to and set the ownership of those directories and `/var/www/html` to the `apache user and group`.

```
RUN mkdir -p /var/run/apache2 /var/lock/apache2 /var/log/apache2
RUN chown -R apache:apache /var/run/apache2 /var/lock/apache2 /var/log/apache2 /var/www/html
```

The `-p` option tells `mkdir` to create the directories as needed, so it won’t throw an error if the directories already exist. Then, we’re running the chown command to give ownership of both directories to the myuser user.

This copies all the files in the current directory into the `/var/www/html` directory in the container.

```
COPY . /var/www/html/
```

This exposes port 80 so that we can access the Apache server running in the container.

```
EXPOSE 80
```

This sets the user that the container will run the Apache server as to apache and specifies the command that will be run to start the server when the container is launched.

```
USER apache
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```

### Using a non-root user in a Dockerfile provides several benefits:

**Improved security:** By default, a process running as root has full access to the system, which can be a security risk. Running a process as a non-root user with reduced privileges can limit the damage that can be done in the event of a security breach.

**Better isolation:** When running a container as a non-root user, the container is isolated from the host system and other containers, which reduces the attack surface.

**Avoiding file permission issues:** Running a container as a non-root user can help avoid file permission issues, as the user running the container will have the same permissions as the user who created the files.

Overall, using a non-root user in a Dockerfile can improve the security and isolation of your container, help meet compliance requirements, and avoid file permission issues.