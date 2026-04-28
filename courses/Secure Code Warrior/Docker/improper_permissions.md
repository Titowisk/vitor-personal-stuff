Introduction
In the era of containerization, Docker and Kubernetes have become central players. However, with their versatile configurations comes potential security pitfalls. One significant vulnerability that can be easily overlooked is improper permissions. Ensuring stringent and appropriate permissions is paramount to avoid potential security breaches.

Description
The adoption of containers has paved the way for faster, more efficient deployments, and scalable applications. However, the flexibility of configurations also introduces several potential weak points. One of the most subtle yet dangerous is the misconfiguration of permissions. In the digital world, permissions act as gatekeepers, determining what actions are allowed and by whom. While these container platforms provide extensive configurations to fine-tune these permissions, their default settings might not always align with the best security practices. Improper permissions can lead to scenarios where malicious actors exploit these vulnerabilities to gain unauthorized access, manipulate application behavior, or escalate privileges, putting the entire system at risk. Recognizing and rectifying these misconfigurations is imperative to maintain the integrity and security of containerized environments.

Mitigation Techniques
Avoid running as root
One of the primary rules in container security is to avoid running container processes as the root user. By default, many container images are set to run as root. This can pose a significant risk because if an attacker gains control over a container running as root, they can potentially compromise the host system as well.

Avoid privileged containers
A privileged container is essentially a container that has almost all the capabilities of its host machine. Running containers in privileged mode breaks the isolation between the container and its host, making it a potential security risk.

Avoid the use of writable filesystems
Containers should be treated as immutable entities, meaning once they're spun up, their filesystems should not change. If a container uses a writable filesystem, it could be modified during its runtime, potentially leading to various security vulnerabilities.

Docker
Microservices that run as root in containers pose a significant threat. If compromised, attackers can gain full container privileges. While some default protections remain (like Linux capabilities and AppArmor or SELinux profiles), running as root eliminates an essential protection layer. This not only broadens the attack surface but also contradicts the principle of least privilege. Using privileged containers is akin to running without any container, risking the host and other containers.

Let's explore some recommendations:

Use non-root user: By default, Docker runs container processes as root within the container. To run as a non-root user, you can use a pre-existing image that already includes a non-root user, or you can configure one yourself. Many popular Docker images, like those based on Alpine Linux, already come with a non-root user named nobody. Before creating a new user, it's worth checking if one already exists that you can use. Here's how you can set this up:

# Create a group and user
`RUN groupadd -r myuser && useradd -r -g myuser myuser`

# Use the created user to run the application
USER myuser
If you're using docker-compose, you can specify the user in the compose file itself:

```
services:
your-service:
  image: your-image
  user: "myuser"
```
Avoid using --privileged flag: When running containers, avoid using the --privileged flag as it provides all capabilities to the container, making it almost equivalent to running the process directly on the host machine.

Set file/folder permissions: Ensure that the files and folders that your application needs to access are owned by the user under which the application runs, and they have the appropriate permissions. This avoids the need for your application to run as root just to access certain files or directories.

Set specific capabilities: If your service needs specific capabilities (e.g., binding to a low port), instead of running the container as root or using --privileged, you can grant only the required capabilities using the --cap-add and --cap-drop flags:

`docker run --cap-add=NET_BIND_SERVICE --cap-drop=ALL your-image`

Insecure Code Example
The Dockerfile below will create an image based on Ubuntu 20.04 and start a bash shell for a non-privileged user when you run the container.

```
# Use the official Ubuntu 20.04 base image
FROM ubuntu:20.04

# Update and install any required packages

# Set the default command to execute when starting the container
CMD [ "bash" ]
```

The Dockerfile sets the default command to start a bash shell when the container is initiated. However, it does not explicitly create or switch to a non-privileged user. Running containers as the root user poses significant security risks, as it can give attackers the potential to gain full privileges within the container.

Secure Code Example
The Dockerfile below is designed to create an image based on Ubuntu 20.04 and start a Bash shell for running commands when the container starts.

```
# Use the official Ubuntu 20.04 base image
FROM ubuntu:20.04

# Update and install any required packages

# Create a non-privileged user
RUN useradd -m myuser

# Switch to the non-privileged user for subsequent commands
USER myuser

# Set the user's home directory as the working directory
WORKDIR /home/myuser

# Set the default command to execute when starting the container
CMD [ "bash" ]
```

The Dockerfile sets the default command to start a bash shell when the container is initiated and takes additional steps to enhance security. Specifically, it creates a non-privileged user named myuser and switches to this user for subsequent commands. This ensures that the container operates with limited permissions, reducing the risk of potential attacks that exploit containers running as root. Setting the user's home directory as the working directory further confines operations to this restricted environment.

References & Resources
Docker:
OWASP Docker Top 10 - D01: Secure User Mapping
OWASP Cheat Sheet Series - Docker Security
Docker Security
Kubernetes:
OWASP Kubernetes Top 10 2022 - K01: Insecure Workload Configurations
OWASP Cheat Sheet Series - Kubernetes Security
Kubernetes Security